# Video Summarization API With DSNet Using PyTorch and AWS

VideoSummAPI leverages deep learning and AWS infrastructure to provide an efficient, scalable solution for summarizing video content. This project is inspired by the DSNet paper and aims to make video content more accessible and manageable by extracting and presenting key segments, saving time and resources for users.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Pre-Training and API Usage](#pre-training)
- [System Design](#system-design)
- [Implementation Phases](#implementation-phases)
- [AWS Services Used](#aws-services-used)
- [Output](#output)
- [Contributing](#contributing)
- [License](#license)

## Introduction

![image](https://github.com/anvithalolla/Video_Summarization_API/assets/55392153/995d4d01-4234-49de-9093-98ac384c8a34)

The VideoSummAPI is designed to handle 600 requests per day, processing input videos stored in S3 buckets and utilizing various AWS services for a fully integrated cloud solution.

## Prerequisites

- AWS Account with CLI configured
- Python 3.7+
- Access to AWS EC2, S3, Lambda, API Gateway, SQS, SNS

## Getting Started

This section guides you through the initial setup and configuration needed to start using VidSummarizer. The setup involves creating an AWS EC2 instance, configuring it with the necessary hardware and software, and setting up the base repository.

### Step 1: AWS EC2 Instance Setup

1. **Create an EC2 Instance**:
    - Log into your AWS Management Console and navigate to the EC2 Dashboard.
    - Launch a new instance and select the **p2.xlarge** instance type for optimal performance with video summarization tasks. Note: This instance type is necessary for its GPU capabilities, which are essential for processing video data efficiently.

2. **Increase Capacity**:
    - By default, AWS accounts might have limitations on the usage of certain instance types. If you encounter a limit for the **p2.xlarge** instances, request an increase in capacity through AWS Support.

3. **Key-Pair Configuration**:
    - During the instance setup, create a new key-pair named `video_summarization_key`. This key will be used to securely connect to your instance.
    - Download the key-pair and convert it to `.ppk` format using a tool like PuTTYgen if you're on Windows. This format is required for PuTTY, a SSH client used to connect to the instance.

### Step 2: Connecting to Your Instance

1. **Use PuTTY for SSH Connection**:
    - Open PuTTY and input your instance's public IP address in the `Host Name (or IP address)` field.
    - Navigate to `Connection > SSH > Auth` in the PuTTY configuration menu. Browse and select your `.ppk` key file under `Private key file for authentication`.

2. **Remote Terminal Access**:
    - Once the configuration is set, open the connection to access the remote terminal of your EC2 instance.

### Step 3: Environment Setup

In the remote terminal, execute the following commands to set up your environment:

```bash
# Update your instance's package repository
sudo apt-get update

# Install CUDA (replace 'latest_version' with the actual version number)
sudo apt-get install cuda-latest_version

# Clone the base repository
git clone <repository-url>

# Navigate to the repository directory
cd <repository-name>

# Install required dependencies
pip install -r requirements.txt
```

## Pre-Training

VidSummarizer offers pre-trained models that are readily available for download. These models can be used for immediate video summarization without the need for training from scratch. If you prefer to train your own models, you may skip this section.

### Downloading Pre-trained Models

We provide two types of pre-trained models: anchor-based and anchor-free. You can download and unzip these models using the following commands:

- **Anchor-Based Model**:
    ```
    wget https://www.dropbox.com/s/0jwn4c1ccjjysrz/pretrain_ab_basic.zip
    unzip pretrain_ab_basic.zip
    ```

- **Anchor-Free Model**:
    ```
    wget https://www.dropbox.com/s/2hjngmb0f97nxj0/pretrain_af_basic.zip
    unzip pretrain_af_basic.zip
    ```

It's crucial to extract these pre-trained model archives for the subsequent video summarization tasks.

### Environment Setup

Before utilizing the pre-trained models, ensure your environment is correctly set up. This setup includes installing CUDA 11.0.4, Python 3.7, and other necessary dependencies.

1. **CUDA Installation**:
    Update your package list and remove any existing NVIDIA drivers before installing the new ones:
    ```
    sudo apt update
    sudo apt-get --purge remove 'nvidia*'
    sudo apt install nvidia-driver-450
    sudo reboot
    ```

2. **Python 3.7 Installation**:
    Update your package list, add the deadsnakes PPA for newer Python versions, and install Python 3.7 along with necessary packages:
    ```
    sudo apt update
    sudo apt install software-properties-common
    sudo add-apt-repository ppa:deadsnakes/ppa
    sudo apt install python3.7
    sudo apt install python3.7-distutils
    sudo apt-get install python3.7-dev
    ```

3. **Clone Repository and Set Up Virtual Environment**:
    After cloning the repository to your server, create a virtual environment using Python 3.7 and activate it:
    ```
    virtualenv venv --python=python3.7
    source venv/bin/activate
    ```

4. **Install Dependencies**:
    Navigate to your project directory and install the required Python packages. If you encounter any issues with Torch, install it first before proceeding with other requirements:
    ```
    cd DSNet  # Change to your project directory
    pip install torch==1.1.0
    pip install -r requirements.txt
    ```

### Running Inference

To run the inference process, follow these steps:

1. **Prepare the Model Directory**:
    ```
    mkdir -p models && cd models
    ```

2. **Execute Inference**:
    Change the directory to `src` and run the inference script. If you encounter an import error with PIL, update torchvision to version 0.5.0:
    ```
    cd src  # Change to the src directory of your project
    pip install torchvision==0.5.0  # Update torchvision if necessary
    python infer.py anchor-free --ckpt-path ../models/pretrain_af_basic/checkpoint/summe.yml.0.pt --source ../custom_data/videos/EE-bNr36nyA.mp4 --save-path ./output.mp4
    ```

3. **Testing with Custom Video**:
    To test with a custom video file, transfer it to your EC2 instance and run the inference script:
    ```
    scp -i ~/.ssh/aws-ft-key-pair.pem /path/to/test.mp4 ubuntu@<PUBLIC_DNS>:/path/to/DSNet/src/
    ```
    Replace `<PUBLIC_DNS>` with your instance's public DNS. Then, run the inference command again with your test video as the source.

By following these instructions, you can leverage VidSummarizer's pre-trained models for efficient video summarization.


## API Usage

Send a POST request with the video path and model selection to the API endpoint. The API processes requests asynchronously, with summarized videos stored in the designated output S3 bucket.

## System Design

Video Summarization employs a robust and scalable architecture to handle video summarization requests efficiently. The system is designed to be fully automated, leveraging various AWS services to manage the workflow from request to delivery.

### Overview

The process begins when a user submits a video summarization request through an API built with Amazon API Gateway. This request triggers a Lambda function, which performs two critical actions:

1. **Immediate Acknowledgment**: The Lambda function sends an immediate response back to the user, acknowledging the receipt of the request.
2. **Queue Update**: The function updates an Amazon SQS queue specifically created to manage the video summarization tasks.

### Continuous Polling and Processing

An EC2 instance is dedicated to continuously polling this SQS queue for new requests. Upon receiving a new request, the EC2 instance performs the following steps:

- **Video Download**: Retrieves the input video file from an Amazon S3 bucket.
- **Summarization**: Initiates the video summarization process using pre-determined models and algorithms.
- **Output Upload**: Once the summarization is complete, the output video is uploaded back to a designated S3 bucket for retrieval.

### Notification System

Upon successful completion and upload of the summarized video, an Amazon SNS topic is used to send a notification to the API endpoint. This notification contains details about the output video, such as its location in the S3 bucket, allowing the user to access the summarized content.

### Architectural Diagram

![image](https://github.com/anvithalolla/Video_Summarization_API/assets/55392153/3d765679-b905-40c4-ae3b-b81159125190)

This diagram illustrates the flow of data and interactions between different AWS services, providing a clear view of how VidSummarizer processes each video summarization request from start to finish.

By employing this architecture, VidSummarizer ensures a seamless and scalable solution to video summarization, capable of handling multiple requests concurrently and efficiently.
.

## Implementation Phases

1. **Initial Setup**: Creation of an AWS EC2 instance, installation of CUDA, and setup of the base repository.
2. **Model Preparation**: Downloading and extracting pre-trained models for video summarization.
3. **AWS Integration**: Configuration of Lambda functions, API Gateway, SQS for request management, and SNS for notification handling.
4. **Video Processing**: Implementation of video summarization logic on EC2 instances, with input/output managed via S3 buckets.

## AWS Services Used

- **EC2**: For running the video summarization models and processing tasks.
- **S3**: To store input and output videos.
- **Lambda**: To handle API requests and manage SQS queue updates.
- **API Gateway**: For API endpoint creation and management.
- **SQS**: To queue video summarization tasks.
- **SNS**: For notifying the API endpoint upon task completion.

## Output

The final output is a summarized version of the input video, significantly reduced in length while retaining essential content. This output is stored in an S3 bucket and accessible via a notification sent to the API endpoint.

![Output Example](path/to/output_example_image.png)

## Contributing

We welcome contributions to enhance VideoSummAPI's functionality and performance. Please refer to the contributing guidelines for more details.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.


