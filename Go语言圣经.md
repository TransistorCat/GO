# Go语言圣经

## 并发获取多个URL

```go
package main

import (
	"fmt"
	"io"
	"io/ioutil"
	"net/http"
	"os"
	"strings"
	"time"
)

func main() {
	start := time.Now()
	ch := make(chan string)
	for _, url := range os.Args[1:] {
		if !strings.HasPrefix(url, "http://") {
			url = "http://" + url
			go fetch(url, ch)
			// url = strings.Join([]string{"http://", url}, "")
		}//检测url的开头有没有"http://"没有就自动加
		
	}
	for range os.Args[1:] {
		fmt.Println(<-ch)
	}
	fmt.Printf("%.2fs elapsed\n", time.Since(start).Seconds())
}

func fetch(url string, ch chan<- string) {
	start := time.Now()
	resp, err := http.Get(url)
	if err != nil {
		ch <- fmt.Sprintf("%v", err)
		return
	}
	nbytes, err := io.Copy(ioutil.Discard, resp.Body)//因为只要获取字节数而不需要写入，ioutil.Discard看成垃圾桶
	resp.Body.Close()
	if err != nil {
		ch <- fmt.Sprintf("while reading %s: %v\n", url, err)
		return
	}
	sec := time.Since(start).Seconds()
	ch <- fmt.Sprintf("%.2fs %7d %s", sec, nbytes, url)
}

```

在这个例子中，每一个fetch函数在执行时都会往channel里发 送一个值(ch <- expression)，主函数负责接收这些值(<-ch)。这个程序中我们用main函数来接 收所有fetch函数传回的字符串，可以避免在goroutine异步执行还没有完成时main函数提前退出。

对于普通的上网过程，系统其实是这样做的：
浏览器本身是一个客户端，当在浏览器中输入 URL （网址）的时候，首先浏览器会去请求 DNS 服务器，通过 DNS 获取相应的域名对应的 IP，然后通过 IP 地址找到对应的服务器后，要求建立 TCP 连接；
与服务器建立连接后，浏览器会向服务器发送 HTTP Request （请求）包；
服务器接收到请求包之后开始处理请求包，并调用自身服务，返回 HTTP Response（响应）包；
客户端收到来自服务器的响应后开始渲染这个 Response 包里的主体（body），等收到全部的内容后断开与该服务器之间的 TCP 连接。

![img](C:\Users\你的姓名\Nutstore\1\我的坚果云\笔记\GO\4-19120512054A56.gif)通过上图可以将 Web 服务器的工作原理简单地归纳为：

- 客户机通过 TCP/IP 协议与服务器建立 TCP 连接；
- 客户端向服务器发送 HTTP 协议请求包，请求服务器里的资源文档；
- 服务器向客户机发送 HTTP 协议应答包，如果请求的资源包含有动态语言的内容，那么服务器会调用动态语言的解释引擎负责处理“动态内容”，并将处理得到的数据返回给客户端；
- 客户机与服务器断开，由客户端解释 HTML 文档，在客户端屏幕上渲染图形结果

```go
package main

import (
	"io/ioutil"
	"log"
	"net/http"
)

func main() {
	http.HandleFunc("/", handler)//为向 url发送请求时，调用的函数
	log.Fatal(http.ListenAndServe("localhost:8000", nil))
}

func handler(w http.ResponseWriter, r *http.Request) {
	content, _ := ioutil.ReadFile("./111.html")
	w.Write(content)
}

```

