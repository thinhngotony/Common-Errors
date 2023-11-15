# Fix bug can't export .gw.go files with golang and gRPC gateway
Exported on 15/11/2023 11:46:09 [from Phind Agent](https://www.phind.com/agent?cache=cloz7z5gz000qif0887vwi26a) - with [SaveMyChatbot](https://save.hugocollin.com)

#### User
```javascript
I have problem with ~/.config/fish/config.fish
```

In that I have this

```bash
# Fix GO env variable PATH got messed up
set -x PATH /usr/local/go/bin $PATH /usr/bin /bin

=\> With above statement, I can use go in fish but I cannot export grpc gateway

But when I use this command:
export PATH=$PATH:$(go env GOPATH)/bin

It help me to export grpc gateway, example services_proxmox.pb.gw.go but when I check go it not exist anymore, for example:
```

❯ go

```csharp
Command 'go' not found, but can be installed with:
sudo snap install go         # version 1.21.4, or
sudo apt  install golang-go  # version 2:1.20~0ubuntu1
sudo apt  install gccgo-go   # version 2:1.20~0ubuntu1
```

See 'snap info go' for additional versions.



#### Phind - ASK USER

**question:** Could you please clarify what error messages you are seeing when you try to export the grpc gateway? Also, could you provide more details about your current environment setup, such as the version of Go and grpc you are using?



#### User
Error when try to export protoc --go\_out=plugins=grpc:proto/protogen -I proto/ proto/\*.proto --grpc-gateway\_out=logtostderr=true:./proto protoc-gen-grpc-gateway: program not found or is not executable Please specify a program using absolute path or make sure the program is available in your PATH system variable --grpc-gateway\_out: protoc-gen-grpc-gateway: Plugin failed with status code 1.

Version grpc: \_ "github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway" \_ "github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2" \_ "google.golang.org/grpc/cmd/protoc-gen-go-grpc" \_ "google.golang.org/protobuf/cmd/protoc-gen-go"



#### Phind - SEARCH

**query:** how to correctly set PATH for grpc gateway in fish shell
**second query:** protoc-gen-grpc-gateway: program not found or is not executable solution



#### Phind
It seems like you are trying to set up the PATH for gRPC Gateway in the Fish shell, but you are encountering issues with the `protoc-gen-grpc-gateway` executable not being found. The problem arises from the fact that when you run the export command, it doesn't persist across sessions and may be overwriting your PATH instead of appending to it.

Here's a step-by-step solution to your problem:

1.  First, make sure the `protoc-gen-grpc-gateway` is installed properly. You can do this by running the command:

```bash
go install github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway@latest
```

This command installs the `protoc-gen-grpc-gateway` plugin in your $GOPATH/bin directory [stackoverflow.com](https://stackoverflow.com/questions/60578892/protoc-gen-go-grpc-program-not-found-or-is-not-executable).

2.  After the plugin is installed, you need to add the $GOPATH/bin directory to your PATH. In the Fish shell, you can use the `fish_add_path` function to do this:

```bash
fish_add_path (go env GOPATH)/bin
```

The `fish_add_path` function adds a directory to your PATH and automatically handles the persisting of this addition across sessions [fishshell.com](https://fishshell.com/docs/current/cmds/fish_add_path.html).

3.  After these steps, you can verify that the `protoc-gen-grpc-gateway` is in your PATH by running:

```bash
which protoc-gen-grpc-gateway
```

This command should return the full path to the `protoc-gen-grpc-gateway` executable.

4.  Now, you should be able to run the `protoc` command to generate your grpc gateway without encountering the `protoc-gen-grpc-gateway: program not found or is not executable` error.

Remember that in the Fish shell, the `export` command does not exist. Instead, you should use the `set` command to set environment variables. If you want the variables to be exported (i.e., available to subprocesses), you need to use the `-x` or `--export` flag [dev.to](https://dev.to/nabbisen/fish-shell-customize-path-80k).
___
**Sources:**
- [(2) fish\_add\_path - add to the path — fish-shell 3.6.1 documentation](https://fishshell.com/docs/current/cmds/fish_add_path.html)
- [(6) Fish shell: Customize $PATH - DEV Community](https://dev.to/nabbisen/fish-shell-customize-path-80k)
- [(18) protoc-gen-go-grpc: program not found or is not executable](https://stackoverflow.com/questions/60578892/protoc-gen-go-grpc-program-not-found-or-is-not-executable)

**All search results:**
- [(1) Modifying PATH with fish shell - Stack Overflow](https://stackoverflow.com/questions/26208231/modifying-path-with-fish-shell)
- [(2) fish\_add\_path - add to the path — fish-shell 3.6.1 documentation](https://fishshell.com/docs/current/cmds/fish_add_path.html)
- [(3) What is the right way to set PATH variables in fish : r/fishshell - Reddit](https://www.reddit.com/r/fishshell/comments/gmr18t/what_is_the_right_way_to_set_path_variables_in/)
- [(4) path - manipulate and check paths — fish-shell 3.6.1 documentation](https://fishshell.com/docs/current/cmds/path.html)
- [(5) An all-in-one guide to gRPC-Gateway - LogRocket Blog](https://blog.logrocket.com/guide-to-grpc-gateway/)
- [(6) Fish shell: Customize $PATH - DEV Community](https://dev.to/nabbisen/fish-shell-customize-path-80k)
- [(7) laravel - Path setup in Fish shell - Stack Overflow](https://stackoverflow.com/questions/45676288/path-setup-in-fish-shell)
- [(8) Tutorial — fish-shell 3.6.1 documentation](https://fishshell.com/docs/current/tutorial.html)
- [(9) Add a relative path to $PATH on fish startup - Stack Overflow](https://stackoverflow.com/questions/7064053/add-a-relative-path-to-path-on-fish-startup)
- [(10) set - display and change shell variables - fish](https://fishshell.com/docs/current/cmds/set.html)
- [(11) Temporarily Set $PATH for a fish Shell Session From a Script](https://unix.stackexchange.com/questions/429043/temporarily-set-path-for-a-fish-shell-session-from-a-script)
- [(12) GOPATH environment variable not getting set in the Fish shell?](https://stackoverflow.com/questions/58514516/gopath-environment-variable-not-getting-set-in-the-fish-shell)
- [(13) Modifying PATH with fish shell | Gang of Coders](https://www.gangofcoders.net/solution/modifying-path-with-fish-shell/)
- [(14) go - protoc generated grpc files and gateway files end up in different ...](https://stackoverflow.com/questions/66171590/protoc-generated-grpc-files-and-gateway-files-end-up-in-different-paths)
- [(15) proto grror when generating gateway protoc-gen-grpc-gateway: program ...](https://stackoverflow.com/questions/74498464/proto-grror-when-generating-gateway-protoc-gen-grpc-gateway-program-not-found-o)
- [(16) error:protoc-gen-grpc-gateway: program not found or is not executable ...](https://github.com/grpc-ecosystem/grpc-gateway/issues/205)
- [(17) cmd - Why do I get: "--grpc\_out: protoc-gen-grpc: The system cannot ...](https://stackoverflow.com/questions/53158493/why-do-i-get-grpc-out-protoc-gen-grpc-the-system-cannot-find-the-path-spec)
- [(18) protoc-gen-go-grpc: program not found or is not executable](https://stackoverflow.com/questions/60578892/protoc-gen-go-grpc-program-not-found-or-is-not-executable)
- [(19) protoc-gen-grpc: program not found or is not executable #1 - GitHub](https://github.com/faaxm/exmpl-cmake-grpc/issues/1)
- [(20) protoc-gen-grpc-gateway - Go Packages](https://pkg.go.dev/github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway)
- [(21) GitHub - grpc-ecosystem/grpc-gateway: gRPC to JSON proxy generator ...](https://github.com/grpc-ecosystem/grpc-gateway)
- [(22) How to Fix protoc-gen-go-grpc: program not found or is not executable](https://askgolang.com/how-to-fix-protoc-gen-go-grpc-program-not-found-or-is-not-executable/)
- [(23) protoc-gen-go-grpc: program not found or is not executable when using ...](https://github.com/golang/protobuf/issues/1053)
- [(24) protoc-gen-grpc-gateway: program not found or is not executable](https://www.grepper.com/answers/486898/protoc-gen-grpc-gateway%3A+program+not+found+or+is+not+executable)
- [(25) gRPC and Plugin support in CMake - Falko's Blog](https://www.f-ax.de/dev/2020/11/08/grpc-plugin-cmake-support.html)
- [(26) Can some one please help me get past this error? : r/grpc - Reddit](https://www.reddit.com/r/grpc/comments/106hcvt/can_some_one_please_help_me_get_past_this_error/)
- [(27) Getting below error while the executing the protoc-gen.cmd file:-](https://medium.com/@siddelhi1/getting-below-error-while-the-executing-the-protoc-gen-cmd-file-eab07bfbf24)
- [(28) protoc-gen-objcgrpc: program not found or is not executable - Google Groups](https://groups.google.com/g/grpc-io/c/wosw_PPcwBI)


#### User
❯ make

```bash
protoc --go_out=plugins=grpc:proto/protogen -I proto/ proto/*.proto --grpc-gateway_out=logtostderr=true:./proto
protoc-gen-go: unable to determine Go import path for "rpc_clone_vm.proto"

Please specify either:
        • a "go_package" option in the .proto source file, or
```

• a "M" argument on the command line.

```bash
See https://protobuf.dev/reference/go/go-generated#package for more information.

--go_out: protoc-gen-go: Plugin failed with status code 1.
make: *** [Makefile:3: gen-proto] Error 1
~/Desktop/Projects/proxmox   master  ~3 -3                                                                                                                                        32ms  thinhngotony  fish 
❯ which protoc-gen-grpc-gateway

/home/thinhngotony/go/bin/protoc-gen-grpc-gateway
```



#### Phind - SEARCH

**query:** how to specify go\_package option in .proto source file
**second query:** protoc-gen-go: unable to determine Go import path solution



#### Phind
The error message is indicating that `protoc-gen-go` is unable to determine the Go import path for your `.proto` file. To resolve this issue, you need to specify the `go_package` option in your `.proto` file.

The `go_package` option specifies the import path for the Go package that will be generated by the `protoc-gen-go` plugin. This import path is used to determine the location of the generated Go file and also to create import statements in other generated Go files that refer to this package.

Here's how you can specify the `go_package` option in your `.proto` file:

```protobuf
syntax = "proto3";

option go_package = "github.com/yourusername/yourrepository/yourpackage";
```

In the `go_package` option, replace `github.com/yourusername/yourrepository/yourpackage` with the actual import path of your Go package.

After specifying the `go_package` option, you should be able to run the `protoc` command to generate your gRPC gateway without encountering the `protoc-gen-go: unable to determine Go import path` error.

If you want to specify the Go import path on the command line when invoking the compiler, you can pass a `M${PROTO_FILE}=${GO_IMPORT_PATH}` flag. Here's an example:

```bash
protoc --proto_path=src \
  --go_opt=Mproto/buzz.proto=github.com/yourusername/yourrepository/yourpackage \
  proto/buzz.proto
```

In the `M${PROTO_FILE}=${GO_IMPORT_PATH}` flag, replace `proto/buzz.proto` with your `.proto` file and `github.com/yourusername/yourrepository/yourpackage` with the actual import path of your Go package [stackoverflow.com](https://protobuf.dev/reference/go/go-generated#package).



