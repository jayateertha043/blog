---
title: "Intro to Fuzzing IoT Protocols using BooFuzz"
author: "Jayateertha Guruprasad"
date: 2024-01-17T12:16:22.412Z
lastmod: 2024-08-31T13:12:39+05:30

description: ""

subtitle: ""

image: "/post/img/2024-01-17_intro-to-fuzzing-iot-protocols-using-boofuzz_1.png" 
images:
 - "/post/img/2024-01-17_intro-to-fuzzing-iot-protocols-using-boofuzz_0.png"
 - "/post/img/2024-01-17_intro-to-fuzzing-iot-protocols-using-boofuzz_1.png"
 - "/post/img/2024-01-17_intro-to-fuzzing-iot-protocols-using-boofuzz_2.png"


aliases:
- "/intro-to-fuzzing-iot-protocols-using-boofuzz-02bab66454a7"

---

In this article let’s learn about how to fuzz IoT protocols such as modbus using boofuzz, a open source fuzzing framework which can fuzz most of IoT protocols using TCP, UDP, SSL or even Serial protocols.

**Modbus TCP has following format**—

1. Transaction ID (2 Bytes)
2. Protocol ID (2 Bytes) — Protocol ID is always 0000 as per modbus protocol definition
3. Length (2 Bytes) — Value is calculated by amount of bytes from Unit ID to Function Dependent Data
4. Unit ID/Device ID (1 Byte)
5. Function Code (1 Byte)
6. Function Dependent Data (No of Bytes depends on Function Code Used, for example For Reading Coils from Memory it’s usually 4 bytes)

> For detailed description of modbus protocol format refer [here](https://ipc2u.com/articles/knowledge-base/detailed-description-of-the-modbus-tcp-protocol-with-command-examples/).

**Sample Modbus TCP Packet from**[**PCAP file**](https://github.com/jayateertha043/ModbusSamples/blob/main/ModbusReadCoil.pcapng)**for Reading Coil from Memory (0001 0000 0006 00 01 0000 0001)—**

1. 0001 is Transaction ID
2. 0000 is Protocol ID
3. 0006 is Length
4. 00 is Unit ID
5. 01 is Function Code for Read Coil from Memory
6. 0000 is Base address to Read Value From
7. 0001 is Number of Coils to Read from Base Address

> All major Modbus functions in PCAP file can be grabbed from [here](https://github.com/ITI/ICS-Security-Tools/blob/master/pcaps/bro/modbus/modbus.pcap) for analysis, if required.

We need a simulator first before fuzzing, [**pymodbus**](https://github.com/pymodbus-dev/pymodbus) can be used for simulating modbus server(slave).

Install pymodbus using —

```
pip install pymodbuspip install pymodbus[serial]
```

Create a file ModbusServer.py with the below content and run **python ModbusServer.py** to start a simple **Modbus Slave/Server Simulator** —


{{<highlight python>}}
import random
import logging
from pymodbus.datastore import ModbusSequentialDataBlock, ModbusSlaveContext, ModbusServerContext
from pymodbus.server.async_io import StartTcpServer

# Enable logging (makes it easier to debug if something goes wrong)
logging.basicConfig()
log = logging.getLogger()
log.setLevel(logging.DEBUG)

# Define the Modbus registers
coils = ModbusSequentialDataBlock(0, [False] * 100)
discrete_inputs = ModbusSequentialDataBlock(0, [False] * 100)
holding_registers = ModbusSequentialDataBlock(0, [0] * 100)
input_registers = ModbusSequentialDataBlock(0, [0] * 100)

# Set Random Values for Coils and Holding Registers
coil_values = [random.randint(0, 1) for _ in range(100)] # coils are always boolean
holding_register_values = [random.randint(28, 48) for _ in range(100)]
coils.setValues(1,coil_values)
holding_registers.setValues(1, holding_register_values)
print("Coil Values:", coil_values)

# Define the Modbus slave context
slave_context = ModbusSlaveContext(
    di=discrete_inputs,
    co=coils,
    hr=holding_registers,
    ir=input_registers
)

# Define the Modbus server context
server_context = ModbusServerContext(slaves=slave_context, single=True)

# Start the Modbus TCP server
StartTcpServer(context=server_context, address=("localhost", 502))

{{</highlight>}}

To test if server is working properly you can use your wireshark with modbus filter applied & run below Modbus Master/Client Simulator script, after saving with file name ModbusClient.py and running **python ModbusClient.py**.

{{<highlight python>}}
from pymodbus.client.tcp import ModbusTcpClient

# Connect to the Modbus TCP server
client = ModbusTcpClient('localhost', port=502)

# Read the values from the Modbus registers
coils = client.read_coils(address=0, count=8,slave=0)

# Should check for errors here... i.e.
if coils.isError():
    print('Error getting coils: {coils}')
    raise Exception('Error getting coils: {coils}') # trying to display coils.bits would fail

# Print the values
print("Coils:", coils.bits)

# Close the Modbus TCP client
client.close()
{{</highlight>}}

![](/post/img/2024-01-17_intro-to-fuzzing-iot-protocols-using-boofuzz_0.png#layoutTextWidth)

This should give similar output to that of [PCAP file](https://github.com/jayateertha043/ModbusSamples/blob/main/ModbusReadCoil.pcapng).

**Now, let’s get started with actual fuzzing part by installing boofuzz.**

```
pip install boofuzz
```

> Although boofuzz provides various monitors(procmon,network or any custom monitors) and callbacks which can be used to monitor crashes and auto restart environment to resume the crashed program, We will only be using simple fuzzing script in this tutorial for easier understanding.

**For advanced examples you are free to refer programs from **[**here**](https://github.com/jtpereyda/boofuzz/tree/master/examples)**.**

Save the below script with filename ModbusFuzz.py & run **python ModbusFuzz.py** to start boofuzz fuzzer.

> You can view the Web UI at [http://127.0.0.1:26000/](http://127.0.0.1:26000/) once the fuzzer starts.

{{<highlight python>}}
from boofuzz import *
import socket

HOST = "127.0.0.1"
PORT = 502
SLEEP = 0
RECV_TIMEOUT = 2

target = Target(connection=TCPSocketConnection(host=HOST,port=PORT))

def checkAliveAndRestart(target, fuzz_data_logger, session, sock, *args, **kwargs):
    SOCKET = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    IS_CONNECTED = SOCKET.connect_ex((HOST,PORT))
    SOCKET.close()
    # Connection Lost
    if IS_CONNECTED != 0:
        fuzz_data_logger.log_error(description="Crash Detected: " + fuzz_data_logger.most_recent_test_id)
        import os
        os._exit(1)
        """
        May be restart process after crash ?
        """


SESSION = Session(target=target,post_test_case_callbacks=[checkAliveAndRestart],restart_callbacks=[checkAliveAndRestart])


#Fuzz Read Coil function of Modbus Protocol
def FuzzReadCoilMemory():
 s_initialize("modbus_read_coil")
 s_word(0x0001,name='Transaction ID',fuzzable=True,endian=BIG_ENDIAN)
 s_word(0x0000,name='Protocol ID',fuzzable=False,endian=BIG_ENDIAN)
 s_word(0x0006,name='Length',fuzzable=True,endian=BIG_ENDIAN)
 s_byte(0x00,name='Unit Identifier',fuzzable=False,endian=BIG_ENDIAN)
      
 s_byte(0x01,name='Function Code for Read Coil Memory',fuzzable=False,endian=BIG_ENDIAN)
 s_word(0x0000,name='Start Address',fuzzable=True,endian=BIG_ENDIAN)
 s_word(0x0001,name='Amount of Coils to Read',fuzzable=True,endian=BIG_ENDIAN)

 SESSION.connect(s_get("modbus_read_coil"))
 SESSION.fuzz()

if __name__=="__main__":
    FuzzReadCoilMemory()
{{</highlight>}}

**Explanation of the above script —**

1. First we created a target (host/device to fuzz) which uses TCP connection on given HOST and PORT.
2. Then we created a boofuzz Session Object where we passed our target to fuzz along with post_test_case_callbacks & restart_callbacks methods. These methods defines what boofuzz must do after each test case & how to restart the target in case of crash respectively.
3. In the FuzzReadCoilMemory, We defined Read Coil packet of modbus as per the standards explained earlier. s_initialize is used to initialize a node to fuzz, SESSION.connect() is used to create connections between different nodes to fuzz, In this case as we are fuzzing only a single node, we pass only s_get("modbus_read_coil").
4. SESSION.fuzz() is used to start the fuzzing session.

**Note: We are only fuzzing one request in this tutorial which is Read Coil, you may need to create more nodes as required to implement all modbus functions.**

Refer [this ](https://github.com/youngcraft/boofuzz-modbus/blob/master/fuzz_modbus.py)for detailed implementation of all the methods.

![](/post/img/2024-01-17_intro-to-fuzzing-iot-protocols-using-boofuzz_1.png#layoutTextWidth)

boofuzz by default generates output in .db files which can be viewed through SQLite Viewers to analyze test cases & steps to infer which test case has generated crash as shown below.

![](/post/img/2024-01-17_intro-to-fuzzing-iot-protocols-using-boofuzz_2.png#layoutTextWidth)

**References**:

1. [https://medium.com/@jtpereyda/a-simple-ftp-fuzzer-with-boofuzz-d02ceaf8ce7](https://medium.com/@jtpereyda/a-simple-ftp-fuzzer-with-boofuzz-d02ceaf8ce7)
2. [https://xavibel.com/2019/06/23/fuzzing-how-to-find-bugs-using-boofuzz/](https://xavibel.com/2019/06/23/fuzzing-how-to-find-bugs-using-boofuzz/)
3. [https://boofuzz.readthedocs.io/en/stable/user/quickstart.html](https://boofuzz.readthedocs.io/en/stable/user/quickstart.html)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on January 17, 2024 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/intro-to-fuzzing-iot-protocols-using-boofuzz-02bab66454a7)
