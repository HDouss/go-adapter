<img src="https://www.artipie.com/logo.svg" width="64px" height="64px"/>

[![EO principles respected here](https://www.elegantobjects.org/badge.svg)](https://www.elegantobjects.org)
[![DevOps By Rultor.com](http://www.rultor.com/b/artipie/go-adapter)](http://www.rultor.com/p/artipie/go-adapter)
[![We recommend IntelliJ IDEA](https://www.elegantobjects.org/intellij-idea.svg)](https://www.jetbrains.com/idea/)

[![Build Status](https://img.shields.io/travis/artipie/go-adapter/master.svg)](https://travis-ci.org/artipie/go-adapter)
[![Javadoc](http://www.javadoc.io/badge/com.artipie/goproxy.svg)](http://www.javadoc.io/doc/com.artipie/goproxy)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/artipie/goproxy/blob/master/LICENSE.txt)
[![Hits-of-Code](https://hitsofcode.com/github/artipie/go-adapter)](https://hitsofcode.com/view/github/artipie/go-adapter)
[![Maven Central](https://img.shields.io/maven-central/v/com.artipie/goproxy.svg)](https://maven-badges.herokuapp.com/maven-central/com.artipie/goproxy)
[![PDD status](http://www.0pdd.com/svg?name=artipie/go-adapter)](http://www.0pdd.com/p?name=artipie/go-adapter)

This Java library turns your storage
(files, S3 objects, anything) with Go sources into
a Go repository.

Similar solutions:

  * [Artifactory](https://www.jfrog.com/confluence/display/RTF/Go+Registry)

Some valuable references:

  * [Module proxy protocol](https://golang.org/cmd/go/#hdr-Module_proxy_protocol)
  * [Why you should use a Go module proxy](https://arslan.io/2019/08/02/why-you-should-use-a-go-module-proxy/)
  * [Go Modules Are Awesome, But There Is One Tiny Problem](https://jfrog.com/blog/go-modules-are-awesome-but-there-is-one-tiny-problem/)

This is the dependency you need:

```xml
<dependency>
  <groupId>com.artipie</groupId>
  <artifactId>go-adapter</artifactId>
  <version>[...]</version>
</dependency>
```

Then, you implement `com.artipie.goproxy.Storage` interface
and pass it to the instance of `Goproxy`. Then, you
let it know when is the right moment to update certain artifact:

```java
Goproxy goproxy = new Goproxy(storage);
goproxy.update("example.com/foo/bar", "0.0.1");
```

Read the [Javadoc](http://www.javadoc.io/doc/com.artipie/goproxy)
for more technical details.

## How it works?

It is assumed that package sources are located in the repository
by their package names. For example, if you have two packages, they
may be located like this:

```
/foo
  /first
    go.mod
    foo.go
    LICENSE.txt
/bar
  /second
    go.mod
    bar.go
```

Then, when you are ready to release a new version `0.0.1`, you call
`update("example.com/foo/first", "0.0.1").blockingAwait()` or `update("example.com/foo/first", "0.0.1").subscribe()` for async execution.
Four new files will be created:

```
/example.com
  /foo
    /first@v0.0.1
      list
      v0.0.1.zip
      v0.0.1.mod
      v0.0.1.info
```

These files are needed for Go to understand that the package is ready
to be used.
There are samples of these files from Google repository:
[`.mod`](https://proxy.golang.org/github.com/liujianping/ts/@v/v0.0.7.mod),
[`.info`](https://proxy.golang.org/github.com/liujianping/ts/@v/v0.0.7.info),
[`.zip`](https://proxy.golang.org/github.com/liujianping/ts/@v/v0.0.7.zip),
and
[`list`](https://proxy.golang.org/github.com/liujianping/ts/@v/list).

When you decide to release another version, three additional files will
be created when you call `update("example.com/foo/first", "0.0.2").blockingAwait()`:

```
/example.com
  /foo
    /first@v0.0.1
      list
      v0.0.1.zip
      v0.0.1.mod
      v0.0.1.info
      v0.0.2.zip
      v0.0.2.mod
      v0.0.2.info
```

The file `list` will be updated.

## How to contribute

Fork repository, make changes, send us a pull request. We will review
your changes and apply them to the `master` branch shortly, provided
they don't violate our quality standards. To avoid frustration, before
sending us your pull request please run full Maven build:

```
$ mvn clean install -Pqulice
```

To avoid build errors use Maven 3.2+.
