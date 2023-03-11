<p align="center">
  <img src="https://user-images.githubusercontent.com/3843505/92512260-26878d80-f1d4-11ea-944d-73f3387f74e2.png" width="150" height="150" alt="sampo">
  <br>
  <strong>A shell script API server for running your shell scripts.</strong>
</p>

```
joukahainen:~$ curl -i http://localhost:1042/echo/rusty-fork
HTTP/1.1 200 OK
Date: Sun, 06 Sep 2020 16:54:45 UTC
Version: HTTP/1.1
Accept: text/plain
Accept-Language: en-US
Server: sampo/1.0.0
Content-Type: text/plain

rusty-fork
```

# Running The Pre-built Image

This is a quick way to see how sampo works.  Bundled into the image is an example config and several example scripts.

```bash
docker pull ghcr.io/jacobsalmela/sampo/sampo:1.0.0 # get the sampo image
docker run --rm -d --name sampo -p 1042:1042 ghcr.io/jacobsalmela/sampo/sampo:1.0.0 # run a detached container
curl http://localhost:1042/example # see an example shell script being executed from the API call
curl http://localhost:1042 # see a list of endpoints and the functions they call
```
## Customizing

If you are looking into this software, it quickly becomes apparent that you need to be able to drop in your own scripts and make your own endpoints.  You can do this by mounting a directory with `sampo.sh`, `sampo.conf`, and a directory named `scripts`, that holds all of the shell scripts you want to use.

```bash
docker pull ghcr.io/jacobsalmela/sampo/sampo:1.0.0
mkdir -p sampo/scripts
# get a copy of the script 
curl -o sampo/sampo.sh https://raw.githubusercontent.com/jacobsalmela/sampo/main/docker/sampo/sampo.sh
chmod 755 sampo/sampo.sh
curl -o sampo/sampo.conf https://raw.githubusercontent.com/jacobsalmela/sampo/main/docker/sampo/sampo.conf
chmod 644 sampo/sampo.conf
curl -o sampo/scripts/example.sh https://raw.githubusercontent.com/jacobsalmela/sampo/main/docker/sampo/scripts/example.sh
vim sampo/scripts/example.sh # make changes to the script as you desire
chmod 755 sampo/scripts/example.sh
docker run --rm -d --name sampo -p 1042:1042 -v ${PWD}/sampo:/sampo ghcr.io/jacobsalmela/sampo/sampo:1.0.0 # run a detached container mounting your local files over the example ones bundled in the container image
# make your own scripts/*.sh and add them to sampo.conf for endless possibilities
```

# Running Locally

Sampo also can run directly in your shell with the help of a listener like `socat` or `nc`:

```bash
mkdir -p sampo/scripts
# get a copy of the script 
curl -o sampo/sampo.sh https://raw.githubusercontent.com/jacobsalmela/sampo/main/docker/sampo/sampo.sh
chmod 755 sampo/sampo.sh
curl -o sampo/sampo.conf https://raw.githubusercontent.com/jacobsalmela/sampo/main/docker/sampo/sampo.conf
chmod 644 sampo/sampo.conf
curl -o sampo/scripts/example.sh https://raw.githubusercontent.com/jacobsalmela/sampo/main/docker/sampo/scripts/example.sh
vim sampo/scripts/example.sh # make changes to the script as you desire
chmod 755 sampo/scripts/example.sh
# choose one
socat TCP-LISTEN:1042,reuseaddr,pf=ip4,bind=127.0.0.1,fork system:sampo/sampo.sh # socat preferred
netcat -lp 1042 -e sampo/sampo.sh # version that supports '-e, --exec'
# make your own scripts/*.sh and add them to sampo.conf for endless possibilities
```

# Building And Running

You can also create your own image with the scripts bundled in.  Clone this repo and use the build script to see how it works.

## Running Locally With `socat`

```bash
./build.sh -l
```

## Running in Docker

```bash
./build.sh -d
```

## Running in Kubernetes

```bash
./build.sh -k
```

# How It Works

- `sampo.sh` listens for incoming requests
- `sampo.conf` is configured to user-defined endpoint that run user-defined shell scripts
- `scripts/` contains all of the user-defined scripts

# Details

Details can be found on [this blog post](https://jacobsalmela.com/2020/09/15/introducing-sampo-a-bash-api-server-that-runs-your-shell-scripts/).
