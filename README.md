# ZIO with SFTP - FTP / FTPS

thin wrapper over ftp and sftp client, which wrap with ZIO libraries

[![Build Status](https://circleci.com/gh/zio/zio-ftp.svg?style=svg&circle-token=???)](https://circleci.com/gh/zio/zio-ftp)
[![codecov](https://codecov.io/gh/zio/zio-ftp/branch/master/graph/badge.svg)](https://codecov.io/gh/zio/zio-ftp)
[![Maven Central](https://img.shields.io/maven-central/v/com.github.zio/zio-ftp_2.12.svg)](http://search.maven.org/#search%7Cga%7C1%7Czio-ftp) 

Setup
-----

```
//support scala 2.12 / 2.13

libraryDependencies += "dev.zio" %% "zio-ftp" % "0.1.0"
```

How to use it ?
---

* FTP / FTPS
```scala
import zio.blocking.Blocking
import zio.ftp.FtpClient._
import zio.ftp.Settings._

// FTP
val settings = FtpSettings("127.0.0.1", 21, FtpCredentials("foo", "bar"))
// FTP with ssl (FTPS)
val settings = FtpSettings.secure("127.0.0.1", 21, FtpCredentials("foo", "bar"))

//listing files
connect(settings).use{
  _.ls("/").runCollect
}.provide(Blocking.Live)

```

* SFTP (support ssh key)

```scala
import zio.blocking.Blocking
import zio.ftp.FtpClient._
import zio.ftp.Settings._

val settings = SFtpSettings("127.0.0.1", 22, FtpCredentials("foo", "bar"))

//listing files
connect(settings).use{ 
  _.ls("/").runCollect
}.provide(Blocking.Live)
```

Support any commands ?
---

If you need a method which is not wrapped by the library, you can have access to underlying FTP client in a safe manner by using

```scala
trait FtpClient[+A] {
  def execute[T](f: A => T): ZIO[Blocking, Throwable, T]
} 
```

All the call are safe since the computation will be executed in the blocking context you will provide

```scala
import zio.ftp.FtpClient._
import zio.ftp.Settings._

val settings = SFtpSettings("127.0.0.1", 22, FtpCredentials("foo", "bar"))

connect(settings).use{
  _.execute(_.version())
}
``` 



