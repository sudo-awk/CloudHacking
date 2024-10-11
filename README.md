# Intro-CloudHacking

## Create an Amazon Account and generate your access id and secret access

Once you have signup for an account, go to Security Settings
![image](https://github.com/user-attachments/assets/fc4e09cc-19d6-4b96-8bcb-675a03372618)
 And generate your access key, it will spit out an access key and secret access key which will be like a username and password

Now that we have an account an aws cli, we can now proceed to test.

We are going to learn hacking using a game,  so go to 

```
http://flaws.cloud/
```

![image](https://github.com/user-attachments/assets/27e4ec27-430e-46ed-8dfc-7103ec76b24c)


Shout out to the developers of this site, you are amazing

Now lets, start hacking

## Level 1 

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

To try and connect to
