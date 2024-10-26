# flop

[![GoDoc](https://godoc.org/github.com/riptano/flop?status.svg)](https://godoc.org/github.com/riptano/flop)
[![MIT license](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Mentioned in Awesome Go](https://awesome.re/mentioned-badge.svg)](https://github.com/avelino/awesome-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/riptano/flop)](https://goreportcard.com/report/github.com/riptano/flop)

<img src="https://github.com/riptano/flop/raw/master/doc/logo.png" width="200" title="design based on original gopher by Renee French">

----

flop aims to make copying files easier in Go, and is modeled after
[GNU cp](https://www.gnu.org/software/coreutils/manual/html_node/cp-invocation.html).
Most administrators and engineers interact with GNU utilities every day, so it makes sense to utilize
that knowledge and expectations for a library that does the same operation in code.

flop strategically diverges from cp where it is advantageous for the programmer to explicitly define the
behavior, like cp assuming that copying from a file path to a directory path means the file should be
created inside the directory. This behavior must be explicitly defined in flop by passing the option
AppendNameToPath, otherwise an error will be returned.

```BASH
go get -u github.com/riptano/flop
```

## Usage

Basic file copy.

```go
err := flop.SimpleCopy("src_path", "dst_path")
handle(err)
```

Advanced file copy with [options](https://pkg.go.dev/github.com/riptano/flop?tab=doc#Options).

```go
options := flop.Options{
    Recursive: true,
    MkdirAll:  true,
}
err := flop.Copy("src_path", "dst_path", options)
handle(err)
```

## Logging

flop won't throw logs at you for no reason, but if you want to follow along with what's going on giving it a logger
can help expose the behavior, or aid in debugging if you are generous enough to contribute.

```go
// the logger just takes a string so format your favorite logger to accept one
import (
	"github.com/riptano/flop"
	"github.com/rs/zerolog"
	zlog "github.com/rs/zerolog/log"
	llog "github.com/sirupsen/logrus"
)

func logDebug(msg string) {
	llog.WithFields(llog.Fields{
		"application": "stuffcopy",
	}).Info(msg)
}

func main() {
	zlog.Logger = zlog.Output(zerolog.ConsoleWriter{Out: os.Stderr})
	err := flop.Copy(src.Name(), dst.Name(), flop.Options{
		InfoLogFunc: zlog.Info().Msg,  // Msg already accepts a string so we can just pass it directly
		DebugLogFunc: logDebug,        // logrus Debug takes ...interface{} so we need to wrap it
	})
	handle(err)
}
```
