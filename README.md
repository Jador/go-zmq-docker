Needed to be able to compile a go app that uses ØMQ for linux on OSX.
Hooray for Docker!

This image (~195MB) has the ability to compile go apps that depend on ØMQ (obviously).

GOPATH defaults to /go

## Usage

### Using a Volume

The simplest way to simply compile the binary and have it available on the host machine
is to use a `docker volume`

`docker run --rm=true -v $(pwd):/go/src/myapp -w /go/src/myapp jador/zeromq go get <zmq binding> && go build -o linux_amd64/${PWD##*/}`

To simplify this further, you could place your build steps into a shell script that is in your src directory
and cause that to run when you fire up the container

`docker run --rm=true -v $(pwd):/go/src/myapp -w /go/src/myapp jador/zeromq ./build.sh` 

### Using a Dockerfile

For a more complex build you could use a Dockerfile. However, if your aim is simply to produce the binary I suggest sticking with the
previous method

Example Dockerfile:
    
    FROM jador/go-zeromq
    WORKDIR /go/src/myapp
    
    # Copy go code over
    COPY .  ./
    RUN go get <your preferred zmq binding> && go build

An easy way to get the binary out is with `docker cp`

    $ docker build -t myapp .
    $ docker run -d myapp /bin/sh
    [container id] # You only need to make note of the first four characters of the hash
    $ docker cp <container id>:/go/src/myapp/myapp linux_amd64/
    $ docker stop <container id> # Don't forget to stop the container

## Notes
Currently this builds with Go 1.3.3 due to the fact that 1.3.3 is the version available in apk

This has only been tested with pebbe's [zmq4](https://github.com/pebbe/zmq4/)
