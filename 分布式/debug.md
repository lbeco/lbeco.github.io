debug

dlv调试：

dlv --listen=:2345 --headless=true --api-version=2 --accept-multiclient exec

dlv --listen=:2345 --headless=true --api-version=2 --accept-multiclient exec ./mrcoordinator wc.so

go run -race mrworker.go wc.so

```shell
dlv --listen=:2345 --headless=true --api-version=2 --accept-multiclient exec  mrsequential -- wc.so pg*.txt
```

文件目录： /root/go/6.824/6.824/src/main

调试流程：

编译程序：

```shell
go build -race -buildmode=plugin ../mrapps/wc.go
go build -race mrcoordinator.go
go build -race mrworker.go
```

调试程序：

```shell
dlv --listen=:2345 --headless=true --api-version=2 --accept-multiclient exec mrcoordinator -- pg*.txt
dlv --listen=:2346 --headless=true --api-version=2 --accept-multiclient exec mrworker -- wc.so
ps -ef | grep dlv | awk '{ print $2 }' | xargs kill 
```

