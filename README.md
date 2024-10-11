![image](https://github.com/user-attachments/assets/f19b25a9-a83a-47b8-b57c-b50e36369885)# Intro-CloudHacking

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
