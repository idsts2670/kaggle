# kaggle

## how to connect docker with aws ec2

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


## Pull the Docker image from ECR to EC2 by autehnticating docker client to a registry 
Go to the website (https://us-west-2.console.aws.amazon.com/ecr/repositories?region=us-west-2)
Select the repository and click on the "View push commands" button.
Copy the commands from (1. Retrieve an authentication token and authenticate your Docker client to your registry.
Use the AWS CLI:) and paste it in the terminal.
```zsh
# the command should look like this:
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin **********************************
```

You may receive the error message, "Unable to locate credentials. You can configure credentials by running "aws configure"." If so, run the following command:
```zsh
aws configure
# and then type the aws access key id, aws secret access key, and etc. respectively
```

And then, run the following command again:
```zsh
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin **********************************
```

To pull the docker from ECR to ec2, find the URI of the Image for your repository in the ECR console first (https://us-west-2.console.aws.amazon.com/ecr/repositories?region=us-west-2). 
The URI shuold look like this:
```zsh
5944*******.dkr.ecr.us-west-2.amazonaws.com/repo-kaggle:latest
```
Copy and paste it with docker pull command:
```zsh
docker pull 5944*******.dkr.ecr.us-west-2.amazonaws.com/repo-kaggle:latest
```

Check the docker image and and can see the image which we just pulled from aws ECR:
```zsh
docker image ls
```

## Run the docker image on EC2 instance:
```zsh