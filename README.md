# DLforCV cloud setting
## Environments
### Google Cloud instance
#### VMs
##### Create a VM instance using Google Cloud
Settings:
* Zone: us-east1-d
* Machine type: click customize
  - 8 vCPU
  - 30 GB Memory
  - CPU platform: Intel Haswell or later
  - GPU: 1 NVIDIA Tesla K80
* Boot disk:
  - OS images: Ubuntu 16.04 LTS
  - Boot disk type: Standard persistent disk
  - Size: 200 GB

##### Apply for GPU quota (Skip this part if you do not need one)
1. Link your billing account to the credit you received (If you use the $300 free trial credit in your account, you will not be able to use GPU)
2. In the notifications on top right of your browser, click request increase
![quota-request](https://lh4.googleusercontent.com/zAgJVQvSSSRoFBXHVgX0kvNU12MpHoGbHJD3ieAJ6-u-ibjGfAVFFIQOFpTy0vehwrOQUYRnrdIpcoLNGVv2tKFKudxy67kZerd5Nt-zmXBu5iWFKjcE34ApDgkJjpYeISt2t-JS)
3. In the Quotas, find NVIDIA K80 GPUs for us-east1. Select it and click EDIT QUOTAS on top.
![quota-request2](https://lh4.googleusercontent.com/bTBt3dg6Ylm0BbdwPHQV0FQS-htvqMkW_L3PpBn9xrr8d2ob1WP0VvlBQv-IrCMlqLjAU4D-cEc7T0Kf2WY0aZvnHk-5xlLsvwwK_xvuxmx-6RH9Umz5jnZ_bc9x9hXm94rpUsfX)
4. Enter your information.
5. Enter 1 in the limit
6. In the description say you will use the gpu for Columbia CS 4995 Deep learning for Computer Vision Course Project
7. The quota will be approved almost instantaneously

##### Connect to your VM
1. Web terminal
2. SSH
  generate a public private key pair. Use your uni for USERNAME. USERNAME will be used later.
  ```
  ssh-keygen -t rsa -f ~/.ssh/[KEY_FILENAME] -C [USERNAME]
  cat ~/.ssh/[KEY_FILENAME].pub
  Save it to Metadata
  ssh -i ~/.ssh/my-ssh-key [USERNAME]@[EXTERNAL_IP_ADDRESS]
  ```
3. Cyberduck/WinSCP/Putty

##### Tensorflow with GPU
###### Set up environment from scratch

```
The following script helps you install all the dependencies for keras.
We highly recommend that you run the following code, manually, line by line
to avoid any problem.

You need to run the code in the given order to avoid dependency issues.

* Linux Essentials
* Nvidia Driver
* CUDA
* cuDNN
* Tensorflow
* Keras
```

```
# essential
sudo apt-get update
sudo apt-get upgrade  
sudo apt-get install build-essential cmake g++ gfortran 
sudo apt-get install git pkg-config python-dev 
sudo apt-get install software-properties-common wget
sudo apt-get autoremove 
sudo rm -rf /var/lib/apt/lists/*

# install driver
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-375

# reboot your machine
Sudo reboot

# check driver is installed correctly
nvidia-smi

# install cuda
wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run
sudo chmod +x cuda_9.0.176_384.81_linux-run
sudo sh cuda_9.0.176_384.81_linux-run
'''
Executing cuda installation
1. scoll to the bottom of the license agreement using d
2. Do you accept the previously read EULA? : accept
3. Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81?: NO (we've installed manually)
4. Install the CUDA 9.0 Toolkit? : yes
5. Enter Toolkit Location: use default(press enter)
6. Do you want to install a symbolic link at /usr/local/cuda?: yes
7. Install the CUDA 9.0 Samples?: no
'''

# change environment for cuda
echo 'export PATH=/usr/local/cuda-9.0/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc 

#verify your cuda is installed correctly
nvcc -V 

# install cuDNN 
# You need to register online and download the file to local machine then upload to the cloud
# https://developer.nvidia.com/cudnn
# download the newest version for CUDA 9.0 and for Ubuntu 16.04. 
sudo apt install ./libcudnn7_7.1.2.21-1+cuda9.0_amd64.deb 

# install python stuff
sudo apt-get update
sudo apt-get install git python-dev python3-dev python3-numpy build-essential python-pip python3-pip python-virtualenv swig python-wheel libcurl3-dev
sudo apt-get install -y libfreetype6-dev libpng12-dev
pip3 install -U matplotlib ipython[all] jupyter pandas scikit-image

# install tensorflow
pip3 install --upgrade tensorflow-gpu

# keras
pip3 install keras

# pytorch
pip3 install http://download.pytorch.org/whl/cu90/torch-0.3.1-cp35-cp35m-linux_x86_64.whl 
pip3 install torchvision

# helpful tools 
# tmux: keep session in the background. Keep the session running even the ssh disconnects.
# nohup: similar to tmux. Keep things running. Log the output to nohup.log
```

##### Tensorflow without GPU
```
wget https://repo.continuum.io/archive/Anaconda3-5.1.0-Linux-x86_64.sh
bash Anaconda3-5.1.0-Linux-x86_64.sh
pip install tensorflow
```

## Examples
- Go to google gloud console, go to 'VPC Network' panel, select 'Fire wall rules'. Add a rule as follows.
```
tensorboard

Network
default

Priority
1000

Direction
Ingress

Action on match
Allow

Source filters
IP ranges
0.0.0.0/0

Protocols and ports
tcp:6006;udp:6006
```

- Then click your own instances, go to 'Edit', check box with 'Enable connecting to serial ports'

- Run the following code in ssh
```
# Get the code from tensorflow
git clone https://github.com/tensorflow/tensorflow.git

# Mnist is chosen as demo
cd tensorflow/tensorflow/examples/tutorials/mnist

# run it in the background, output is stored in train.log
nohup python mnist_with_summaries.py --max_steps=1000000 > train.log

# close and open another terminal
# run the tensorboard
tensorboard --logdir=/tmp/tensorflow/mnist
```

- Close the terminal

- Open your browser, go to '[external id]: 6006', you should see the tensorflow.
([extenal id] could be found on the VM instance page)





<hr>

###  Remember to Stop the instance
## Remember to Stop the instance
# Remember to Stop the instance

