#### Documents
```
https://www.youtube.com/watch?v=MlRkm2wXxp0
```

# Merge code proxy.go and main.go instead of run 2 main functions
Exported on 15/11/2023 15:51:58 [from Phind Agent](https://www.phind.com/agent?cache=clozik1vq000hjm08z51in4kl) - with [SaveMyChatbot](https://save.hugocollin.com)

#### User
```bash
I have 2 file in 1 source:
```

proxy.go package main

```go
import (
```

"context" "flag" "fmt"

```go
"net/http"

	log "github.com/sirupsen/logrus"

	"github.com/grpc-ecosystem/grpc-gateway/v2/runtime"
	"google.golang.org/grpc"
	"google.golang.org/grpc/credentials/insecure"
	"google.golang.org/grpc/grpclog"

	gw "main/proto/protogen" // Update
)

const (
	PROXY_PORT = 8088
)

var (
	// command-line options:
	// gRPC server endpoint
	grpcServerEndpoint = flag.String("grpc-server-endpoint", "localhost:8888", "gRPC server endpoint")
)

func run() error {
	ctx := context.Background()
	ctx, cancel := context.WithCancel(ctx)
	defer cancel()

	// Register gRPC server endpoint
	// Note: Make sure the gRPC server is running properly and accessible
	mux := runtime.NewServeMux()
	opts := []grpc.DialOption{grpc.WithTransportCredentials(insecure.NewCredentials())}
	err := gw.RegisterQemuHandlerFromEndpoint(ctx, mux, *grpcServerEndpoint, opts)
	if err != nil {
```

return err

```go
}

	// Start HTTP server (and proxy calls to gRPC server endpoint)
	return http.ListenAndServe(fmt.Sprintf(":%v", PROXY_PORT), mux)
}

// Implement proxy server to received message -\> Forward to grpc server
func main() {
	flag.Parse()
	log.Infof("Proxy Server is running at port %v...", PROXY_PORT)
	if err := run(); err != nil {
		grpclog.Fatal(err)
	}
}
```

main.go

package main

```go
import (
```

"fmt"

```bash
"main/proto"
	"main/proto/protogen"
```

"net"

```go
log "github.com/sirupsen/logrus"
	"google.golang.org/grpc"
)

const (
	GRPC_PORT = 8888
)

func main() {
	lis, err := net.Listen("tcp", fmt.Sprintf("0.0.0.0:%v", GRPC_PORT))
	if err != nil {
		log.Errorf("err while create listen %v", err)
	}

	s := grpc.NewServer()
	protogen.RegisterQemuServer(s, proto.Server{})

	log.Infof("gRPC Gateway is running at port %v...", GRPC_PORT)
	err = s.Serve(lis)

	if err != nil {
		log.Errorf("err while serve %v", err)
	}
}


I dont want when run I need to run 2 place: go run main.go and go run proxy/proxy.go, anyway to merge it
```



#### Phind
Yes, you can merge the two files into one and run both the gRPC server and the HTTP server in separate goroutines. Here's how you can do it:

```go
package main

import (
	"context"
	"flag"
	"fmt"
	"net"
	"net/http"

	log "github.com/sirupsen/logrus"

	"github.com/grpc-ecosystem/grpc-gateway/v2/runtime"
	"google.golang.org/grpc"
	"google.golang.org/grpc/credentials/insecure"
	"google.golang.org/grpc/grpclog"

	"main/proto"
	"main/proto/protogen"
)

const (
	GRPC_PORT  = 8888
	PROXY_PORT = 8088
)

var (
	// command-line options:
	// gRPC server endpoint
	grpcServerEndpoint = flag.String("grpc-server-endpoint", "localhost:8888", "gRPC server endpoint")
)

func runProxy() error {
	ctx := context.Background()
	ctx, cancel := context.WithCancel(ctx)
	defer cancel()

	// Register gRPC server endpoint
	// Note: Make sure the gRPC server is running properly and accessible
	mux := runtime.NewServeMux()
	opts := []grpc.DialOption{grpc.WithTransportCredentials(insecure.NewCredentials())}
	err := protogen.RegisterQemuHandlerFromEndpoint(ctx, mux, *grpcServerEndpoint, opts)
	if err != nil {
		return err
	}

	// Start HTTP server (and proxy calls to gRPC server endpoint)
	return http.ListenAndServe(fmt.Sprintf(":%v", PROXY_PORT), mux)
}

func runGrpc() error {
	lis, err := net.Listen("tcp", fmt.Sprintf("0.0.0.0:%v", GRPC_PORT))
	if err != nil {
		log.Errorf("err while create listen %v", err)
		return err
	}

	s := grpc.NewServer()
	protogen.RegisterQemuServer(s, proto.Server{})

	log.Infof("gRPC Gateway is running at port %v...", GRPC_PORT)
	return s.Serve(lis)
}

func main() {
	flag.Parse()

	go func() {
		log.Infof("Proxy Server is running at port %v...", PROXY_PORT)
		if err := runProxy(); err != nil {
			grpclog.Fatal(err)
		}
	}()

	if err := runGrpc(); err != nil {
		log.Errorf("err while serve %v", err)
	}
}
```

In this code, `runProxy` and `runGrpc` are the functions that start the HTTP and gRPC servers respectively. They are called in separate goroutines in the `main` function. The gRPC server is started in the main goroutine, and the HTTP server is started in a new goroutine. If any of the servers fail to start, the program will exit with an error message.



#### User
```go
Why runProxy put in go func()
```



#### Phind
The `go func()` statement is used to start a new goroutine, which is a lightweight thread managed by the Go runtime.

In your case, you want to run both the gRPC server and the HTTP server concurrently. If you were to call `runProxy()` and `runGrpc()` sequentially (one after the other), the second function would not start until the first one has completed. Since both `runProxy()` and `runGrpc()` are blocking operations (they do not return until the server stops), the second server would never start.

By putting `runProxy()` inside a `go func()`, you're telling Go to start executing that function in a new goroutine, allowing it to run concurrently with the rest of your program. This way, both servers can run at the same time.

The reason why `runGrpc()` is not put in a `go func()` is because if the main goroutine (the one that runs the `main()` function) finishes, all other goroutines are killed. So, to prevent the main goroutine from exiting and thus killing the goroutine running the HTTP server, we block the main goroutine by running the gRPC server in it.



