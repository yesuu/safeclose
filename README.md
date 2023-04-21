# safeclose

SafeClose 是一个 Go 语言包，提供了一些函数用于安全地关闭和终止 Go 协程。该包通过使用 context 和信号量来实现优雅地关闭异步任务。

## 安装

`go get github.com/yesuu/safeclose`

## 用法

使用 SafeClose 包很简单，只需要调用它提供的函数即可。

```go
package main

import (
	"fmt"
	"github.com/your_username/safeclose"
	"time"
)

func main() {
	// 在一个独立的 Go 协程中执行函数 f
	safeclose.Do(func() {
		for i := 0; i < 10; i++ {
			fmt.Println(i)
			time.Sleep(time.Second)
		}
	})

	// 在一个独立的 Go 协程中执行函数 f，并可以接收 context 信号以终止函数执行
	safeclose.DoContext(func(ctx context.Context) {
		for {
			select {
			case <-ctx.Done():
				fmt.Println("Task is canceled.")
				return
			default:
				fmt.Println("Task is running.")
				time.Sleep(time.Second)
			}
		}
	})

	// 注册一个将在所有异步任务执行完毕后执行的函数 f
	safeclose.Defer(func() {
		fmt.Println("All tasks are done.")
	})

	// 等待所有异步任务执行完毕，然后执行 Defer 函数注册的函数
	safeclose.Wait()

	// 向所有由 DoContext 开启的异步任务发送终止信号，以优雅地终止它们
	safeclose.Cancel()
}
```

## 版权信息

SafeClose 包是基于 MIT 许可证开源的，详情请参考 LICENSE 文件。
