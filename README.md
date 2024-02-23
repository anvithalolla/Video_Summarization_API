# Video Summarization API With DSNet Using PyTorch and AWS

VideoSummAPI leverages deep learning and AWS infrastructure to provide an efficient, scalable solution for summarizing video content. This project is inspired by the DSNet paper and aims to make video content more accessible and manageable by extracting and presenting key segments, saving time and resources for users.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
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

## Installation

1. Clone the repository:
    ```
    git clone <repository-url>
    cd <repository-name>
    ```
2. Install dependencies:
    ```
    pip install -r requirements.txt
    ```

## Getting Started

Set up your AWS environment according to the provided guidelines, ensuring all necessary services are correctly configured. Deploy the API and follow the instructions to start processing video summarization tasks.

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


