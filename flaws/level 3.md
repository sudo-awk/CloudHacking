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
