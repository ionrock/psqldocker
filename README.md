<img align="right" width="300" src="https://github.com/adrianbrad/psqldocker/blob/image-data/psql_docker.png?raw=true" alt="adrianbrad psqldocker">

# 🚢 psqldocker

powered by [`ory/dockertest`](https://github.com/ory/dockertest).

[![made-with-Go](https://img.shields.io/badge/Made%20with-Go-1f425f.svg)](https://go.dev/)
[![GitHub go.mod Go version of a Go module](https://img.shields.io/github/go-mod/go-version/adrianbrad/psqldocker)](https://github.com/adrianbrad/psqldocker)
[![GoDoc reference example](https://img.shields.io/badge/godoc-reference-blue.svg)](https://pkg.go.dev/github.com/adrianbrad/psqldocker)

[![CodeFactor](https://www.codefactor.io/repository/github/adrianbrad/psqldocker/badge)](https://www.codefactor.io/repository/github/adrianbrad/psqldocker)
[![Go Report Card](https://goreportcard.com/badge/github.com/adrianbrad/psqldocker)](https://goreportcard.com/report/github.com/adrianbrad/psqldocker)
[![lint-test](https://github.com/adrianbrad/psqldocker/workflows/lint-test/badge.svg)](https://github.com/adrianbrad/psqldocker/actions?query=workflow%3Alint-test)
[![codecov](https://codecov.io/gh/adrianbrad/psqldocker/branch/main/graph/badge.svg)](https://codecov.io/gh/adrianbrad/psqldocker)

---

Go package providing lifecycle management for PostgreSQL Docker instances.

Leverage Docker to run unit and integration tests against a real PostgreSQL database.

### Usage
#### Recommended: In a TestXxx function

```go
package foo_test

import (
	"testing"

	"github.com/adrianbrad/psqldocker"
)

func TestXxx(t *testing.T) {
    c, err := psqldocker.NewContainer(
        "user",
        "password",
        "dbName",
        psqldocker.WithContainerName("test"), 
        // initialize with a schema
        psqldocker.WithSql("CREATE TABLE users(user_id UUID PRIMARY KEY);"),
        // you can add other options here
    )
    if err != nil {
        t.Fatalf("cannot start new psql container: %s\n", err)
    }
	
    t.Cleanup(func() {
        err = c.Close()
        if err != nil {
            t.Logf("err while closing conainter: %w", err)
        }
    })
	
    t.Run("Subtest", func(t *testing.T) {
        
    })
}
```
---
#### In a TestMain function

```go
package foo_test

import (
	"log"
	"testing"

	"github.com/adrianbrad/psqldocker"
)

func TestMain(m *testing.M) {
    c, err := psqldocker.NewContainer(
        "user",
        "password",
        "dbName",
        psqldocker.WithContainerName("test"), 
        // initialize with a schema
        psqldocker.WithSql("CREATE TABLE users(user_id UUID PRIMARY KEY);"),
        // you can add other options here
    )
    if err != nil {
        log.Fatalf("cannot start new psql container: %s\n", err)
    }
	
    defer func() {
        err = c.Close()
        if err != nil {
            log.Printf("err while closing conainter: %w", err)
        }
	}() 
	
    m.Run()
}
```

