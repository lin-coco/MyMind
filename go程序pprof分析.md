# Go语言pprof分析

## 安装pprof

```bash
go install github.com/google/pprof@latest
// ubuntu/debian
apt install graphviz
// centos
yum install graphviz
// macos
brew install graphviz
```

## 使用pprof

```go
package main
import (
    ...
   "fmt"
       _ "net/http/pprof"
)

func main(){
    go func() {
        fmt.Println(http.ListenAndServer(":6060", nil))
    }
}
```

## 示例程序

不断统计文件的单词数

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "net/http"
    "os"
    "strings"
    "time"

    _ "net/http/pprof"
)

func main() {
    go func() {
        fmt.Println("pprof server listening on http://0.0.0.0:6060")
        // 开启pprof http服务端，监听6060端口
        http.ListenAndServe(":6060", nil)
    }()
    resp, err := http.Get("https://www.gutenberg.org/cache/epub/1342/pg1342.txt")
    if err != nil {
        panic(err)
    }
    bs, err := io.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }
    os.WriteFile("sample.txt", bs, 0)
    for {
        n := countWords("sample.txt")
        fmt.Println("unique words:", n)
        time.Sleep(time.Millisecond * 100)
    }
}

func countWords(filename string) int {
    wordCount := make(map[string]int)
    file, err := os.Open(filename)
    if err != nil {
        panic(err)
    }
    defer file.Close()

    scanner := bufio.NewScanner(file)
    for scanner.Scan() {
        line := scanner.Text()
        words := strings.Fields(line)
        for _, word := range words {
            word = strings.ToLower(word)
            wordCount[word]++
        }
    }
    if err := scanner.Err(); err != nil {
        panic(err)
    }
    return len(wordCount)
}
```

## 抓取指标

先运行程序

```go
go run main.go
```

然后另一个终端再执行，抓取30秒

```go
// cpu占用时间分析
go tool pprof -http:7070 http://localhost:6060/debug/pprof/profile\?seconds\=30
// allocs内存占用分析
go tool pprof -http:7070 http://localhost:6060/debug/pprof/allocs\?seconds\=30
// goroutine数量分析
go tool pprof -http:7070 http://localhost:6060/debug/pprof/goroutine\?seconds\=30
...
```

## 指标分析

### top

- flat: 函数执行总耗时（不包含调用子函数）

- flat%: 函数运行时间百分比（不包含调用子函数）

- cum: 函数执行总耗时（包含调用子函数）

- cum%: 函数运行时间百分比（包含调用子函数）

- sum%: 其上所有行的flat%的累加。可以视为，这一行及其以上行，其所有的directly操作一共占了多少物理时间

### 火焰图

浏览器查看，更直观
























