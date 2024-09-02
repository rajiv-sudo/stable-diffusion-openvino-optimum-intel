# Running Stable Diffusion on Xeon Scalable Processors CPU in AWS using optimum[openvino] from Hugging Face

**Optimum[openvino]** is an extension of the Hugging Face Optimum library specifically designed to work with Intel's OpenVINO toolkit. This integration allows developers to optimize and accelerate the inference of machine learning models, particularly those from the Hugging Face model hub, on Intel hardware, including CPUs, GPUs, and AI accelerators.

By using **Optimum[openvino]**, you can easily load pre-trained models, apply performance optimizations like quantization (e.g., INT8), and run these models efficiently on Intel architectures. This is especially useful for deploying AI models in environments where performance and resource utilization are critical, such as in edge computing or production scenarios where inference speed and efficiency are paramount.

In this repository, we demonstrate how to download the stabilityai/stable-diffusion-xl-base-1.0 model from Hugging Face, then use the OpenVINO toolkit to quantize it to INT8 precision. We will guide you through running inference to generate images from text prompts, optimized for Intel hardware, specifically Intel Sapphire Rapids Xeon Scalable processors. This approach allows you to leverage your existing Xeon-based infrastructure for various Generative AI use cases, including creative content generation, visual storytelling, design prototyping, and enhancing artistic workflows.

We will use an M7i.8xlarge EC2 instance in AWS to run the test. The instance is based on the 4th generation of Intel Xeon Scalable processors called Sapphire Rapids.

## Pre-requisites
- Access to an AWS account and privilege to setup EC2 instances
- Knowledge of setting up EC2 instances
- Basic familiarity with running CLI commands, SSH, and running Jupyter notebook

### Step-01

Launch an EC2 instance from AWS Console. Select US-East-1 region, since it is likely to have better availability in that region. Specs for the EC2 instance as follows.

> M7i.8xlrage
>
> Ubuntu 24.04
>
> 300 GB disk storage
>
> Enable public IP
>
> Inside your existing VPC or default VPC
>
> Create a key pair or use your existing key pair
>
> Create a security group or use your existing security group. Inbound rules needed for security group are:
>> open port 22 for SSH from your desired IP address
>>
>> open port 8888 for connecting to jupyter notebook from your desired IP address
>
> Wait until the EC2 instance is fully launched and ready

### Step-02
Log into the EC2 instance via SSH. You will need the EC2 key that you created or reused in the earlier step. Since the OS is ubuntu 24.04, your ssh login username will be ubuntu.

You can use your favorite SSH clients like putty or bitvise or even log in from your command line.

Once logged in you will be at the directory `/home/ubuntu`

### Step-03
Run the following commands.

Update the OS

```
sudo apt-get update
```

```
sudo apt-get upgrade -y
```

Install pip

```
sudo apt-get install python3-pip
```

Install Python virtual environment

```
sudo apt-get install python3-venv
```

Create a virtual environment called test

```
python3 -m venv test
```

Activate the virtual environment

```
source test/bin/activate
```

Install Jupyterlab

```
pip3 install jupyterlab jupyter
```

Generate Jupyter notebook configuration file

```
jupyter notebook --generate-config
```

> note the path to the configuration file is /home/ubuntu/jupyter/jupyter_notebook_config.py

Create Jupyter notebook password

```
jupyter notebook password
```
>
> you enter the password of your choice. You will need to re-enter the password to confirm

### Step-04
From the command line within the EC2 instance, from your SSH session, type the command below to start the Jupyter server.

```
taskset -c 0-31 jupyter-lab --ip 0.0.0.0 --no-browser --allow-root
```

The command `taskset -c 0-31 jupyter-lab --ip 0.0.0.0 --no-browser --allow-root` launches a Jupyter Lab server, binding it to CPU cores 0 through 31 to control which processors the server can use. It also configures the server to be accessible from any IP address **(--ip 0.0.0.0)**, disables launching a web browser **(--no-browser)**, and allows it to run as the root user **(--allow-root)**.

Do not press control + C, keep this running, so that you can access the Jupyter notebook from your web browser.

### Step-05
Access the Jupyter notebook and run the instructions within the notebook for using optimum[openvino] to convert text to image.

Open your favorite browser, like Chrome or something you use. On the address bar line type the following link and press `Enter` key.
`http://Public_IP:8888`, where **Public_IP** is the public IP address of your EC2 instance. You can get the public IP address from the AWS console or CLI

Once the Jupyter notebook opens, give the password you set befor. This will bring you into the Jupyter notebook environment.

### Step-06
Once in the Jupyter notebook environment, click the upload files icon and upload the notebook `.ipynb` file. Name of the file is `OpenVINO_Stable_Diffusion.ipynb`. The screen shot below show the location of the upload files icon.

![Upload Files Icon in Jupyter](Upload_Files_Jupyter.png "Upload Files Icon in Jupyter")

### Step-07
Double click the `OpenVINO_Stable_Diffusion.ipynb` file on the left hand tree of the Jupyter notebook. This will launch the notebook. Read the documentation and run through the notebook cells to go over the test step by step.

## Destroy Resources in AWS
At the end of the testing, please remember to destroy or terminate your resources in AWS to avoid aditional billing.

## Next Steps

- We encourage you to try out the testing in your own environment to run stable diffusion on Xeon with optimized models by Openvino toolkit.
- Try this method on other Intel platforms. OpenVINO optimizes AI models for Intel CPUs, integrated and discrete GPUs, Movidius VPUs, and FPGAs, enhancing performance across diverse platforms. It leverages Intel-specific technologies like AVX-512 and VNNI, enabling efficient inferencing from edge devices to cloud environments, maximizing the potential of Intel-based hardware for AI workloads.
- Reach out to OpenVino Development Community with your questions, suggestions, and feedback.

## Acknowledgements and References
[Stable Diffusion Pipelines with Optimum OpenVINO](https://huggingface.co/docs/diffusers/en/optimization/open_vino)
Optimum provides Stable Diffusion pipelines compatible with OpenVINO to perform inference on a variety of Intel processors
