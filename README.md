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

# Running Locally With `socat`

```bash
./build.sh -l
```

# Running in Docker

```bash
./build.sh -d
```

# Running in Kubernetes

```bash
./build.sh -k
```

# How It Works

- `sampo.sh` listens for incoming requests
- `sampo.conf` is configured to user-defined endpoint that run user-defined shell scripts
- `scripts/` contains all of the user-defined scripts

# Details

Details can be found on [this blog post](https://jacobsalmela.com/2020/09/15/introducing-sampo-a-bash-api-server-that-runs-your-shell-scripts/).
