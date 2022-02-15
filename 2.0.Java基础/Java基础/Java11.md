## Flight Recoder飞机黑盒子

Flight Recorder以前是商业版的特性，在java11当中开源出来，它可以导出事件到文件中，之后可以用Java Mission Control来分析。可以在应用启动时配置java -XX:StartFlightRecording，或者在应用启动之后，使用jcmd来录制；

JFR 的性能开销最大不超过 1%，所以，工程师可以基本没有心理负担地在大规模分布式的生产系统使用，

```shell
$ jcmd <pid> JFR.start

$ jcmd <pid> JFR.dump filename=recording.jfr

$ jcmd <pid> JFR.stop
```

