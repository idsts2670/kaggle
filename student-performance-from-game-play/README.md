>run the command below to build the docker image
docker build --rm -t {image name:tag} {path to Dockerfile}
--rm remove intermediate containers after a successful build
-t name and optionally a tag in the 'name:tag' format
```zsh
# https://docs.docker.com/engine/reference/commandline/build/
docker build --rm -t kaggle_env /Users/satoshiido/Documents/programming/kaggle/student-performance-from-game-play/
```

>run the command below to run the docker container
```zsh
docker run -it --rm --name kaggle_work_container kaggle_env
```

> click the icon "Remote Explorer" on the left side of vscode and connect to the container
```zsh
/Users/satoshiido/Documents/programming/kaggle/student-performance-from-game-play/Dockerfile
```