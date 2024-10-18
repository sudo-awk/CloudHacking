# Intro-CloudHacking
This is my notes to learn about cloud hacking, this is only introductory, and still there are a lot of things I need to learn


## Create an Amazon Account and generate your access id and secret access

Once you have signup for an account, go to Security Settings
![image](https://github.com/user-attachments/assets/fc4e09cc-19d6-4b96-8bcb-675a03372618)

And generate your access key, it will spit out an access key and secret access key which will be like a username and password.

## Install aws cli 

To install the aws in your machine, you can go the amazon aws install documentation, provided by official amazon documentation

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

If you are using kali you can also use this copy and paste this script
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Once installed, we can now configure to use our access key using the command

```
aws configure
```
Then paste your keys

![image](https://github.com/user-attachments/assets/6bd36beb-5de8-4dd9-8dd9-f4dda623ffd2)



Yey, congrats!

## Level 5

![image](https://github.com/user-attachments/assets/02d5d9d8-6d2b-43fe-91db-215dce2b1e6b)
