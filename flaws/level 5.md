## Level 5
url: http://level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud/243f422c/
<kbd>
![image](https://github.com/user-attachments/assets/ff87639d-72f3-41d7-9a2d-ec12ebd99fc8)
</kbd>
Lab says that the level 6 link can be accessed when we used a proxy, we checked the level 6 link first and sure enough we get access denied
<kbd>
![image](https://github.com/user-attachments/assets/5f8ff2ea-d531-4221-9ca1-d53b90bd027f)
</kbd>
If you notice all three links have a directory `proxy` in it

>- http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/
>- http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/summitroute.com/blog/feed.xml
>- http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/neverssl.com/

If we  play around with it, the `proxy` redirects us to the a different location, to confirm,
notice the results of icanhazip.com it results to my ip, 

<kbd>
![image](https://github.com/user-attachments/assets/bcb95ce7-c823-4484-9828-6e3b5149b34f)
</kbd>


Then we will use this the icanhazip again this time using the our target. 

Notice that the IP address of the target is the one that showed up, this is like a server side request forgery (SSRF) we can issue make commands via the server
<kbd>
![image](https://github.com/user-attachments/assets/f6d0d92f-06c6-4339-972d-f6e4c5f68a35)
</kbd>
With this vulnerability in mind, Aws,Gcp and azure uses the ip address 169.254.169.254 (Magic IP) as their metadata intance address and should only be accessible using the server.
<kbd>
![image](https://github.com/user-attachments/assets/c9b93321-a6a4-455e-aa97-649e59e5eb64)
</kbd>
You can read more about the instances here if you want 


https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html

Since we have access to their server, if they did not disable the ipv4 access, we can get request for the intance using this server.

After trying the said Magic IP we are able to list the instance metadata 

`http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/`

I also transitioned to curl for easier enumeration I can just press the up arrow in my keyboard and then type there faster, and while enumerating I found this access keys
<kbd>![image](https://github.com/user-attachments/assets/23e7389a-0bce-42b8-bfab-6d05a1861e51)</kbd>

> - I woudn't recommend doing it in curl, because some character encoding is different when you access the keys from there, and I spent some time troubleshooting the issue


We can use this key to check what access we have, we will create another profile for this access, I used the comand below

```
┌──(aaron㉿kali)-[~/flaws/level6]
└─$ aws configure --profile level5
AWS Access Key ID [None]: ASIA6GG7PSQG3JI4EYHC
AWS Secret Access Key [None]: S3O6moArXchcbC1EkUzHNnU0VEXHqr9K2iZII3dh
Default region name [None]: 
Default output format [None]: 

```

However, to solve the level, we need to add another variable to our profile which is the aws_session_token variable, we add that by going in directly to the aws keys file location, you can use any editor you want
```
┌──(aaron㉿kali)-[~/flaws/level6]         
└─$ vim ~/.aws/credentials
1 [level5]
  2 aws_access_key_id = ASIA6GG7PSQGV34RQHKC
  3 aws_secret_access_key = S3O6moArXchcbC1EkUzHNnU0VEXHqr9K2iZII3dh
  4 aws_session_token = IQoJb3JpZ2luX2VjEOn//////////wEaCXVzLXdlc3QtMiJHMEUCICHzlVYTRyXmaSM1Ggxy1nFMIg9hsIXN8xq6OLKBzcNpAiEAlJQUl8D8eUgqI+LBOgFXNWcHn6B5O+ROmXu9L4puh5Qqs    gUIUhAEGgw5NzU0MjYyNjIwMjkiDI/akownn+kVn26ptiqPBbk1ua2V0EJYoQuld5LzUxkDl18PXVEpHYZLsSD0YHhtw8mYFrJU8CVmRQhkRrD2bQJjtdXUb8ajhQgXSTrGJma0Bto2Z8FFt4Ij2LSL9ipWwwHzDh2WEU    ykL8PjHiPXJfkvoM1low9s2JqWs59AD6oaU2rE5SZnggnW6isq889VcLH/AM4h6h5EXqXE+Sf3SE0J4J7VxF18rbVsapVPQT2+KOI9e27/ffdftQb8HSNCI42ULC73Eno6fQGKr6g0yxApVwpMq6Kj/9IPm4pwqwabj1L    ZDyK7dXiUGh8XZoBC/B1Ra/4icwEwV3whn+NrAXHnYEw+vv1gKEFx6ZZe/bbXI/SBi45hh9cnkRwR7j47OimqLRlWqQOk7dv+HIw/vqIrZdAiXUXYxWFweNK6ZXPAsmbgJl8/xiW8Fk3oupkJ6KD80nIpg4TKjFrxTHhV    j48FtQykv3yFRp4zpP9SnQ0/UdJyr7CGAsc8rMegQDZFozoguqkSRiMWyRHrx93WPs5/rSDzBALVUzl7/siR9QfrktuRPluv7K+UlgKdaZoP93PRXz4jQa8cuf4MHu5fPo2wiJudR7aFw4dSIyfPsDtvd4w0Edeok7Mbx    fTTWBD9owLsTmP6gV0WSH+oE4uDyaJFgULH2eh3hBJr/PHReZsrezs3g9wrZrtFk3BeP9NPQcPJEgJAQ/hz5Egisr9AqFwMOv5qpia/i4IV6r0HMk/EdIlAxzYPc/TcksYqAhLrybF03XEg5DxKtRG0D57p8FrBjdQE4i    hPQm72zutEreBIjk4kFZpZpJLr2pOAHCRRCva3wN6S8Zu+9BXYMN6XsEGvaGbTxl4Nir5eZ9RTtdKg2SvIiFKK+t98/pbcnR4w8IDMuAY6sQGQq08uh++HS8fSpF/pqkmNXvjLD2uCp23BFJ+CIhbC4PWE0apMHoI2bOR    3KfAPzJb2lyPfFS2na7pd+Rye2VbOXEJ1YQbGmbw1V0aXeFAtc2YwZm9kQzckNfY8H23aP4JkTrTCQ2FpOsZS9zTz1ayUrDnDGhseB8tlXHL6+1au2Z2K6R2eO6M9pF1fj688pNKpiCTQnsDt3/hG/ULRhrsNOtZQ7uyc    8t1B9K3q9LhJ8c0=
~
```
<kbd>![image](https://github.com/user-attachments/assets/4c847d7d-889c-4289-a198-b80cab2bc5f5)</kbd>

And if we try to list the s3, we can now see the hidden directory, we can conclude that it is directory because of the `/` at the end of the name


<kbd>![image](https://github.com/user-attachments/assets/3b66b6d9-7eaf-4827-97d6-3d1169191587)</kbd>

And if we list the directories we can see this

![image](https://github.com/user-attachments/assets/3b552212-ba9e-4aca-bc69-630b88b9ced4)

All we need to do now is to render the hidden directory in the browser and we can go ahead and proceed to the final challenge


![image](https://github.com/user-attachments/assets/bfb64361-52c5-4ef0-8dfb-30b4577b21a5)


