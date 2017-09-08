# Basic AWS EC2
A tutorial for setting up an AWS EC2 instance, installing libraries and training a simple neural network.

![](img/1_index.png )

### 1. Create and login to a free account at https://aws.amazon.com/
### 2. From the console, click 'EC2'

![](img/2_aws_console.png)

### 3. From the EC2 dashboard, launch an instance.

![](img/3_launch.png)

### 4. Choose an AMI (Amazon Machine Image).
We will use Deep Learning AMI, which comes preinstalled with Jupyter and machine learning libraries such as Theano and TensorFlow.

![](img/4_choose_ami.png)

### 5. Choose the general purpose t2.micro instance type that is selected by default
For the purpose of this tutorial, t2.micro is sufficient and is also free for the amount we will use it. However, this is where you could scroll down and select GPU instances if you planned to do some heavier lifting and train a deep neural network for image classification, for example. Click 'Next' on this and the next several screens until you land at **Configure Security Group**.

![](img/5_instance_type.png)

### 6. Configure security group.
In addition to the default SSH rule, add HTTPS and Custom TCP rules shown as below. These will allow us to access the Jupyter server from our browser via HTTPS. Note that by setting the source IP to 0.0.0.0/0 for each rule, we are allowing all IP addresses to access the VM instance. If we were working on a sensitive project we wanted to secure, we would specify just one or perhaps a range of IP addresses that could access the instance. Click **Review & Launch**.

![](img/6_sec_group.png)

