---
layout: post
title:  "Dockerize & Deploy"
date:   2019-07-22 17:45:00 +0530
categories: Docker AWS CI/CD Deployment Cloud
---
Let’s learn how to dockerize your node application and then deploying it to AWS so that it can be accessible to everyone around the globe.

<img src="https://miro.medium.com/max/700/1*ovRuAuqPf4r2xpiWh71rUg.png" class="center">

This article has some prerequisites:
1. You should be having a good understanding of how to make Node.js apps.
2. You should know how to use GitHub (Basics)
3. A basic understanding of what Docker is.

Oh, you have these, then you’re good to go.

Let me give a basic definition of Docker. In a nutshell, docker is that man who takes your application and then helps you to make it deliver to the world without any unnecessary problems like “It works fine in my system”.
So, docker is a tool which makes it easier to create, deploy and run the applications (images)by making containers (instances).  Basically, a container is an instance of your image (which is your application when pushed to Docker Hub).

Let’s move forward now, just open any of your Node.js application and follow the steps.

Step 1: Making a Dockerfile

Create a new file in your app directory and name it as “Dockerfile”.

```javascript
FROM node:alpine as builder

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

CMD ["npm" ,"run", "start"]
```

In the first line, we are pulling an image from Docker on which our app will work. I’ve taken node:alpine so that it gets npm and node preinstalled.
Then, I’ve set the working directory of the container. Then, copy the package.json file to the directory i.e. /app. And, then running npm install to install all the modules in the container. After that, giving it a command to run the application.
After this, just post your application to a GitHub Repository.
That’s the end of Development phase!

Step 2: Configuring TravisCI with our GitHub repository

Create a new file in your app directory and name it as “.travis.yml”.
Type the following code in it:

```javascript
language: node_js
node_js: node
sudo: required
services:
  - docker

script:
  - docker build -t priyeshs2/sample .

deploy:
  provider: elasticbeanstalk
  region: "ap-south-1"
  app: "docker-sample"
  env: "Docker"
  access_key_id: 
    secure: "$AWS_ACCESS_KEY"
  secret_access_key:
    secure: "$AWS_SECRET_KEY"
  bucket-name: "elasticbeanstalk-ap-south-1-980064312340"
  path: "docker-sample"
  on:
    branch: master
  skip_cleanup: true
```

This is how a Travis file looks. The name of the file should be “.travis.yml” only because the CI looks for this file in your repository.
Now in this file, first you have to tell about the environment you are using. I am having a NodeJS application so I mentioned env language as nodejs. After that mention docker in your services and then your build command under script tag. Leave the rest of it, I’ll explain it after telling how to setup ElasticBeanstalk service on Amazon.

Step 3: Creating a new application on AWS Beanstalk

Sign in to your AWS account and search for ElasticBeanstalk in the search menu and then click on “Create new application” option.

<img src="https://miro.medium.com/max/700/1*W1rdd3WRz_atHdv3CEPkTw.png" class="center">

<img src="https://miro.medium.com/max/700/1*ObDkYF_Sej_m8sApT3pSxA.png" class="center">

Enter all the required details and also choose Docker as a platform in AWS Elastic Beanstalk. Now, an application is created on Elastic Beanstalk.

Now, you have to give some permissions to TravisCI for it be able to access and deploy the code to the application that you just created.

Go to the search bar and search for the IAM Management Console.
Create a new user and then create a group which should be having permissions of full access to ElasticBeanstalk applications. It should be something like this:

<img src="https://miro.medium.com/max/700/1*pa25Yg83xXfNAnw645xMrg.png" class="center">

After giving the permissions it will give a confirm button to create a new user and then after that, AWS Access Key and Key ID will be shown. These are the IDs through which your user(TravisCI) will access the application.
Go to your TravisCI project and then go to settings and there will be an option to set environment variables. Create two variables and enter the given IDs and just click on the ADD button. Now your keys are safe and encrypted. We did this because, we have a public repository on GitHub and we don’t want anyone else to access our application on AWS, so we give the IDs to someone responsible here 
❤ TravisCI ❤.

<img src="https://miro.medium.com/max/700/1*mX9qzCrGzS-UGlPz2BaByg.png" class="center">

Now, let’s get back to our Travis file and understand the deploy stuff there.

```javascript
language: node_js
node_js: node
sudo: required
services:
  - docker

script:
  - docker build -t priyeshs2/sample .

deploy:
  provider: elasticbeanstalk
  region: "ap-south-1"
  app: "docker-sample"
  env: "Docker"
  access_key_id: 
    secure: "$AWS_ACCESS_KEY"
  secret_access_key:
    secure: "$AWS_SECRET_KEY"
  bucket-name: "elasticbeanstalk-ap-south-1-980064312340"
  path: "docker-sample"
  on:
    branch: master
  skip_cleanup: true
```

Under deploy option, you have to mention your provider, then region (the region is available in your application URL, copy it from there)and then your app name, the application name in AWS.
After that you have to enter the environment, then your secret IDs and then the bucket name, which you can find under S3 option on AWS. Now, we want TravisCI to update changes from the master branch so we have mentioned it and then a mandatory line to clean up the stack left after the building process.
This was our Travis File.

Now, one last simple step, that you have to do. Just push your application to GitHub repository which contains the .travis.yml file.
Now just go to the URL provided in the AWS ElasticBeanstalk application.

Umm..what it’s not working? Giving an nginx 502 bad gateway error !!
Nothing to worry about, we just have to add, one line of code to our Dockerfile. Well, you should remember that when we run a container, we map the ports using -p tag. Well, it’s basically the exposing of ports.

So, just add this line to your Dockerfile:

```javascript
EXPOSE 8081
```

Push it to your GitHub repo, wait for TravisCI to run the processes, and then visit the URL again. Your app should be working fine now. Congratulations ❤

Note: If you’re just trying out an application on ElasticBeanstalk and it’s not for production use, then after testing, just terminate the application by clicking on the Actions button on the right top. If, not, then you’ll be giving some amount of money out of your free tier for your sample experiment.

<img src="https://miro.medium.com/max/700/1*zcWonvLOUpwcHrDoTlaTvw.png" class="center">

Well, if you have followed each step, and executed it successfully, then you are a learner, you are the best ❤.

If this article really helped you, please give a clap for it. In case of any doubts, please write a response to it, or you can also contact me directly on contact@priyesh.dev.

Here are some helpful links:

Link to my GitHub repo: [github repo](https://github.com/codejockey02/AapasKiBaat-AnonymousChatApp-)

Docker official docs: [official docs](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

TravisCI official docs: [official docs](https://docs.travis-ci.com/user/docker/)

TravisCI deploy docs: [official docs](https://docs.travis-ci.com/user/deployment/elasticbeanstalk/)
