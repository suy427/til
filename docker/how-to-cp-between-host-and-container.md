# how to transfer(copy) a file or directory between docker container and host

## container -> host
* ```shell docker cp <container name>:<file path> <host's path>```

## host -> container
* ```shell docker cp <host file path> <container name>:<container's path>```

