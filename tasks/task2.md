task2

11/05/2020 20:25

* * *
# Task2
* * *
- [x] Create container image that’s has Jenkins installed  using dockerfile 

- [x] When we launch this image, it should automatically starts Jenkins service in the container.

- [x] Create a job chain of job1, job2, job3 and  job4 using build pipeline plugin in Jenkins 

- [x] Job1 : Pull  the Github repo automatically when some developers push repo to Github.

- [x] Job2 : By looking at the code or program file, Jenkins should automatically start the respective language interpreter install image container to deploy code ( eg. If code is of  PHP, then Jenkins should start the container that has PHP already installed ).

- [x] Job3 : Test your app if it  is working or not.

- [x] Job4 : if app is not working , then send email to developer with error messages.

- [x] Create One extra job job5 for monitor : If container where app is running. fails due to any reson then this job should automatically start the container again.

* * * 
# MY VISUALS

![tas2-visual.jpeg](../_resources/1f958f95c8db481aad64ba94d6226e85.jpeg)

* * *
# MY SOLUTION
* * * 
## Check for internet connectivity
#Masquerading allows for docker ingress and egress
- firewall-cmd --zone=public --add-masquerade --permanent
- firewall-cmd --reload
#Restart docker
- systemctl restart docker

* * *
## Here the fun begins

- create a bridged network --> test2net

![8b9e4ef8917daee4c52865750075f6c8.png](../_resources/e10fc3ba1e65411f9fa77e38da204c50.png)

check it
![f016a3fd4a7ce57e34b3cd46105eb081.png](../_resources/d7e91ba895ec44e4b0421deff1ce4a74.png)



- build jenkins image
	- important link : https://www.jenkins.io/doc/pipeline/tour/getting-started/

docker fie content (added some of my personal scripts too)
![82a13d3f8f828a390cd1780d6c07857c.png](../_resources/d8eab40a250b4011a516c92d0fd90b0f.png)


```
docker build -t myjenkins:v1 -f "jenkins.dockerfile" .
```

- create jenkins container
	- mapped dot files (for initialsecrets, and plugins settings and data)
	- mapped gitclones files (files downloaded from github as persistent between storages)
	- mapped docker socket to container (to control docker daemon from inside the container)
	- attached to the network.

```
docker run -dit  -v /root/task2/dotjenkins/:/root/.jenkins/ -v gitclones:/gitclones/ -v /var/run/docker.sock:/var/run/docker.sock --network test2net  --name try_jenkins -p 8080:8080 myjenkins:v1
```

![1f06261913b262c94d27d6dd2704f2c5.png](../_resources/e382891fc39f4805990e4de0bcbe391e.png)


- jenkins jobs


create 5 jobs
![38bce0f490418d75163820008146316e.png](../_resources/2df2c2ea8e804ec9b93ae66c2a79f971.png)


build pipeline for 4 jobs
![94631b674357e1d14fa9841c527d924f.png](../_resources/e000825598c34774a4db0c754edf2008.png)

* * * 
- job1

download data from github whenever new version arrives
![08352209f866d04cc8013dffd02a8dbc.png](../_resources/3fc5ee0d86c24868b42177fd1a8ecadb.png)

(was trying for webhooks, BAD LUCK there -- so used POLL scm instead )

![87c13fd135ccbf09c7cf78d24572473e.png](../_resources/8490793184e942c19903734ef5dd7ef0.png)

save it in /gitclones (persistent storage)
![b9c695b2df961fb5666c24ddeef905fa.png](../_resources/805e14354f2b48f8befdbdbd3ef46b1b.png)



- job2

build after job1 is done
![bf0bdbfe3314a90af47152264703a10f.png](../_resources/1f1559c9221e45f898dfbe6429ca1142.png)

apply magicfile.py to gitclones directory (read the code below)
![26b935904172568d646bbe646853c844.png](../_resources/5806c346522940ecbeb3b8bfbae2ca01.png)

(`magicfile.py` -- not so magical afterall -- simple python tricks)

![293f1d4d53d51662336531140b95ecad.png](../_resources/da8762f7578f474888a9a95bc8f06a9c.png)

- job3

build after job2
![1bad87ed0fb2bd91b47b1a98d1d23563.png](../_resources/43a4d74cb346411098dadf0037e11430.png)

run checkstatus.py file (read code below)
![c874e390a6d07e33c2e515a0c061ff96.png](../_resources/c23692f1af9e471cb03f2fad2991a42a.png)

(check_status.py)

![c6e174b9ab43b01f82751b60b93e2afe.png](../_resources/19f919853f0c469cb0a81c6a4279368d.png)

- job4

build after job3
![2214a2cc949f7c451cde04d828a79b3a.png](../_resources/c1a7ec540784409bb50653e0b9c5351a.png)

check for /status file (if SEND MAIL in it)... If yes, then send mail to ris3234@gmail.com(developer mail id)
![e339ae431378098747cd9586af65dcae.png](../_resources/415e7d9f7acf4bbca830130cfaa12b9a.png)

(`sendmail.py` -- credentials hidden)

![9b40970692aaf0f2fa744d567cfcec37.png](../_resources/5f757291ccc947a7b1970860a378c2cd.png)


- job5

build after job4
![8d569ba2c29109c1698197265823c386.png](../_resources/3db9c09cbfeb4eb4bf5f5994d8f1532e.png)


will update the restart policy for every running container

![0e77f3bf93b66e8de99e9ace50950df4.png](../_resources/2a32ae0639144bdcb52b8bcc4a83d3cd.png)




* * *
SNAPSHOTS OF ALL FILES
* * * 

(`python.dockerfile`)

![a08d8eb0c58aa9a6842c9d97df3359f0.png](../_resources/6229c60dcf9648a6849710cece5595f8.png)


(`php.dockerfile`)
![45cfd706cea4d4963183d82c0fd70288.png](../_resources/b7a1a33c63b243e18601c019cc69dc1c.png)


(`magicfile.py`)
![927a324031d5f5a5844ebea45eb5b47f.png](../_resources/522c2218a524473db4965122b3fedc5e.png)


(`jenkins.dockerfile`)
![5d0c9f70f2a0d71be5b095f3607b9e46.png](../_resources/c071d57f71674bb49ca5aa72819b83f8.png)


(`docker.repo`)
![160c83f92d356726e6a79ec00d7ee229.png](../_resources/7a1e741aabb74f38bb2b8cb0ee01d31e.png)


(`check_status.py`)
![039ff575a388fb6d568870a5bd465d26.png](../_resources/49b4c8a240324befa9a2a81f4df7756e.png)


