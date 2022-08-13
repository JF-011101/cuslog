# cuslog

## 功能说明
该日志包主要实现以下几个功能：

- 支持自定义配置。
- 支持文件名和行号。
- 支持日志级别 Debug、Info、Warn、Error、Panic、Fatal。
- 支持输出到本地文件和标准输出。
- 支持 JSON 和 TEXT 格式的日志输出，支持自定义日志格式。
- 支持选项模式。

## 自定义日志选项
日志选项定义如下：


```go
type options struct {
    output        io.Writer
    level         Level
    stdLevel      Level
    formatter     Formatter
    disableCaller bool
}
```
你可以通过选项模式，来对日志选项进行设置：


```go
type Option func(*options)

func initOptions(opts ...Option) (o *options) {
    o = &options{}
    for _, opt := range opts {
        opt(o)
    }

    if o.output == nil {
        o.output = os.Stderr
    }

    if o.formatter == nil {
        o.formatter = &TextFormatter{}
    }

    return
}

func WithLevel(level Level) Option {
    return func(o *options) {
        o.level = level
    }
}
...
func SetOptions(opts ...Option) {
    std.SetOptions(opts...)
}

func (l *logger) SetOptions(opts ...Option) {
    l.mu.Lock()
    defer l.mu.Unlock()

    for _, opt := range opts {
        opt(l.opt)
    }
}
```
可通过以下方式，来动态地修改日志的选项：
```
cuslog.SetOptions(cuslog.WithLevel(cuslog.DebugLevel))
```
你可以根据需要，对每一个日志选项创建设置函数 Withfunc 。这个日志包支持如下选项设置函数：
- WithOutput（output io.Writer）：设置输出位置。
- WithLevel（level Level）：设置输出级别。
- WithFormatter（formatter Formatter）：设置输出格式。
- WithDisableCaller（caller bool）：设置是否打印文件名和行号。
