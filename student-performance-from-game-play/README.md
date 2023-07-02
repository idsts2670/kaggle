

# how to connect docker with aws ec2

Go to the website (https://us-west-2.console.aws.amazon.com/ec2/home?region=us-west-2#Instances:) and launch the instance. 

Go to the folder where the Dockerfile is located and run the following commands to :
```zsh
ssh -i ~/.ssh/key-kaggle.pem ec2-user@ec2-35-83-194-69.us-west-2.compute.amazonaws.com
```

Start the docker in ECR and check if the image is there:
```zsh
sudo service docker start
docker image ls
```


# Pull the Docker image from ECR to EC2 by autehnticating docker client to a registry 

## how to push docker image to aws ECR
* Go to the website (https://us-west-2.console.aws.amazon.com/ecr/home?region=us-west-2#) and create the repository (click "Get started").
* Select the repository and click on the "View push commands" button.
* step 1: Copy the commands from step 1 and paste it in the terminal.
```zsh
# the command should look like this:
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin **********************************
```
* * You may receive the error message, "Unable to locate credentials. You can configure credentials by running "aws configure"." If so, run the following command:
```zsh
aws configure
# and then type the aws access key id, aws secret access key, and etc. respectively
```
* * And then, run the following command again:
```zsh
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin **********************************
```

* skip step 2 (the Docker building) since we already have the docker image in the ECR.

* step 3: After the build completes, tag your image so you can push the image to this repository:
```zsh
docker tag {your docker image name}:latest 594458320364.dkr.ecr.us-west-2.amazonaws.com/repo-kaggle:latest
# check the docker image 
docker image ls
```

* step 4: Run the following command to push this image to your newly created AWS repository:
```zsh
docker push 594458320364.dkr.ecr.us-west-2.amazonaws.com/repo-kaggle:latest
```

## building EC2 environment
* Go to the website (https://us-west-2.console.aws.amazon.com/ec2/home?region=us-west-2#Instances:) and launch the instance (create a new key-pair(.pem format) for SSH login. And make sure the IP address is fix by allocating Elastic IP addess and associating Elastic IP address with a exsiting instance).

* Connect Instance with SSH
* * Go to the website (https://us-west-2.console.aws.amazon.com/ec2/).
* * Select "EC2 Instance Connect (browser-based SSH connection)" and click on the "Connect" button.
* * Copy the command from the "Connect to instance" section with SSH client and paste it in the terminal. Make sure the key-pair is not publicly viewable for security reason.
```zsh
# see the key-pair in the .ssh folder
ssh -i ~/.ssh/key-kaggle.pem
# check the current key-pair status
ls -l .ssh/key-ml.pem
# run the command to protect the key-pair
chmod 400 ~/.ssh/key-kaggle.pem
# check the current key-pair status again
ls -l .ssh/key-ml.pem
# Use the example to connect to your instance using its Public DNS:
ssh -i ~/.ssh/key-ml.pem ec2-user@ec2-52-10-225-13.us-west-2.compute.amazonaws.com
# check with the command
ls
pwd
# Go to your working directory and type the same command above again
ssh -i ~/.ssh/key-ml.pem ec2-user@ec2-52-10-225-13.us-west-2.compute.amazonaws.com
```

* Install Docker on EC2 instance
```zsh
# Under the EC2 instance, run the following command to install docker
mkdir work
cd work
sudo yum update -y install docker
sudo service docker start
sudo usermod -a -G docker ec2-user
exit
# then, reboot the instance on the website (https://us-west-2.console.aws.amazon.com/ec2/).
# After the reboot, connect to the instance again by using the following command:
ssh -i ~/.ssh/key-ml.pem ec2-user@ec2-52-10-225-13.us-west-2.compute.amazonaws.com
sudo service docker start
docker image ls
```

## Pull the Docker image from ECR to EC2
* Go to the ECR webpage (https://us-west-2.console.aws.amazon.com/ecr/repositories?region=us-west-2)
* Select the repository and click on the "View push commands" button.
* step 1: Copy the commands from step 1 and paste it in the terminal.
```zsh
# the command should look like this:
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin **********************************
# You may receive the error message, "Unable to locate credentials. You can configure credentials by running "aws configure"." If so, run the following command:
aws configure
# and then type the aws access key id, aws secret access key, and etc. respectively
# And then, run the following command again:
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin **********************************
# You should see the message "Login Succeeded"
```
* To pull the docker from ECR to ec2, find the URI of the Image (should look like this: 5944*******.dkr.ecr.us-west-2.amazonaws.com/repo-kaggle:latest) for your repository in the ECR console first (https://us-west-2.console.aws.amazon.com/ecr/repositories?region=us-west-2). Then, run the following command:
```zsh
# Copy and paste it with docker pull command:
docker pull 5944*******.dkr.ecr.us-west-2.amazonaws.com/repo-kaggle:latest
# Check the docker image and and can see the image which we just pulled from aws ECR:
sudo service docker start
docker image ls
```
* Run the docker image on EC2 instance:
```zsh
docker run --name ml-image -it --rm -v ~/work:/workdir -p 8080:8080 ec59c782d5c3
# the terminal change into "(base) root@8cbc88f71374:/#" from [ec2-user@ip-172-31-20-30 ~]$. This means when we place some files on ec2, then that files will be placed on the work/ of docker as well. For example, when you create a file, it will be shown in the work/. Run the command below:
cd workdir/
touch test.txt
workdir# ls
exit
cd work/
ls

```



# what is ECR?
Amazon Elastic Container Registry (ECR)
Here used as the Container Registry for Docker Container
Through AWS CLI, we can push the docker image to ECR and pull the image from ECR to EC2 instance