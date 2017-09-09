# Basic AWS EC2
A tutorial for setting up an AWS EC2 instance, installing libraries and training a simple neural network.

![](img/1_index.png )

**Prerequisite:** Install AWS Command Line Interface: http://docs.aws.amazon.com/cli/latest/userguide/installing.html
If you have `pip` and Python, it's as simple as `$ pip install awscli --upgrade --user`

Okay then, let's begin!

# Part 1 - Launch an connect to EC2 Instance with Deep Learning AMI

### 1. Create and login to a free account at https://aws.amazon.com/
### 2. From the console, click 'EC2'

![](img/2_aws_console.png)

### 3. From the EC2 dashboard, launch an instance.

![](img/3_launch.png)

### 4. Choose an AMI (Amazon Machine Image).
We will use Deep Learning AMI, which comes preinstalled with Jupyter and machine learning libraries such as Theano and TensorFlow.

![](img/4_choose_ami.png)

### 5. Choose the general purpose t2.micro instance type that is selected by default
For the purpose of this tutorial, t2.micro is sufficient and is also free for the amount we will use it. However, this is where you could scroll down and select a GPU instance if you planned to train a deep neural network on image classification, for example. Click 'Next' on this and the next several screens until you land at **Configure Security Group**.

![](img/5_instance_type.png)

### 6. Configure security group.
In addition to the default SSH rule, add HTTPS and Custom TCP rules shown as below. These will allow us to access the Jupyter server from our browser via HTTPS. Note that by setting the source IP to 0.0.0.0/0 for each rule, we are allowing all IP addresses to access the VM instance. If we were working on a sensitive project we wanted to secure better, we would specify just one or perhaps a range of IP addresses that could access the instance. Click **Review & Launch**.

![](img/6_sec_group.png)

### 7. Launch instance
Hit 'Launch'. Follow the prompt to create and download a key pair that will allow you to connect securely to your instance. Create a new folder named **aws_tutorial** and save the key_pair.pem file there. In the below image, the downloaded key pair file will be called tutorial.pem.

![](img/7_key_pair.png)

On the Launch Status page, click 'View Instances.' You will see a lot of info about the new instance including its DNS name and public IP address, both of which we will use to access the VM.

### 8. SSH to the instance
Using your command-line tool, navigate to **aws_tutorial** where you stored key_pair.pem. Enter the following:

`ssh -i <PATH_TO_PEM> ec2-user@ec2-xx-xxx-xx-xxx.us-west-2.compute.amazonaws.com`

Because the current directory contains the key pair, `<PATH_TO_PEM>` is simply the key pair file name. Replace `ec2-xx-xxx-xx-xxx` with the Public DNS (IPv4) for your instance found on the Instances dashboard.

### 9. Install additional libraries on your EC2.
Since we chose Amazon's Deep Learning AMI, we already have the libraries we need for this tutorial. However, if you chose a 'vanilla' AMI that is not specialized for any task, you can set up your environment easily using `pip install` to add packages.

# Part 2 - Configure Jupyter Server

### 1. Generate Jupyter configuration file and choose password to access notebooks
Enter the following commands and follow prompt to set a password:

```python
jupyter notebook --generate-config
key=$(python -c "from notebook.auth import passwd; print(passwd())")
```

### 2. Generate certificates from your chosen password
Enter the below commands and follow prompts. If you like, you may leave all fields blank when asked for info to be incorporated into the certificate request (Country Name, State or Province Name, etc.)

```python
cd ~
mkdir certs
cd certs
certdir=$(pwd)
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.key -out mycert.pem
```

### 3. Point Jupyter configuration file at your newly created certificates
Enter this command so that Jupyter server to grant access to notebooks over HTTPS when your chosen password is provided:
```python
cd ~
sed -i "1 a\
c = get_config()\\
c.NotebookApp.certfile = u'$certdir/mycert.pem'\\
c.NotebookApp.keyfile = u'$certdir/mycert.key'\\
c.NotebookApp.ip = '*'\\
c.NotebookApp.open_browser = False\\
c.NotebookApp.password = u'$key'\\
c.NotebookApp.port = 8888" .jupyter/jupyter_notebook_config.py
```
# Part 3 - Run Jupyter server and copy notebook to it from cloned repo
On the EC2 instance we'll create a folder `notebook` and start the Jupyter server from that location. On the EC2 instance, start Jupyter server the same way you do from your local machine.

```python
mkdir notebook
cd notebook
jupyter notebook
```
You can now access the Jupyter server in your browser at this URL:

```python
https://<your-instance-public-ip>:8888
```
Now, open a new terminal window on your local machine and navigate to the folder containing both the cloned repo and your .pem key pair file. Enter the following command to securely copy the ipynb notebook in that folder to the EC2 instance:

```python
scp -i <PATH_TO_PEM> placeholder.ipynb ec2-user@ec2-xx-xxx-xx-xxx.us-west-2.compute.amazonaws.com:./notebook
```
You should now be able to run the copied notebook in your browser and proceed with the neural network demo using Theano!
