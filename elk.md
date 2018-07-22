### Logstash 命令行参数

+ -e 立即执行
```
input {
    stdin {}
}

output {
    stdout {}
}
```

+ --config，-f 通过指定配置文件运行
+ --configtest，-t 测试匹配文件是否能正常解析
+ --log， -l ：Logstash 默认输入日志到标准错误，可以通过此参数设置输出文件

```
logstash -l logs/logstash.log
```