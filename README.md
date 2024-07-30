English| [简体中文](./README_cn.md)

# 1. Introduction of Functions

**hobot_llm** is a Node of **Large Language Model (LLM)** integrated into the RDK platform, allowing users to experience LLM at the edge. Currently, there are two ways to experience it: direct text chatting at the terminal and subscribing to text messages, then publishing the results in text format.

# 2. Bill of Materials

| Robot Name        | Manufacturer | Reference Link                                  |
| :---------------- | ----------- | ---------------------------------------------- |
| RDK X3 (4GB RAM)  | Multiple Suppliers   | [Click here](https://developer.d-robotics.cc/rdkx3) |

# 3. How to Use

## 3.1. Preparation

Before experiencing it, the following basic requirements need to be met:

- Confirm that the RDK is the 4GB RAM version.
- The RDK has been burned with the Ubuntu 20.04.
- Install transformers, using the command `pip3 install transformers -i https://pypi.tuna.tsinghua.edu.cn/simple`.
- Update hobot-dnn, using the command `sudo apt update; sudo apt install -y hobot-dnn`.

## 3.2. Installing Function Packages

After starting the RDK X3, connect to the robot through SSH or VNC terminal, copy and run the following commands on the RDK system to complete the installation of the corresponding Nodes.

tros foxy:
```bash
sudo apt update
sudo apt install -y tros-hobot-llm
```

tros humble:
```bash
sudo apt update
sudo apt install -y tros-humble-hobot-llm
```

## 3.3. Running the Program

Before running the program, download the model files and unzip them using the following commands:

tros foxy:
```bash
# Download model files
wget http://archive.d-robotics.cc/tros/llm-model/llm_model.tar.gz

# Unzip
sudo tar -xf llm_model.tar.gz -C /opt/tros/${TROS_DISTRO}/lib/hobot_llm/
```

tros humble:
```bash
# Download model files
wget http://archive.d-robotics.cc/tros/llm-model/llm_model.tar.gz

# Unzip
source /opt/tros/humble/setup.bash ##export TROS_DISTRO=humble
sudo tar -xf llm_model.tar.gz -C /opt/tros/${TROS_DISTRO}/lib/hobot_llm/
```

At the same time, change the reserved memory size of BPU to 1.7GB. Refer to [TODO]() for the setting method.

After rebooting, adjust the CPU's highest frequency to 1.5GHz and set the scheduling mode to `performance`, using the following commands:

```bash
sudo bash -c 'echo 1 > /sys/devices/system/cpu/cpufreq/boost'
sudo bash -c 'echo performance > /sys/devices/system/cpu/cpufreq/policy0/scaling_governor'
```

Currently, two running programs are provided, **hobot_llm_chat** and **hobot_llm**. Among them, **hobot_llm_chat** provides a terminal interactive experience, allowing users to directly input text to experience the large model. The **hobot_llm** program subscribes to text messages of type `std_msgs/msg/String`, sends them to the large model for processing, and finally publishes the results in the form of `std_msgs/msg/String`. This program can be connected with other nodes, for example, to play the output text as speech.

### 3.3.1. Running hobot_llm_chat

tros foxy:
```bash
source /opt/tros/setup.bash

ros2 run hobot_llm hobot_llm_chat
```

tros humble:
```bash
source /opt/tros/humble/setup.bash

ros2 run hobot_llm hobot_llm_chat
```

After the program is launched, you can chat directly with the robot in the current terminal.

### 3.3.2. Running hobot_llm

1. Start hobot_llm

    tros foxy:
    ```bash
    source /opt/tros/setup.bash

    ros2 run hobot_llm hobot_llm
    ```

    tros humble:
    ```bash
    source /opt/tros/humble/setup.bash

    ros2 run hobot_llm hobot_llm
    ```

2. Open a new terminal to subscribe to the output result topic


    tros foxy:
    ```bash
    source /opt/tros/setup.bash

    ros2 topic echo /text_result
    ```

    tros humble:
    ```bash
    source /opt/tros/humble/setup.bash

    ros2 topic echo /text_result
    ```

3. Open a new terminal to publish a message

    tros foxy:
    ```bash
    source /opt/tros/setup.bash

    ros2 topic pub --once /text_query std_msgs/msg/String "{data: ""What is the capital of China?""}"
    ```

    tros humble:
    ```bash
    source /opt/tros/humble/setup.bash

    ros2 topic pub --once /text_query std_msgs/msg/String "{data: ""What is the capital of China?""}"
    ```

# 4. Interface Description

The interface of **hobot_llm** program is as follows:

## 4.1. Topics

| Name         | Message Type         | Description               |
| ------------ | -------------------- | ------------------------- |
| /text_query  | std_msgs/msg/String  | Default subscription topic |
| /text_result | std_msgs/msg/String  | Default result publication topic |## 4.2. Parameters

| Parameter   | Type         | Description          | Required | Supported Configuration | Default Value  |
| ----------- | ------------ | -------------------- | -------- | ------------------------ | -------------- |
| topic_sub   | std::string  | Subscription topic name | No      | Configured according to the actual deployment environment | /text_query  |
| topic_pub   | std::string  | Publish result topic name | No     | Configured according to the actual deployment environment | /text_result |

# 5. Frequently Asked Questions

1. Model loading failure

   Confirm that the development board has 4GB of memory, and modify the reserved memory size of BPU to 1.7GB.

2. Output result is garbled

   Confirm that you have used the command `sudo apt update; sudo apt install hobot-dnn` to update hobot-dnn.

3. How to manually modify the BPU reserved memory to 1.7GB?

   Refer to the modification method in [Setting ion_cam size in device tree](https://developer.horizon.ai/api/v1/fileData/documents_rdk/system_software_development/driver_develop_guide/18-Memory_Managment_zh_CN.html#ion-cam-size), and change the values from 0x2a000000 to 0x6a400000 in the alloc-ranges and size attributes.
