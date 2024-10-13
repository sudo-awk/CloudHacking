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
> You will  get an error if your aws keys are not configured
> The intended solution should be to add ```--no-sign-request``` to your command
>
> ```aws s3 ls s3://flaws.cloud --no-sign-request```


I went ahead and downloaded the secretfile, using  ```cp``` command on the file name, 

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

So, this this time, we successfully connected to the bucket, however

```
aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud

```

![image](https://github.com/user-attachments/assets/a210bfed-31e6-4ea4-aeb8-d62003b3360d)

In my end since there is no secret listed and we have to invesitgate each file, we have to download this files in our local machine. To do that, I used another aws command callled ```sync```, 

```
aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud .
```

And if you list files in our current working directory we should see this

![image](https://github.com/user-attachments/assets/9dbfa7fd-9a80-4c83-b7af-8096848dc273)

When I  download files, I always check for their exifdata to check for usernames, etc

```
exiftool * 
```

![image](https://github.com/user-attachments/assets/2ba8edca-1fdd-4085-af56-f1b2a2406f76)

I was not able to get any info on this, so I opened the authenticated_users.png, but still no valueable information to solve the challenge


![ image](https://github.com/user-attachments/assets/1f8eb19b-b9a8-4177-bebc-04323b3605b2)

however, since I play around with linux I typed noticed the .git folder hinding 

```
ls -last
```

![image](https://github.com/user-attachments/assets/df5876b4-e866-427a-981c-9b8546127d28)

Without further ado, I typed 

```
git log
```
We can see two commits made and an interesting comment, you can also notice that we are currently in the ```e526``` (HEAD|MASTER) tag

![image](https://github.com/user-attachments/assets/480ba3a3-9eb5-4f7f-b5f4-621a8217a678)


We can go to the previous commit by using ```git checkout commit_number```just like below

```
┌──(aaron㉿kali)-[~/cloud-hacking/level3]
└─$ git checkout f52ec03b227ea6094b04e43f475fb0126edb5a61
M       index.html
Previous HEAD position was b64c8dc Oops, accidentally added something I shouldn't have
HEAD is now at f52ec03 first commit

```

If we type ```ls``` again in our current terminal we will see a new file called ```access_key.txt```

![image](https://github.com/user-attachments/assets/45fa4909-6566-41d2-8ef2-e168815cb90e)

To go back to the current commit you can use 

```
git checkout master
```

Also another trick we can do is to use ```git diff``` to easily check whats changed in each commits

```
git diff b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526 f52ec03b227ea6094b04e43f475fb0126edb5a61

```


![image](https://github.com/user-attachments/assets/1ec59992-3bfc-4403-aa4b-b7b3ce58d6b5)



![image](https://github.com/user-attachments/assets/14a30b36-da87-402b-8133-88a45f22ae15)

Now all we need is to use this key, I am going to create a aws profile using this key. Back to my machine I used 

```
aws configure --profile profilename
```

![image](https://github.com/user-attachments/assets/e88bf4a0-b3ca-4e01-bfc3-228cc0356304)

To list all your aws profile, we can use 

```
aws configure list-profiles
```

![image](https://github.com/user-attachments/assets/4368e708-029d-41f5-928d-b04eb6b6166c)

Now we can list available buckets for this account using the command below

```
aws s3api list-buckets --profile level3 
```

![image](https://github.com/user-attachments/assets/ae62338e-8da9-4ee7-b567-4fa76ef825ab)

We can also see all the other urls to all hidden s3 services. But we dont want to finish the game by that, we need to learn more. 

## Level 4
URL: http://level4-1156739cfb264ced6de514971a4bef68.flaws.cloud/

![image](https://github.com/user-attachments/assets/f6f21622-dfe4-4346-9012-09f10da62bb6)

If we go the link provided in in the instructions, we are asked for a credential, we dont have any credentials yet aside from the access key and secret key we got from the previous level.

![image](https://github.com/user-attachments/assets/681623bc-8168-4e8c-aa3f-98e86d166460)

Addtionally, it was said that a snapshot was made before the server. 

I honestly, dont have an idea how to go about this so I clicked on the hint and we got this

![image](https://github.com/user-attachments/assets/1582dfb2-b53a-44f9-94cc-ec7d2296dc47)

So I went back to my machine and typed the command 

```
aws --profile level3 sts get-caller-identity
```

And we get the result below

![image](https://github.com/user-attachments/assets/2b1bc529-0ced-445c-b858-2640e4d86078)

We now have the accountid, we can follow the instructions on the hint again

    "Account": "975426262029",


![image](https://github.com/user-attachments/assets/ec1c1c31-adc4-401e-b01a-64f63fc85196)

I've tried to command in my end but it's giving me error to provide region

![image](https://github.com/user-attachments/assets/94e3384b-5450-41cc-9bff-ffc2d41995bf)

To fix the issue, I think you can put any region. But I want to put us-west-2 as was said in the hint. I will also opt out the accountid.

![image](https://github.com/user-attachments/assets/6d07a77e-bac5-48c6-9421-73e0a45fbb61)

Again, I dont have any idea how to solve this, so I just play around to see what we can find. Now we try to provde our accountid 

![image](https://github.com/user-attachments/assets/a7497c52-c678-4939-a898-7f2804eb9147)

I also tried to prompt using different us regions like east,north and south just to see what will happen, no results though as seen below

![image](https://github.com/user-attachments/assets/99bb0dc5-1d11-41cf-bdd0-ae490a0850e8)

It said we need to take a look at the snapshot

![image](https://github.com/user-attachments/assets/9b596c3a-a989-442e-9246-e1036a6bbeff)

```
┌──(aaron㉿kali)-[~/cloud-hacking/level3]
└─$ aws --profile level3 ec2 describe-snapshots --owner-id 975426262029 --region us-west-2
{
    "Snapshots": [
        {
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "flaws backup 2017.02.27"
                }
            ],
            "StorageTier": "standard",
            "SnapshotId": "snap-0b49342abd1bdcb89",
            "VolumeId": "vol-04f1c039bc13ea950",
            "State": "completed",
            "StartTime": "2017-02-28T01:35:12+00:00",
            "Progress": "100%",
            "OwnerId": "975426262029",
            "Description": "",
            "VolumeSize": 8,
            "Encrypted": false
        }
    ]
}
        
```

I dont know how to mount an ec2 snapshot instance yet, let's go for more hints

![image](https://github.com/user-attachments/assets/35aef6c7-0bf8-41d8-9986-4e43b5c081c9)

We are provided with an aws script. 

```
aws --profile YOUR_ACCOUNT ec2 create-volume --availability-zone us-west-2a --region us-west-2  --snapshot-id  snap-0b49342abd1bdcb89
```

Since we already have access keys , accountid and snapshot we'll go ahead and try this in our end

![image](https://github.com/user-attachments/assets/6f401125-8c1a-40ef-a816-e9841700edad)



