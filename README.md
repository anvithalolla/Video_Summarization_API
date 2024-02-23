# Video Summarization API With DSNet Using PyTorch and AWS

VideoSummAPI leverages deep learning and AWS infrastructure to provide an efficient, scalable solution for summarizing video content. This project is inspired by the DSNet paper and aims to make video content more accessible and manageable by extracting and presenting key segments, saving time and resources for users.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [API Usage](#api-usage)
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

## API Usage

Send a POST request with the video path and model selection to the API endpoint. The API processes requests asynchronously, with summarized videos stored in the designated output S3 bucket.

## System Design

![System Architecture](path/to/system_architecture_image.png)

The system architecture utilizes AWS Lambda for immediate request acknowledgment and SQS for queue management, with EC2 instances processing video summarization tasks.

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


