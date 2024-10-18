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

We need to use our own aws profile,

![image](https://github.com/user-attachments/assets/729e243f-23f4-4fb8-8be0-a04eb3686488)

Then we will log in to our own aws account via browser and check the volumes if successfully created under our account 

![image](https://github.com/user-attachments/assets/e5365ca9-ad92-464a-8b4e-cc3e3be0627c)

> If you dont see your volume, make sure that you are under the preferred region which is us-west-2
> ![image](https://github.com/user-attachments/assets/064a6887-b2da-4d14-a90b-4379b99588dc)
>

Then, we'll create a new ec2 instance, here are the settings of my ec2 instance, make sure that you change your subnet to the same as snapshot us-west-2a and create your pair

![image](https://github.com/user-attachments/assets/0c46a907-396b-4862-aeca-83034e1dd0e7)


I also enabled all connections to the ec2 instance and I used ubuntu as the default username

![image](https://github.com/user-attachments/assets/0f68436c-35ad-4845-bad2-7787f60eb3a2)

If everything is done correctly, we should be able to connect to our ec2 via ssh 

![image](https://github.com/user-attachments/assets/77b71732-f5f2-4ca3-9c34-063239e9ebd3)


Since this is a newly launched ec2 instance, it doesnt have anything on it yet, just enough files for it to run, we can also list the mounted drives first to check drive names

```
lsblk
```

![image](https://github.com/user-attachments/assets/8ec5b603-d933-4a04-b41f-0c709be3a954)

Now that we know the drives, we can now attach the snapshot volume we found earlier, to do that go back to our aws browser select your volume and then click `actions` and then `attach`

![image](https://github.com/user-attachments/assets/f9d57e58-3f32-4b50-9f19-a5a5de07d537)

After attaching, we should have 2 volumes listed 

![image](https://github.com/user-attachments/assets/ac892583-d6e7-4f14-8e93-8a0d6c049745)

and if we list all drives again we should see that another drive has been listed

![image](https://github.com/user-attachments/assets/52823b0b-3583-4e06-985a-570e4ec98628)


Now we will need to mount this volume using `mount` command, first we will create the mounting point 
```
ubuntu@ip-172-31-45-255:~$ sudo mkdir /mnt/new_volume

```
Then we will mount the volume
```
ubuntu@ip-172-31-45-255:~$ sudo mount /dev/xvdbq1 /mnt/new_volume

```

And then if we list all the files, we will see that the snapshot has been loaded  in the drive

![image](https://github.com/user-attachments/assets/35518900-44ea-434f-af4b-f1e32a2b8de1)

We can now do more enumeration and see what we can find, and checking the home folder we can see what looks like a username and password

![image](https://github.com/user-attachments/assets/f9ec1e9f-3cbf-49f6-befd-e1b5d1a672e8)
```
ubuntu@ip-172-31-45-255:/mnt/new_volume$ cat home/ubuntu/setupNginx.sh 
htpasswd -b /etc/nginx/.htpasswd flaws nCP8xigdjpjyiXgJ7nJu7rw5Ro68iE8M

```

We'll now try and log in using the credentials and we have solved the level.

![image](https://github.com/user-attachments/assets/1626f12b-3af2-4359-84b6-cb1c7f8b75ed)



![image](https://github.com/user-attachments/assets/b430a0f2-be2b-4e3e-910d-74f5fd0d1606)

Yey, congrats!
