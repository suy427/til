# how to transfer(copy) a file or directory between docker container and host

## container -> host
* ```docker cp <container name>:<file path> <host's path>```

## host -> container
* ```docker cp <host file path> <container name>:<container's path>```

