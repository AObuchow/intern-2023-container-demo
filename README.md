# Container Demo

A demo on containers for the Toronto internship program orientation.

Consists of a simple Go server with instructions on how to run it locally, and from within a container.

Credit goes to [Yu Qi (Jerry) Zhang](https://github.com/yuqi-zhang), from which this demo was [forked from](https://github.com/yuqi-zhang/intern-2022-demos).  

## Part 0: Setup

### Install Podman
We will be running our container with `podman`. If you do not have this locally (`which podman`) you can get it via `sudo dnf install podman`.

### Clone this repo

Clone this repo locally with `git clone https://github.com/AObuchow/intern-2023-container-demo`

## Part 1: Running the wbeserver using a pre-built container

To start, we'll demonstrate the ease of deploying a container across different environments. To do so, you'll be deploying a container that was built on a different computer than your own. 

To deploy the container on your system, run `podman run -it -p 8080:8080 quay.io/aobuchow/intern-demo-2023`. This will pull (download) a remote container image from [`quay.io/aobuchow/intern-demo-2023`](quay.io/aobuchow/intern-demo-2023), map the container's port `8080` to your `localhost:8080` and deploy the container locally. 

**Something to note:** you are now running a Go server without needing to have Go installed on your system! Containers package applications alongside their dependencies and configuration files so that they can be deployed anywhere in a uniform manner. 

To stop the running server, send a `SIGINT` by doing a CTRL+C in the terminal you used to run the deploy the container. 

## Part 2: Run the web server locally

Inside the [repo](https://github.com/AObuchow/intern-2023-container-demo) we have all the necessary bits to build the web server we deployed in Part 1. It is a simple frontend (`index.tmpl`) and golang backend `app.go` with the necessary `go.mod` and `go.sum` to build.

To build the web server locally:
1. Install Go: `sudo dnf install go`
2. Run the build script: `./build-local.sh`
3. Start the server: `./server`

Now if you go to [`http://localhost:8080/`](http://localhost:8080/) you can see the simple server running.

Once again, you can stop the running server by doing a CTRL+C in the terminal you used to start the server.

## Part 3: Build and run your own container

Now, lets build your own version of the container that you deployed in Part 1.

To do so, run `podman build -t localhost/go-server .`

Podman will build the container by executing the commands listed in the repo's [Dockerfile](./Dockerfile).

Next, run `podman run -it -p 8080:8080 localhost/go-server` to start it.

You can also push the image to a registry (though you'll have to make an account if you wish to use [quay.io](quay.io)), e.g.: `podman push localhost/go-server:latest quay.io/aobuchow/intern-demo-2023`. 

Stop the container by doing a CTRL+C in the terminal you used to run the deploy the container. 

Once you are done, you can see containers with `podman ps -a` and `podman rm <container-name>` the container.

### Bonus: re-map the container's server port to a different port on your local system
Instead of mapping the containers port `8080` to your system's port `8080`, you could map it to a different port on your system (for example, port `8081`).

Try this out by running `podman run -it -p 8081:8080 localhost/go-server`, and then accessing the web server on [`http://localhost:8081/`](http://localhost:8081/).

**Something to note:** the web server was implemented to run on port `8080`, but thanks to containers, we are able to access it on our system via port `8081`. There are many use cases and benefits to container port mapping. For example:
- Port `8080` may already be in use by another application on your system. Port mapping allows you to use another available port instead.
- You might not normally be able to easily change the port the application is intended to run on (e.g. the application is closed-source and there's no option to configure the port). Port mapping allows you to circumvent this limitation.
