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

We are going to learn hacking using a game,  so go to 

```
http://flaws.cloud/
```

![image](https://github.com/user-attachments/assets/27e4ec27-430e-46ed-8dfc-7103ec76b24c)


Shout out to the developers of this site, you are amazing

Now lets, start hacking

## Level 1 
url: http://flaws.cloud
![image](https://github.com/user-attachments/assets/480a4f26-646e-42ff-9732-86f4d22dc4aa)

In my kali, we need to perform a reverse DNS lookup on the website, to do that I used dig 

```
dig flaws.cloud
```
![image](https://github.com/user-attachments/assets/12bc6e7f-8ec4-4262-af01-9d9e4e9b9403)

It would spit out different IP addresses, we can check them one by one,  like

```
dig -x 52.92.133.211
```

![image](https://github.com/user-attachments/assets/e938f79b-bc80-4e0c-b1ee-edc6f380f67d)

I want to check every IP addresses, since I am lazy, I saved the results to a file and parsed them using awk to get only the ip adddreesses.

I copy/pasted the dig results to a file and they should look like this

![image](https://github.com/user-attachments/assets/025df057-1006-4a09-b194-a4a0615f9031)


```
cat flaws.cloud.dig | awk -F ' ' '{print $NF}'

```

to spit out only the ip addresses, we can redirect the results to a file using the ```>``` redirector

```
cat flaws.cloud.dig | awk -F ' ' '{print $NF}'>> ips

```
To check if the IP addresses is saved correctly, we can view it first

![image](https://github.com/user-attachments/assets/e529bab4-74fd-4341-a341-c796227cfb1d)


Now, to use dig in every IP addresses we can do

```
for i in $(cat ips.dig);do dig -x $i;done
```

![image](https://github.com/user-attachments/assets/0ec487af-b7d8-4115-9bca-2dde0572d9d6)

Now we know, all of them are pointing to the the same s3 bucket.

We can try and connect to the site this website, hosted in s3 by doing

```
aws s3 ls s3://flaws.cloud
```
![image](https://github.com/user-attachments/assets/5b5d4dd2-3ec0-46b0-ae7c-10c2cc549c20)


### Note
> This level does not require keys to be set up, however, since I already configured aws keys, I was able to access the files
> You will  get an error if your aws keys are not configured, you need to add ```--no-sign-request``` to your command

I went ahead and downloaded the secretfile, using this command, 

```
aws s3 cp s3://flaws.cloud/secret-dd02c7c.html --no-sign-request secret.html
```
> the `secret.html` would be the downloaded file name

![image](https://github.com/user-attachments/assets/6269c66e-0264-492b-9746-93ce7c61e3a2)

Then I opened it in my terminal using

```
                                                                                                                                                                 
┌──(aaron㉿kali)-[~/cloud-hacking/level1]
└─$ cat secret.html| html2text
                      _____  _       ____  __    __  _____
                     |     || |     /    ||  |__|  |/ ___/
                     |   __|| |    |  o  ||  |  |  (   \_
                     |  |_  | |___ |     ||  |  |  |\__  |
                     |   _] |     ||  _  ||  `  '  |/  \ |
                     |  |   |     ||  |  | \      / \    |
                     |__|   |_____||__|__|  \_/\_/   \___|
              ****** Congrats! You found the secret file! ******
Level 2 is at http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
                                                                              
```
![image](https://github.com/user-attachments/assets/1e42a11b-5730-4d6d-ae14-5f25b97f1aed)

> If you dont have html2text, you can install them in kali using
> ```sudo apt install html2text```
Now we can proceed to level 2.
>

##  Level 2
URL : http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
I typed in my terminal, and was able to access the bucket

```
┌──(aaron㉿kali)-[~/cloud-hacking/level2]
└─$ aws s3 ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud       
2017-02-26 21:02:15      80751 everyone.png
2017-03-02 22:47:17       1433 hint1.html
2017-02-26 21:04:39       1035 hint2.html
2017-02-26 21:02:14       2786 index.html
2017-02-26 21:02:14         26 robots.txt
2017-02-26 21:02:15       1051 secret-e4443fc.html

```

> I replaced the 'http' to 's3'
> I was able to access the the s3 resource because my aws are configured
> The intended solution should to learn how to configure aws keys and we already done that in our set up

Now, we'll copy the secret again to our local machine using `cp` command

```
aws s3 cp s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/secret-e4443fc.html secret.html

```

![image](https://github.com/user-attachments/assets/84e7d157-b3f9-492a-9558-2465070e381a)

Then we open the file in our terminal again using

```
                                                                                                                                                                 
┌──(aaron㉿kali)-[~/cloud-hacking/level2]
└─$ cat secret.html| html2text
                      _____  _       ____  __    __  _____
                     |     || |     /    ||  |__|  |/ ___/
                     |   __|| |    |  o  ||  |  |  (   \_
                     |  |_  | |___ |     ||  |  |  |\__  |
                     |   _] |     ||  _  ||  `  '  |/  \ |
                     |  |   |     ||  |  | \      / \    |
                     |__|   |_____||__|__|  \_/\_/   \___|
              ****** Congrats! You found the secret file! ******
Level 3 is at http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
                                                                            
```

![image](https://github.com/user-attachments/assets/84146a37-8e1c-4fc0-9c0c-513144d2881e)

## Level 3
url : http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
