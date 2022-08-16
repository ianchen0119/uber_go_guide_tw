<!--

Editing this document:

- Discuss all changes in GitHub issues first.
- Update the table of contents as new sections are added or removed.
- Use tables for side-by-side code samples. See below.

Code Samples:

Use 2 spaces to indent. Horizontal real estate is important in side-by-side
samples.

For side-by-side code samples, use the following snippet.

~~~
<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
BAD CODE GOES HERE
```

</td><td>

```go
GOOD CODE GOES HERE
```

</td></tr>
</tbody></table>
~~~

(You need the empty lines between the <td> and code samples for it to be
treated as Markdown.)

If you need to add labels or descriptions below the code samples, add another
row before the </tbody></table> line.

~~~
<tr>
<td>DESCRIBE BAD CODE</td>
<td>DESCRIBE GOOD CODE</td>
</tr>
~~~



-->

## [uber-go/guide](https://github.com/uber-go/guide) 的繁體中文翻譯

## [English](https://github.com/uber-go/guide/blob/master/style.md)

## Uber Go 語言撰寫規範

 [Uber](https://www.uber.com/) 是一家美國矽谷的科技公司，同時也是 Go 語言的早期 adoptor。它開源了許多 golang 相關的專案，像是 Gopher 圈熟知的 [zap](https://github.com/uber-go/zap)、[jaeger](https://github.com/jaegertracing/jaeger) 等。2018 年年末 Uber 將內部的 [Go 語言撰寫規範](https://github.com/uber-go/guide) 開源到 GitHub 上，經過一年多的累積與貢獻，該規範已經初具規模，並且廣受 Gopher 們的關注。本文是該規範的繁體中文版本，內容會以原版為參考盡快更新。

> 該專案參考並修改自 [uber-go/guide 的中文翻譯](https://github.com/xxjwxc/uber_go_guide_cn)，在這邊感謝開源社群的貢獻，讓眾多開發者可以免費使用這些學習資源。

 ## 版本

  - 目前版本：[[2022-05-25] commit:#150](https://github.com/uber-go/guide/commit/6e7643ee10e8808b9088e3f3f96f6e499c1be0ca)
  - 如果讀者發現原版本有任何更新、問題與內容的改進，歡迎大家幫忙貢獻到此專案中。

## 目錄

- [uber-go/guide 的繁體中文翻譯](#uber-goguide-的繁體中文翻譯)
- [English](#english)
- [Uber Go 語言撰寫規範](#uber-go-語言撰寫規範)
- [版本](#版本)
- [目錄](#目錄)
- [介绍](#介紹)
- [指導準則](#指導準則)
  - [指向 interface 的指標](#指向-interface-的指標)
  - [Interface 合理性驗證](#interface-合理性驗證)
  - [接收器 (receiver) 與介面](#接收器-receiver-與介面)
  - [零值 Mutex 是有效的](#零值-mutex-是有效的)
  - [在邊界處複製 Slices 和 Maps](#在邊界處複製-slices-和-maps)
    - [接收 Slices 和 Maps](#接收-slices-和-maps)
    - [返回 slices 或 maps](#返回-slices-或-maps)
  - [使用 defer 釋放資源](#使用-defer-釋放資源)
  - [Channel 的 size 要麽是 1，不然就是無暫存的](#channel-的-size-要麽是-1不然就是無暫存的)
  - [枚舉從 1 開始](#枚舉從-1-開始)
  - [使用 time 處理時間](#使用-time-處理時間)
    - [使用 `time.Time` 表達瞬時時間](#使用-timetime-表達瞬時時間)
    - [使用 `time.Duration` 表達時間區段](#使用-timeduration-表達時間區段)
    - [對外部系統使用 `time.Time` 和 `time.Duration`](#對外部系統使用-timetime-和-timeduration)
  - [Errors](#errors)
    - [錯誤的類型](#錯誤的類型)
    - [錯誤封裝](#錯誤封裝)
    - [錯誤命名](#錯誤命名)
  - [處理斷言失敗](#處理斷言失敗)
  - [不要使用 panic](#不要使用-panic)
  - [使用 go.uber.org/atomic](#使用-gouberorgatomic)
  - [避免可變的全域變數](#避免可變的全域變數)
  - [避免在公共結構中嵌入類型](#避免在公共結構中嵌入類型)
  - [避免使用内置名稱](#避免使用内置名稱)
  - [避免使用 `init()`](#避免使用-init)
  - [追加時優先指定切片容量](#追加時優先指定切片容量)
  - [主函數退出方式 (Exit)](#主函數退出方式-exit)
    - [一次性退出](#一次性退出)
  - [在序列化結構中使用成員標記](#在序列化結構中使用成員標記)
- [性能](#性能)
  - [優先使用 strconv 而不是 fmt](#優先使用-strconv-而不是-fmt)
  - [避免字串到字元的轉換](#避免字串到字元的轉換)
  - [指定容器容量](#指定容器容量)
    - [指定 Map 容量提示](#指定-map-容量提示)
    - [指定切片容量](#指定切片容量)
- [規範](#規範)
  - [避免過長的行](#避免過長的行)
  - [一致性](#一致性)
  - [相似的宣告放在一组](#相似的宣告放在一组)
  - [import 分组](#import-分组)
  - [套件名稱](#套件名稱)
  - [函數名](#函數名)
  - [導入別名](#導入別名)
  - [函數分组與顺序](#函數分组與顺序)
  - [減少嵌套](#減少嵌套)
  - [不必要的 else](#不必要的-else)
  - [上層變數宣告](#上層變數宣告)
  - [對於未導出的上層常數和變數，使用_作為前缀](#對於未導出的上層常數和變數使用_作為前缀)
  - [結構體中的嵌入](#結構體中的嵌入)
  - [本地變數宣告](#本地變數宣告)
  - [nil 是一個有效的 slice](#nil-是一個有效的-slice)
  - [缩小變數作用域](#缩小變數作用域)
  - [避免参數語義不明確 (Avoid Naked Parameters)](#避免参數語義不明確-avoid-naked-parameters)
  - [使用原始字串字面值，避免轉義](#使用原始字串字面值避免轉義)
  - [初始化結構體](#初始化結構體)
    - [使用成員名初始化結構](#使用成員名初始化結構)
    - [省略結構中的零值成員](#省略結構中的零值成員)
    - [對零值結構使用 `var`](#對零值結構使用-var)
    - [初始化 Struct 引用](#初始化-struct-引用)
  - [初始化 Maps](#初始化-maps)
  - [字串 string format](#字串-string-format)
  - [命名 Printf 樣式的函數](#命名-printf-樣式的函數)
- [编程模式](#编程模式)
  - [表驅動測試](#表驅動測試)
  - [功能選項](#功能選項)
- [Linting](#linting)
  - [Lint Runners](#lint-runners)
- [Stargazers over time](#stargazers-over-time)

## 介绍

樣式 (style) 是支配我們程式碼的慣例。術語`樣式`有點用詞不當，因為這些約定涵蓋的範圍不限於由 gofmt 替我們處理的原始程式碼格式。

本指南的目的是通過詳細描述在 Uber 撰寫 Go 程式碼的注意事項來管理這種複雜性。這些規則的存在是為了使函式庫易於管理，同時仍然允許工程師更有效地使用 Go 語言功能。

該指南最初由 [Prashant Varanasi] 和 [Simon Newton] 撰寫，目的是使一些同事能快速使用 Go。多年來，該指南已根據其他人的回饋進行了修改。

[Prashant Varanasi]: https://github.com/prashantv
[Simon Newton]: https://github.com/nomis52

本文件紀錄了我們在 Uber 遵循的 Go 程式碼中的慣用約定。其中許多是 Go 的通用準則，而其他擴展準則依赖於下面外部的指南：

1. [Effective Go](https://golang.org/doc/effective_go.html)
2. [Go Common Mistakes](https://github.com/golang/go/wiki/CommonMistakes)
3. [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)


所有程式碼都應該通過`golint`和`go vet`的檢查並無錯誤。我們建議您將編輯器設置為：

- 保存時使用 `goimports`
- 使用 `golint` 和 `go vet` 檢查錯誤

您可以在以下 Go 編輯器工具的相關頁面中找到更為詳細的訊息：
<https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins>

## 指導準則

### 指向 interface 的指標

您幾乎不需要指向介面類型的指標。您應該將介面作為值進行傳遞，在這樣的傳遞過程中，實質上傳遞的底層資料仍然可以是指標。

介面實質上在底層用兩個成員表示：

1. 一個指向某些特定類型訊息的指標。您可以將其视為"type"。
2. 資料指標。如果儲存的資料是指標，則直接儲存。如果儲存的資料是一個值，則儲存指向該值的指標。

如果希望介面方法修改基礎資料，則必須使用指標傳遞 (將物件指標赋值给介面變數)。

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

// f1.f() 無法修改底層資料
// f2.f() 可以修改底層資料，给介面變數 f2 赋值時使用的是物件指標
var f1 F = S1{}
var f2 F = &S2{}
```

### Interface 合理性驗證

在編譯時驗證介面的符合性。這包括：

- 將實作特定介面的導出類型作為介面 API 的一部分進行檢查
- 實作同一介面的 (導出和非導出) 類型属於實作類型的集合
- 任何違反介面合理性檢查的場景，都會终止編譯，並通知给使用者

補充：上面 3 點是編譯器對介面的檢查機制，
大致意思是錯誤使用介面會在編譯期間報錯。
所以可以利用這個機制讓部分問題在編譯期間暴露。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// 如果 Handler 沒有實作 http.Handler，會在運作時報錯
type Handler struct {
  // ...
}
func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  ...
}
```

</td><td>

```go
type Handler struct {
  // ...
}
// 用於觸發編譯時期的介面合理性檢查機制
// 如果 Handler 沒有實作 http.Handler，會在編譯期間報錯
var _ http.Handler = (*Handler)(nil)
func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

</td></tr>
</tbody></table>

如果 `*Handler` 與 `http.Handler` 的介面不匹配，
那麽語句 `var _ http.Handler = (*Handler)(nil)` 將無法編譯通過。

赋值的右邊應該是斷言類型的零值。
對於指標類型（如 `*Handler`）、切片和映射，這是 `nil`；
對於結構類型，這是空結構。

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}
var _ http.Handler = LogHandler{}
func (h LogHandler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

### 接收器 (receiver) 與介面

使用值接收器的方法既可以通過值調用，也可以通過指標調用。

帶指標接收器的方法只能通過指標或 [addressable values] 調用。

[addressable values]: https://golang.org/ref/spec#Method_values

例如，

```go
type S struct {
  data string
}

func (s S) Read() string {
  return s.data
}

func (s *S) Write(str string) {
  s.data = str
}

sVals := map[int]S{1: {"A"}}

// 你通過值只能調用 Read
sVals[1].Read()

// 這不能編譯通過：
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// 通過指標既可以調用 Read，也可以調用 Write 方法
sPtrs[1].Read()
sPtrs[1].Write("test")
```

類似的，即使方法有了值接收器，也同樣可以用指標接收器來滿足介面。

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

s1Val := S1{}
s1Ptr := &S1{}
s2Val := S2{}
s2Ptr := &S2{}

var i F
i = s1Val
i = s1Ptr
i = s2Ptr

//  下面程式碼無法通過編譯。因為 s2Val 是一個值，而 S2 的 f 方法中沒有使用值接收器
//   i = s2Val
```

[Effective Go](https://golang.org/doc/effective_go.html) 中有一段關於 [pointers vs. values](https://golang.org/doc/effective_go.html#pointers_vs_values) 的精彩講解。

補充：

- 一個類型可以有值接收器方法集和指標接收器方法集
  - 值接收器方法集是指標接收器方法集的子集，反之不是
- 規則
  - 值只可以使用值接收器方法集
  - 指標可以使用`值接收器方法集` + `指標接收器方法集`
- 介面的匹配 (或者叫實作)
  - 類型實作了介面的所有方法，叫匹配
  - 具體的講，要麽是類型的值方法集匹配介面，要麽是指標方法集匹配介面

具體的匹配分兩種：

- 值方法（Method）集和介面匹配
  - 给介面變數赋值的不管是值還是指標，都 ok，因為都包含值方法集
- 指標方法集和介面匹配
  - 只能將指標赋值给介面變數，因為只有指標方法集和介面匹配
  - 如果將值赋值给介面變數，會在編譯期間報錯 (會觸發介面合理性檢查機制)

為何 `i = s2Val` 會報錯，因為值方法集和介面不匹配。

### 零值 Mutex 是有效的

零值 `sync.Mutex` 和 `sync.RWMutex` 是有效的。所以指向 mutex 的指標基本是不必要的。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
mu := new(sync.Mutex)
mu.Lock()
```

</td><td>

```go
var mu sync.Mutex
mu.Lock()
```

</td></tr>
</tbody></table>

如果你使用結構體指標，mutex 應該作為結構體的非指標成員。即使該結構體不被導出，也不要直接把 mutex 嵌入到結構體中。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type SMap struct {
  sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

</td><td>

```go
type SMap struct {
  mu sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.mu.Lock()
  defer m.mu.Unlock()

  return m.data[k]
}
```

</td></tr>
<tr><td>

`Mutex` 成員，`Lock` 和 `Unlock` 方法是 `SMap` 導出的 API 中不刻意說明的一部分。

 </td><td>

mutex 及其方法是 `SMap` 的實作，對其使用者來說是不可見的。

 </td></tr>
 </tbody></table>

### 在邊界處複製 Slices 和 Maps

slices 和 maps 包含了指向底層資料的指標，因此在需要複製它們時要特別注意。

#### 接收 Slices 和 Maps

請記住，當 map 或 slice 作為函數参數傳入時，如果您儲存了對它們的引用，則使用者可以對其進行修改。

<table>
<thead><tr><th>Bad</th> <th>Good</th></tr></thead>
<tbody>
<tr>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = trips
}

trips := ...
d1.SetTrips(trips)

// 你是要修改 d1.trips 嗎？
trips[0] = ...
```

</td>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = make([]Trip, len(trips))
  copy(d.trips, trips)
}

trips := ...
d1.SetTrips(trips)

// 這裡我們修改 trips[0]，但不會影響到 d1.trips
trips[0] = ...
```

</td>
</tr>

</tbody>
</table>

#### 返回 slices 或 maps

同樣，請注意使用者對暴露内部狀態的 map 或 slice 的修改。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex

  counters map[string]int
}

// Snapshot 返回當前狀態。
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// snapshot 不再受互斥鎖保護
// 因此對 snapshot 的任何存取都將受到 race condition 影響
// 影響 stats.counters
snapshot := stats.Snapshot()
```

</td><td>

```go
type Stats struct {
  mu sync.Mutex

  counters map[string]int
}

func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  result := make(map[string]int, len(s.counters))
  for k, v := range s.counters {
    result[k] = v
  }
  return result
}

// snapshot 現在是一個副本
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>

### 使用 defer 釋放資源

使用 defer 釋放資源，諸如文件和鎖。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
p.Lock()
if p.count < 10 {
  p.Unlock()
  return p.count
}

p.count++
newCount := p.count
p.Unlock()

return newCount

// 當有多個 return 分支時，很容易遗忘 unlock
```

</td><td>

```go
p.Lock()
defer p.Unlock()

if p.count < 10 {
  return p.count
}

p.count++
return p.count

// 更可讀
```

</td></tr>
</tbody></table>

Defer 的開銷非常小，只有在您可以證明函數納秒執行時間處於納秒级的程度時，才應避免這樣做。使用 defer 提升可讀性是值得的，因為使用它們的成本微不足道。尤其適用於那些不僅僅是簡單記憶體存取的較大的方法，在這些方法中其他計算的資源消耗遠超過 `defer`。

### Channel 的 size 要麽是 1，不然就是無暫存的

channel 通常 size 應為 1 或是無暫存的。預設情況下，channel 是無暫存的，其 size 為零。任何其他大小都必須經過嚴格的檢查。我們需要考慮如何確定大小，考慮是什麽阻止了 channel 在高負載下和阻塞寫時的寫入，以及當這種情況發生時系统邏輯有哪些變化。(翻譯解釋：按照原文意思是需要界定通道邊界、競爭條件，以及邏輯上下文的梳理)

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// 應該足以滿足任何情況！
c := make(chan int, 64)
```

</td><td>

```go
// 大小：1
c := make(chan int, 1) // 或者
// 無暫存 channel，大小為 0
c := make(chan int)
```

</td></tr>
</tbody></table>

### 枚舉從 1 開始

在 Go 中引入枚舉的標準方法是宣告一個自定義類型和一個使用了 iota 的 const 组。由於變數的預設值為 0，因此通常應以非零值開頭枚舉。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota
  Subtract
  Multiply
)

// Add=0, Subtract=1, Multiply=2
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

// Add=1, Subtract=2, Multiply=3
```

</td></tr>
</tbody></table>

在某些情況下，使用零值是有意義的（枚舉從零開始），例如，當零值是理想的預設行為時。

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```

### 使用 time 處理時間

時間處理很複雜。關於時間的錯誤假設通常包括以下幾點。

1. 一天有 24 小時
2. 一小時有 60 分鐘
3. 一周有七天
4. 一年 365 天
5. [還有更多](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

例如，*1* 表示在一個時間點上加上 24 小時並不總是產生一個新的日曆日。

因此，在處理時間時始终使用 [`"time"`] 包，因為它有助於以更安全、更準確的方式處理這些不正確的假設。

[`"time"`]: https://golang.org/pkg/time/

#### 使用 `time.Time` 表達瞬時時間

在處理時間的瞬間時使用 [`time.Time`]，在比較、加入或減去時間時使用 `time.Time` 中的方法。

[`time.Time`]: https://golang.org/pkg/time/#Time

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func isActive(now, start, stop int) bool {
  return start <= now && now < stop
}
```

</td><td>

```go
func isActive(now, start, stop time.Time) bool {
  return (start.Before(now) || start.Equal(now)) && now.Before(stop)
}
```

</td></tr>
</tbody></table>

#### 使用 `time.Duration` 表達時間區段

在處理時間區段時使用 [`time.Duration`] .

[`time.Duration`]: https://golang.org/pkg/time/#Duration

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func poll(delay int) {
  for {
    // ...
    time.Sleep(time.Duration(delay) * time.Millisecond)
  }
}
poll(10) // 是幾秒鐘還是幾毫秒？
```

</td><td>

```go
func poll(delay time.Duration) {
  for {
    // ...
    time.Sleep(delay)
  }
}
poll(10*time.Second)
```

</td></tr>
</tbody></table>

回到第一個例子，在一個時間瞬間加上 24 小時，我們用於加入時間的方法取決於意圖。如果我們想要下一個日曆日 (當前天的下一天) 的同一個時間點，我們應該使用 [`Time.AddDate`]。但是，如果我們想保證某一時刻比前一時刻晚 24 小時，我們應該使用 [`Time.Add`]。

[`Time.AddDate`]: https://golang.org/pkg/time/#Time.AddDate
[`Time.Add`]: https://golang.org/pkg/time/#Time.Add

```go
newDay := t.AddDate(0 /* years */, 0 /* months */, 1 /* days */)
maybeNewDay := t.Add(24 * time.Hour)
```

#### 對外部系統使用 `time.Time` 和 `time.Duration`

盡可能在與外部系统的互動中使用 `time.Duration` 和 `time.Time` 例如 :

- Command-line 標志: [`flag`] 通過 [`time.ParseDuration`] 支持 `time.Duration`
- JSON: [`encoding/json`] 通過其 [`UnmarshalJSON` method] 方法支持將 `time.Time` 編碼為 [RFC 3339] 字串
- SQL: [`database/sql`] 支持將 `DATETIME` 或 `TIMESTAMP` 列轉換為 `time.Time`，如果底層驅動程式支持則返回
- YAML: [`gopkg.in/yaml.v2`] 支持將 `time.Time` 作為 [RFC 3339] 字串，並通過 [`time.ParseDuration`] 支持 `time.Duration`。

  [`flag`]: https://golang.org/pkg/flag/
  [`time.ParseDuration`]: https://golang.org/pkg/time/#ParseDuration
  [`encoding/json`]: https://golang.org/pkg/encoding/json/
  [RFC 3339]: https://tools.ietf.org/html/rfc3339
  [`UnmarshalJSON` method]: https://golang.org/pkg/time/#Time.UnmarshalJSON
  [`database/sql`]: https://golang.org/pkg/database/sql/
  [`gopkg.in/yaml.v2`]: https://godoc.org/gopkg.in/yaml.v2

當不能在這些互動中使用 `time.Duration` 時，請使用 `int` 或 `float64`，並在成員名稱中包含單位。

例如，由於 `encoding/json` 不支持 `time.Duration`，因此該單位包含在成員的名稱中。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// {"interval": 2}
type Config struct {
  Interval int `json:"interval"`
}
```

</td><td>

```go
// {"intervalMillis": 2000}
type Config struct {
  IntervalMillis int `json:"intervalMillis"`
}
```

</td></tr>
</tbody></table>

當在這些互動中不能使用 `time.Time` 時，除非達成一致，否則使用 `string` 和 [RFC 3339] 中定義的格式時間戳。預設情況下，[`Time.UnmarshalText`] 使用此格式，並可通過 [`time.RFC3339`] 在 `Time.Format` 和 `time.Parse` 中使用。

[`Time.UnmarshalText`]: https://golang.org/pkg/time/#Time.UnmarshalText
[`time.RFC3339`]: https://golang.org/pkg/time/#RFC3339

儘管這在實践中並不成問題，但請記住，`"time"` 套件不支持解析閏秒時間戳（[8728]），也不在計算中考慮閏秒（[15190]）。如果您比較兩個時間瞬間，則差异將不包括這兩個瞬間之間可能發生的閏秒。

[8728]: https://github.com/golang/go/issues/8728
[15190]: https://github.com/golang/go/issues/15190

<!-- TODO: section on String methods for enums -->


### Errors

#### 錯誤的類型

宣告錯誤的選項很少。
在選擇最適合您的用例的選項之前，請考慮以下事項。

- 調用者是否需要匹配錯誤以便他們可以處理它？
  如果是，我們必須通過宣告頂層錯誤變數或自定義類型來支援 [`errors.Is`] 或 [`errors.As`] 函數。
- 錯誤訊息是否為靜態字串，還是需要 context 訊息的動態字串？
  如果是靜態字串，我們可以使用 [`errors.New`]，但對於後者，我們必須使用 [`fmt.Errorf`] 或自定義錯誤的類型。
- 我們是否正在傳遞由下游函數返回的新錯誤？
   如果是這樣，請參考[錯誤封裝部分](#錯誤封裝)。

[`errors.Is`]: https://golang.org/pkg/errors/#Is
[`errors.As`]: https://golang.org/pkg/errors/#As

| 錯誤匹配？| 錯誤訊息 | 指導                           |
|-----------------|---------------|-------------------------------------|
| No              | static        | [`errors.New`]                      |
| No              | dynamic       | [`fmt.Errorf`]                      |
| Yes             | static        | top-level `var` with [`errors.New`] |
| Yes             | dynamic       | custom `error` type                 |

[`errors.New`]: https://golang.org/pkg/errors/#New
[`fmt.Errorf`]: https://golang.org/pkg/fmt/#Errorf

例如，
使用 [`errors.New`] 表示帶有靜態字串的錯誤。
如果調用者需要匹配並處理此錯誤，則將此錯誤導出為變數以支持將其與 `errors.Is` 匹配。

<table>
<thead><tr><th>無錯誤匹配</th><th>錯誤匹配</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open() error {
  return errors.New("could not open")
}

// package bar

if err := foo.Open(); err != nil {
  // Can't handle the error.
  panic("unknown error")
}
```

</td><td>

```go
// package foo

var ErrCouldNotOpen = errors.New("could not open")

func Open() error {
  return ErrCouldNotOpen
}

// package bar

if err := foo.Open(); err != nil {
  if errors.Is(err, foo.ErrCouldNotOpen) {
    // handle the error
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

對於動態字串的錯誤，
如果調用者不需要匹配它，則使用 [`fmt.Errorf`]，
如果調用者確實需要匹配它，則自定義 `error`。

<table>
<thead><tr><th>無錯誤匹配</th><th>錯誤匹配</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open(file string) error {
  return fmt.Errorf("file %q not found", file)
}

// package bar

if err := foo.Open("testfile.txt"); err != nil {
  // Can't handle the error.
  panic("unknown error")
}
```

</td><td>

```go
// package foo

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

func Open(file string) error {
  return &NotFoundError{File: file}
}


// package bar

if err := foo.Open("testfile.txt"); err != nil {
  var notFound *NotFoundError
  if errors.As(err, &notFound) {
    // handle the error
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

請注意，如果您從包中導出錯誤變數或類型，
它們將成為包的公共 API 的一部分。

#### 錯誤封裝

如果調用其他方法時出現錯誤, 通常有三種處理方式可以選擇：

- 將原始錯誤原樣返回
- 使用 `fmt.Errorf` 搭配 `%w` 將錯誤加入進 context 後返回
- 使用 `fmt.Errorf` 搭配 `%v` 將錯誤加入進 context 後返回

如果沒有要加入其他 context，則按原樣返回原始錯誤。
這將保留原始錯誤的類型和訊息。
這非常適合底層錯誤訊息有足夠的訊息來追蹤它來自哪裡的錯誤。

否則，盡可能在錯誤訊息中加入 context 
這樣就不會出現諸如“連接被拒絕”之類的模糊錯誤，
您會收到更多有用的錯誤，例如“調用服务 foo：連接被拒絕”。

使用 `fmt.Errorf` 為你的錯誤加入 context ，
根據調用者是否應該能夠匹配和提取根本原因，在 `%w` 或 `%v` 動詞之間進行選擇。

- 如果調用者應該可以存取底層錯誤，請使用 `%w`。
   對於大多數包装錯誤，這是一個很好的預設值，
   但請注意，調用者可能會開始依赖此行為。因此，對於包装錯誤是已知`var`或類型的情況，請將其作為函數契約的一部分進行紀錄和測試。
- 使用 `%v` 來混淆底層錯誤。
  調用者將無法匹配它，但如果需要，您可以在將來切换到 `%w`。

在為返回的錯誤加入 context 時，通過避免使用"failed to"之類的短語來保持 context 簡潔，當錯誤通過 stack 向上增加時，它會被一層一層的堆疊起來：

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "failed to create new store: %w", err)
}
```

</td><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "new store: %w", err)
}
```

</td></tr><tr><td>

```
failed to x: failed to y: failed to create new store: the error
```

</td><td>

```
x: y: new store: the error
```

</td></tr>
</tbody></table>

然而，一旦錯誤被發送到另一個系统，應該清楚訊息是一個錯誤（例如`err` 標籤或日誌中的"Failed"前缀）。


另見 [不要只檢查錯誤，優雅地處理它們]。

  [`"pkg/errors".Cause`]: https://godoc.org/github.com/pkg/errors#Cause
  [不要只檢查錯誤，優雅地處理它們]: https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully

#### 錯誤命名

對於儲存為全域變數的錯誤值，
根據是否導出，使用前缀 `Err` 或 `err`。
請看指南 [對於未導出的上層常數和變數，使用_作為前缀](#對於未導出的上層常數和變數使用_作為前缀)。

```go
var (
  // 導出以下兩個錯誤，以便此包的使用者可以將它們與 errors.Is 進行匹配。

  ErrBrokenLink = errors.New("link is broken")
  ErrCouldNotOpen = errors.New("could not open")

  // 這個錯誤沒有被導出，因為我們不想讓它成為我們公共 API 的一部分。 我們可能仍然在帶有錯誤的套件中使用它。

  errNotFound = errors.New("not found")
)
```

對於自定義錯誤的類型，請改用後缀 `Error`。

```go
// 同樣，這個錯誤被導出，以便這個包的使用者可以將它與 errors.As 匹配。

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

// 並且這個錯誤沒有被導出，因為我們不想讓它成為公共 API 的一部分。 我們仍然可以在帶有 errors.As 的包中使用它。
type resolveError struct {
  Path string
}

func (e *resolveError) Error() string {
  return fmt.Sprintf("resolve %q", e.Path)
}
```

### 處理斷言失敗

[類型斷言] 將會在检测到不正確的類型時，以單一返回值形式返回 panic。 因此，請始终使用“逗號 ok”习語。

  [類型斷言]: https://golang.org/ref/spec#Type_assertions

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
t := i.(string)
```

</td><td>

```go
t, ok := i.(string)
if !ok {
  // 優雅地處理錯誤
}
```

</td></tr>
</tbody></table>

<!-- TODO: There are a few situations where the single assignment form is
fine. -->

### 不要使用 panic

在生產環境中運作的程式碼必須避免出現 panic。panic 是 [級聯失敗] 的主要原因 。如果發生錯誤，該函數必須返回錯誤，並允許調用方決定如何處理它。

[級聯失敗]: https://en.wikipedia.org/wiki/Cascading_failure

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func run(args []string) {
  if len(args) == 0 {
    panic("an argument is required")
  }
  // ...
}

func main() {
  run(os.Args[1:])
}
```

</td><td>

```go
func run(args []string) error {
  if len(args) == 0 {
    return errors.New("an argument is required")
  }
  // ...
  return nil
}

func main() {
  if err := run(os.Args[1:]); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

</td></tr>
</tbody></table>

panic/recover 不是錯誤處理策略。僅當發生不可恢復的事情（例如：nil 引用）時，程式才必須 panic。程式初始化是一個例外：程式啟動時應使程式中止的不良情況可能會引起 panic。

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

即使在測試程式碼中，也優先使用`t.Fatal`或者`t.FailNow`而不是 panic 來確保失敗被標記。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  panic("failed to set up test")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  t.Fatal("failed to set up test")
}
```

</td></tr>
</tbody></table>

<!-- TODO: Explain how to use _test packages. -->

### 使用 go.uber.org/atomic

使用 [sync/atomic] 包的原子操作對原始類型 (`int32`, `int64`等）進行操作，因為很容易忘記使用原子操作來讀取或修改變數。

[go.uber.org/atomic] 通過隐藏基礎類型為這些操作增加了類型安全性。此外，它包括一個方便的`atomic.Bool`類型。

[go.uber.org/atomic]: https://godoc.org/go.uber.org/atomic
[sync/atomic]: https://golang.org/pkg/sync/atomic/

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type foo struct {
  running int32  // atomic
}

func (f* foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // race!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>

### 避免可變的全域變數

使用選擇依赖注入方式避免改變全域變數。
既適用於函數指標又適用於其他值類型

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// sign.go
var _timeNow = time.Now
func sign(msg string) string {
  now := _timeNow()
  return signWithTime(msg, now)
}
```

</td><td>

```go
// sign.go
type signer struct {
  now func() time.Time
}
func newSigner() *signer {
  return &signer{
    now: time.Now,
  }
}
func (s *signer) Sign(msg string) string {
  now := s.now()
  return signWithTime(msg, now)
}
```
</td></tr>
<tr><td>

```go
// sign_test.go
func TestSign(t *testing.T) {
  oldTimeNow := _timeNow
  _timeNow = func() time.Time {
    return someFixedTime
  }
  defer func() { _timeNow = oldTimeNow }()
  assert.Equal(t, want, sign(give))
}
```

</td><td>

```go
// sign_test.go
func TestSigner(t *testing.T) {
  s := newSigner()
  s.now = func() time.Time {
    return someFixedTime
  }
  assert.Equal(t, want, s.Sign(give))
}
```

</td></tr>
</tbody></table>

### 避免在公共結構中嵌入類型

這些嵌入的類型泄漏實作、禁止類型演化和模糊的文件。

假設您使用共享的 `AbstractList` 實作了多種 list 類型，請避免在具體的 list 實作中嵌入 `AbstractList`。
相反，只需手動將方法寫入具體的 list ，該 list 將委托给 abstract list 。

```go
type AbstractList struct {}
// 加入將實體加入到 list 中。
func (l *AbstractList) Add(e Entity) {
  // ...
}
// 移除從 list 中移除實體。
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList 是一個實體 list 。
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList 是一個實體 list 。
type ConcreteList struct {
  list *AbstractList
}
// 加入將實體加入到 list 中。
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}
// 移除從 list 中移除實體。
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go 允許 [類型嵌入](https://golang.org/doc/effective_go.html#embedding) 作為继承和组合之間的折衷。外部類型獲取嵌入類型的方法的隐式副本。預設情況下，這些方法委托给嵌入實例的同一方法。

結構還獲得與類型同名的成員。
所以，如果嵌入的類型是 public，那麽成員是 public。為了保持向後兼容性，外部類型的每個未來版本都必須保留嵌入類型。

很少需要嵌入類型。
這是一種方便，可以帮助您避免撰寫冗長的委托方法。

即使嵌入兼容的抽象 list  *interface*，而不是結構體，這將為開發人员提供更大的靈活性來改變未來，但仍然洩漏了具體 list 使用抽象實作的細節。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList 是各種實體 list 的通用實作。
type AbstractList interface {
  Add(Entity)
  Remove(Entity)
}
// ConcreteList 是一個實體 list 。
type ConcreteList struct {
  AbstractList
}
```

</td><td>

```go
// ConcreteList 是一個實體 list 。
type ConcreteList struct {
  list AbstractList
}
// 將 Entity 加進 list。
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}
// 移除從 list 中移除 Entity。
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

無論是使用嵌入結構還是嵌入介面，都會限制類型的演化。

- 向嵌入介面加入方法是一個破壞性的改變。
- 從嵌入結構體删除方法是一個破壞性改變。
- 删除嵌入類型是一個破壞性的改變。
- 即使使用滿足相同介面的類型替换嵌入類型，也是一個破壞性的改變。

儘管撰寫這些委托方法是乏味的，但是額外的工作隐藏了實作，留下了更多的更改機會，還消除了在文件中發現完整 list 介面的間接性操作。

### 避免使用内置名稱

Go [語言規範] 概述了幾個内置的，
不應在 Go 项目中使用的 [預先宣告的標識符]。

根據 context 的不同，將這些標識符作為名稱重複使用，
將在當前作用域（或任何嵌套作用域）中隐藏原始標識符，或者混淆程式碼。
在最好的情況下，編譯器會報錯；在最壞的情況下，這樣的程式碼可能會引入潜在的、難以恢復的錯誤。

[語言規範]: https://golang.org/ref/spec
[預先宣告的標識符]: https://golang.org/ref/spec#Predeclared_identifiers

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var error string
// `error` 作用域隐式覆蓋

// or

func handleErrorMessage(error string) {
    // `error` 作用域隐式覆蓋
}
```

</td><td>

```go
var errorMessage string
// `error` 指向内置的非覆蓋

// or

func handleErrorMessage(msg string) {
    // `error` 指向内置的非覆蓋
}
```

</td></tr>
<tr><td>

```go
type Foo struct {
    // 雖然這些成員在技術上不構成阴影，但`error`或`string`字串的重映射現在是不明確的。
    error  error
    string string
}

func (f Foo) Error() error {
    // `error` 和 `f.error` 在視覺上是相似的
    return f.error
}

func (f Foo) String() string {
    // `string` and `f.string` 在視覺上是相似的
    return f.string
}
```

</td><td>

```go
type Foo struct {
    // `error` and `string` 現在是明確的。
    err error
    str string
}

func (f Foo) Error() error {
    return f.err
}

func (f Foo) String() string {
    return f.str
}
```
</td></tr>
</tbody></table>

注意，編譯器在使用預先分隔的標識符時不會生成錯誤，
但是諸如`go vet`之類的工具會正確地指出這些和其他情況下的隐式問題。

### 避免使用 `init()`

盡可能避免使用`init()`。當`init()`是不可避免或可取的，程式碼應先嘗試：

1. 無論程式環境或調用如何，都要完全確定。
2. 避免依赖於其他`init()`函數的顺序或副作用。雖然`init()`顺序是明確的，但程式碼可以更改，
因此`init()`函數之間的關系可能會使程式碼變得脆弱和容易出错。
3. 避免存取或操作全域或環境狀態，如機器訊息、環境變數、工作目錄、程式参數/輸入等。
4. 避免`I/O`，包括檔案系統、網路和系統呼叫。

不能滿足這些要求的程式碼可能属於要作為`main()`調用的一部分（或程式生命周期中的其他地方），
或者作為`main()`本身的一部分寫入。特別是，打算由其他程式使用的函式庫應該特別注意完全確定性，
而不是納秒執行“init magic”

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Foo struct {
    // ...
}
var _defaultFoo Foo
func init() {
    _defaultFoo = Foo{
        // ...
    }
}
```

</td><td>

```go
var _defaultFoo = Foo{
    // ...
}
// or，為了更好的可測試性：
var _defaultFoo = defaultFoo()
func defaultFoo() Foo {
    return Foo{
        // ...
    }
}
```

</td></tr>
<tr><td>

```go
type Config struct {
    // ...
}
var _config Config
func init() {
    // Bad: 基於當前目錄
    cwd, _ := os.Getwd()
    // Bad: I/O
    raw, _ := ioutil.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    yaml.Unmarshal(raw, &_config)
}
```

</td><td>

```go
type Config struct {
    // ...
}
func loadConfig() Config {
    cwd, err := os.Getwd()
    // handle err
    raw, err := ioutil.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    // handle err
    var config Config
    yaml.Unmarshal(raw, &config)
    return config
}
```

</td></tr>
</tbody></table>

考慮到上述情況，在某些情況下，`init()`可能更可取或是必要的，可能包括：

- 不能表示為單個赋值的複雜表達式。
- 可插入的鉤子，如`database/sql`、編碼類型註冊表等。
- 對 [Google Cloud Functions] 和其他形式的確定性预計算的優化。

  [Google Cloud Functions]: https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations

### 追加時優先指定切片容量

追加時優先指定切片容量

在盡可能的情況下，在初始化要追加的切片時為`make()`提供一個容量值。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0, size)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td></tr>
<tr><td>

```
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>

### 主函數退出方式 (Exit)

Go 程式使用 [`os.Exit`] 或者 [`log.Fatal*`] 立即退出 (使用`panic`不是退出程式的好方法，請 [不要使用 panic](#不要使用-panic)。)

  [`os.Exit`]: https://golang.org/pkg/os/#Exit
  [`log.Fatal*`]: https://golang.org/pkg/log/#Fatal

**僅在`main()`** 中調用其中一個 `os.Exit` 或者 `log.Fatal*`。所有其他函數應將錯誤返回到信號失敗中。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func main() {
  body := readFile(path)
  fmt.Println(body)
}
func readFile(path string) string {
  f, err := os.Open(path)
  if err != nil {
    log.Fatal(err)
  }
  b, err := ioutil.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }
  return string(b)
}
```

</td><td>

```go
func main() {
  body, err := readFile(path)
  if err != nil {
    log.Fatal(err)
  }
  fmt.Println(body)
}
func readFile(path string) (string, error) {
  f, err := os.Open(path)
  if err != nil {
    return "", err
  }
  b, err := ioutil.ReadAll(f)
  if err != nil {
    return "", err
  }
  return string(b), nil
}
```

</td></tr>
</tbody></table>

原則上：退出的具有多種功能的程式存在一些問題：

- 不明顯的控制流：任何函數都可以退出程式，因此很難對控制流進行推理。
- 難以測試：退出程式的函數也將退出調用它的測試。這使得函數很難測試，並引入了跳過 `go test` 尚未運作的其他測試的風险。
- 跳過清理：當函數退出程式時，會跳過已经進入`defer` queue 裡的函數調用。這增加了跳過重要清理任務的風险。
#### 一次性退出

如果可能的话，你的`main（）`函數中 **最多一次** 調用 `os.Exit`或者`log.Fatal`。如果有多個錯誤場景停止程式納秒執行，請將該邏輯放在單獨的函數下並從中返回錯誤。
這會缩短 `main()` 函數，並將所有關键業務邏輯放入一個單獨的、可測試的函數中。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
package main
func main() {
  args := os.Args[1:]
  if len(args) != 1 {
    log.Fatal("missing file")
  }
  name := args[0]
  f, err := os.Open(name)
  if err != nil {
    log.Fatal(err)
  }
  defer f.Close()
  // 如果我們調用 log.Fatal 在這條線之後
  // f.Close 將會被納秒執行。
  b, err := ioutil.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }
  // ...
}
```

</td><td>

```go
package main
func main() {
  if err := run(); err != nil {
    log.Fatal(err)
  }
}
func run() error {
  args := os.Args[1:]
  if len(args) != 1 {
    return errors.New("missing file")
  }
  name := args[0]
  f, err := os.Open(name)
  if err != nil {
    return err
  }
  defer f.Close()
  b, err := ioutil.ReadAll(f)
  if err != nil {
    return err
  }
  // ...
}
```

</td></tr>
</tbody></table>

### 在序列化結構中使用成員標記

任何序列化到JSON、YAML、，
或其他支持基於標記的成員命名的格式應使用相關標記進行註釋。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Stock struct {
  Price int
  Name  string
}
bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td><td>

```go
type Stock struct {
  Price int    `json:"price"`
  Name  string `json:"name"`
  // Safe to rename Name to Symbol.
}
bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td></tr>
</tbody></table>

理論上：
結構的序列化形式是不同系统之間的契約。
對序列化表單結構（包括成員名）的更改會破壞此約定。在標記中指定成員名使約定明確，
它還可以通過重構或重命名成員來防止意外違反約定。

## 性能

性能方面的特定準則只適用於高频場景。

### 優先使用 strconv 而不是 fmt

將原語轉換為字串或從字串轉換時，`strconv` 速度比 `fmt` 快。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  s := fmt.Sprint(rand.Int())
}
```

</td><td>

```go
for i := 0; i < b.N; i++ {
  s := strconv.Itoa(rand.Int())
}
```

</td></tr>
<tr><td>

```
BenchmarkFmtSprint-4    143 ns/op    2 allocs/op
```

</td><td>

```
BenchmarkStrconv-4    64.2 ns/op    1 allocs/op
```

</td></tr>
</tbody></table>



### 避免字串到字元的轉換

不要反覆從固定字串建立字元 slice。相反，請納秒執行一次轉換並捕獲结果。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  w.Write([]byte("Hello world"))
}
```

</td><td>

```go
data := []byte("Hello world")
for i := 0; i < b.N; i++ {
  w.Write(data)
}
```

</tr>
<tr><td>

```
BenchmarkBad-4   50000000   22.2 ns/op
```

</td><td>

```
BenchmarkGood-4  500000000   3.25 ns/op
```

</td></tr>
</tbody></table>

### 指定容器容量

盡可能指定容器容量，以便為容器預先分配記憶體。這將在加入元素時最小化後續分配（通過複製和調整容器大小）。

#### 指定 Map 容量提示

在盡可能的情況下，在使用 `make()` 初始化的時候提供容量訊息

```go
make(map[T1]T2, hint)
```

向`make()`提供容量提示會在初始化時嘗試調整 map 的大小，這將減少在將元素加入到 map 時為 map 重新分配記憶體。


注意，與 slices 不同。map capacity 提示並不保證完全的搶斷式分配，而是用於估計所需的 hashmap bucket 的數量。
因此，在將元素加入到 map 時，甚至在指定 map 容量時，仍可能發生分配。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[string]os.FileInfo)

files, _ := ioutil.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := ioutil.ReadDir("./files")

m := make(map[string]os.FileInfo, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

`m` 是在沒有大小提示的情況下建立的； 在運作時可能會有更多分配。

</td><td>

`m` 是有大小提示建立的；在運作時可能會有更少的分配。

</td></tr>
</tbody></table>

#### 指定切片容量

在盡可能的情況下，在使用`make()`初始化切片時提供容量訊息，特別是在追加切片時。

```go
make([]T, length, capacity)
```

與 maps 不同，slice capacity 不是一個提示：編譯器將為提供给`make()`的 slice 的容量分配足夠的記憶體，
這意味著後續的 append()`操作將導致零分配（直到 slice 的長度與容量匹配，在此之後，任何 append 都可能調整大小以容纳其他元素）。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0, size)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td></tr>
<tr><td>

```
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>

## 規範
### 避免過長的行

避免使用需要讀者水平滚動或過度轉動頭部的程式碼行。

我們建議將行長度限制為 **99 characters** (99 個字符).
作者應該在達到這個限制之前换行，
但這不是硬性限制。
允許程式碼超過此限制。
### 一致性

本文中概述的一些標準都是客觀性的评估，是根據場景、 context 、或者主觀性的判断；

但是最重要的是，**保持一致**.

一致性的程式碼更容易维護、是更合理的、需要更少的学习成本、並且随著新的約定出現或者出現錯誤後更容易移植、更新、修复 bug

相反，在一個函式庫中包含多個完全不同或衝突的程式碼風格會導致维護成本開銷、不確定性和認知偏差。所有這些都會直接導致速度降低、程式碼檢查痛苦、而且增加 bug 數量。

將這些標準應用於函式庫時，建議在 package（或更大）等級進行更改，子套件等級的應用程式通過將多個樣式引入到同一程式碼中，違反了上述關注點。

### 相似的宣告放在一组

Go 語言支持將相似的宣告放在一個组内。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
import "a"
import "b"
```

</td><td>

```go
import (
  "a"
  "b"
)
```

</td></tr>
</tbody></table>

這同樣適用於常數、變數和類型宣告：

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go

const a = 1
const b = 2

var a = 1
var b = 2

type Area float64
type Volume float64
```

</td><td>

```go
const (
  a = 1
  b = 2
)

var (
  a = 1
  b = 2
)

type (
  Area float64
  Volume float64
)
```

</td></tr>
</tbody></table>

僅將相關的宣告放在一组。不要將不相關的宣告放在一组。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
  EnvVar = "MY_ENV"
)
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

const EnvVar = "MY_ENV"
```

</td></tr>
</tbody></table>

分组使用的位置沒有限制，例如：你可以在函數内部使用它們：

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func f() string {
  red := color.New(0xff0000)
  green := color.New(0x00ff00)
  blue := color.New(0x0000ff)

  ...
}
```

</td><td>

```go
func f() string {
  var (
    red   = color.New(0xff0000)
    green = color.New(0x00ff00)
    blue  = color.New(0x0000ff)
  )

  ...
}
```

</td></tr>
</tbody></table>

例外：如果變數宣告與其他變數相鄰，則應將變數宣告（尤其是函數内部的宣告）分组在一起。對一起宣告的變數納秒執行此操作，即使它們不相關。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func (c *client) request() {
  caller := c.name
  format := "json"
  timeout := 5*time.Second
  var err error
  // ...
}
```

</td><td>

```go
func (c *client) request() {
  var (
    caller  = c.name
    format  = "json"
    timeout = 5*time.Second
    err error
  )
  // ...
}
```

</td></tr>
</tbody></table>

### import 分组

導入應該分為兩组：

- 標準函式庫
- 其他函式庫

預設情況下，這是 goimports 應用的分组。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td><td>

```go
import (
  "fmt"
  "os"

  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td></tr>
</tbody></table>

### 套件名稱

當命名包時，請按下面規則選擇一個名稱：

- 全部小寫。沒有大寫或底線。
- 大多數使用命名導入的情況下，不需要重命名。
- 簡短而簡潔。請記住，在每個使用的地方都完整標記了該名稱。
- 不用複數。例如`net/url`，而不是`net/urls`。
- 不要用“common”，“util”，“shared”或“lib”。這些是不好的，訊息量不足的名稱。

另請參考 [Go 套件命名規則] 和 [Go 套件樣式指南].

[Go 套件命名規則]: https://blog.golang.org/package-names
[Go 套件樣式指南]: https://rakyll.org/style-packages/

### 函數名

我們遵循 Go 社群關於使用 [MixedCaps 作為函數名] 的約定。有一個例外，為了對相關的測試用例進行分组，函數名可能包含底線，如：`TestMyFunction_WhatIsBeingTested`.

[MixedCaps 作為函數名]: https://golang.org/doc/effective_go.html#mixed-caps

### 導入別名

如果程式套件名稱稱與導入路径的最後一個元素不匹配，則必須使用導入別名。

```go
import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)
```

在所有其他情況下，除非導入之間有直接衝突，否則應避免導入別名。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"

  nettrace "golang.net/x/trace"
)
```

</td><td>

```go
import (
  "fmt"
  "os"
  "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td></tr>
</tbody></table>

### 函數分组與顺序

- 函數應按粗略的調用顺序排序。
- 同一文件中的函數應按接收者分组。

因此，導出的函數應先出現在文件中，放在`struct`, `const`, `var`定義的後面。

在定義類型之後，但在接收者的其餘方法之前，可能會出現一個 `newXYZ()`/`NewXYZ()`

由於函數是按接收者分组的，因此普通工具函數應在文件末尾出現。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func (s *something) Cost() {
  return calcCost(s.weights)
}

type something struct{ ... }

func calcCost(n []int) int {...}

func (s *something) Stop() {...}

func newSomething() *something {
    return &something{}
}
```

</td><td>

```go
type something struct{ ... }

func newSomething() *something {
    return &something{}
}

func (s *something) Cost() {
  return calcCost(s.weights)
}

func (s *something) Stop() {...}

func calcCost(n []int) int {...}
```

</td></tr>
</tbody></table>

### 減少嵌套

程式碼應通過盡可能先處理錯誤情況/特殊情況並盡早返回或繼續循環來減少嵌套。減少嵌套多個等級的程式碼的程式碼量。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for _, v := range data {
  if v.F1 == 1 {
    v = process(v)
    if err := v.Call(); err == nil {
      v.Send()
    } else {
      return err
    }
  } else {
    log.Printf("Invalid v: %v", v)
  }
}
```

</td><td>

```go
for _, v := range data {
  if v.F1 != 1 {
    log.Printf("Invalid v: %v", v)
    continue
  }

  v = process(v)
  if err := v.Call(); err != nil {
    return err
  }
  v.Send()
}
```

</td></tr>
</tbody></table>

### 不必要的 else

如果在 if 的兩個分支中都設置了變數，則可以將其替换為單個 if。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var a int
if b {
  a = 100
} else {
  a = 10
}
```

</td><td>

```go
a := 10
if b {
  a = 100
}
```

</td></tr>
</tbody></table>

### 上層變數宣告

在上層，使用標準`var`關键字。請勿指定類型，除非它與表達式的類型不同。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// 由於 F 已经明確了返回一個字串類型，因此我們沒有必要顯式指定_s 的類型
// 還是那種類型

func F() string { return "A" }
```

</td></tr>
</tbody></table>

如果表達式的類型與所需的類型不完全匹配，請指定類型。

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F 返回一個 myError 類型的實例，但是我們要 error 類型
```

### 對於未導出的上層常數和變數，使用_作為前缀

在未導出的頂層 `vars` 和 `consts`， 前面加上前缀 `_`，以使它們在使用時明確表示它們是全域符號。

基本依據：頂層變數和常數具有套件範圍作用域。使用通用名稱可能很容易在其他文件中意外使用錯誤的值。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // We will not see a compile error if the first line of
  // Bar() is deleted.
}
```

</td><td>

```go
// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)
```

</td></tr>
</tbody></table>

**例外**：未導出的錯誤值可以使用不帶底線的前缀 `err`。 参見[錯誤命名](#錯誤命名)。

### 結構體中的嵌入

嵌入式類型（例如 mutex）應位於結構體内的成員 list 的顶部，並且必須有一個空行將嵌入式成員與常規成員分隔開。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Client struct {
  version int
  http.Client
}
```

</td><td>

```go
type Client struct {
  http.Client

  version int
}
```

</td></tr>
</tbody></table>

内嵌應該提供切實的好處，比如以語義上合適的方式加入或增强功能。
它應該在對使用者沒有任何不利影響的情況下使用。（另請参見：[避免在公共結構中嵌入類型]）。

例外：即使在未導出類型中，Mutex 也不應該作為内嵌成員。另請参見：[零值 Mutex 是有效的]。

[避免在公共結構中嵌入類型]: #避免在公共結構中嵌入類型
[零值 Mutex 是有效的]: #零值-mutex-是有效的

嵌入 **不應該**:

- 纯粹是為了美觀或方便。
- 使外部類型更難構造或使用。
- 影響外部類型的零值。如果外部類型有一個有用的零值，則在嵌入内部類型之後應該仍然有一個有用的零值。
- 作為嵌入内部類型的副作用，從外部類型公開不相關的函數或成員。
- 公開未導出的類型。
- 影響外部類型的複製形式。
- 更改外部類型的 API 或類型語義。
- 嵌入内部類型的非規範形式。
- 公開外部類型的實作詳細訊息。
- 允許使用者觀察或控制類型内部。
- 通過包装的方式改變内部函數的一般行為，這種包装方式會给使用者帶來一些意料之外情況。

簡單地說，有意识地和有目的地嵌入。一種很好的測試體验是，
"是否所有這些導出的内部方法/成員都將直接加入到外部類型"
如果答案是`some`或`no`，不要嵌入内部類型 - 而是使用成員。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // Bad: A.Lock() and A.Unlock() 現在可用
    // 不提供任何功能性好處，並允許使用者控制有關 A 的内部細節。
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // Good: Write() 在外層提供用於特定目的，
    // 並且委托工作到内部類型的 Write() 中。
    io.WriteCloser
    count int
}
func (w *countingWriteCloser) Write(bs []byte) (int, error) {
    w.count += len(bs)
    return w.WriteCloser.Write(bs)
}
```

</td></tr>
<tr><td>

```go
type Book struct {
    // Bad: 指標更改零值的有用性
    io.ReadWriter
    // other fields
}
// later
var b Book
b.Read(...)  // panic: nil pointer
b.String()   // panic: nil pointer
b.Write(...) // panic: nil pointer
```

</td><td>

```go
type Book struct {
    // Good: 有用的零值
    bytes.Buffer
    // other fields
}
// later
var b Book
b.Read(...)  // ok
b.String()   // ok
b.Write(...) // ok
```

</td></tr>
<tr><td>

```go
type Client struct {
    sync.Mutex
    sync.WaitGroup
    bytes.Buffer
    url.URL
}
```

</td><td>

```go
type Client struct {
    mtx sync.Mutex
    wg  sync.WaitGroup
    buf bytes.Buffer
    url url.URL
}
```

</td></tr>
</tbody></table>

### 本地變數宣告

如果將變數明確設置為某個值，則應使用短變數宣告形式 (`:=`)。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var s = "foo"
```

</td><td>

```go
s := "foo"
```

</td></tr>
</tbody></table>

但是，在某些情況下，`var` 使用關键字時預設值會更清晰。例如，[宣告空切片]。

[宣告空切片]: https://github.com/golang/go/wiki/CodeReviewComments#declaring-empty-slices

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td><td>

```go
func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td></tr>
</tbody></table>

### nil 是一個有效的 slice

`nil` 是一個有效的長度為 0 的 slice，這意味著，

- 您不應明確返回長度為零的切片。應該返回`nil` 來代替。

  <table>
  <thead><tr><th>Bad</th><th>Good</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  if x == "" {
    return []int{}
  }
  ```

  </td><td>

  ```go
  if x == "" {
    return nil
  }
  ```

  </td></tr>
  </tbody></table>

- 要檢查切片是否為空，請始终使用 `len(s) == 0`。而非 `nil`。

  <table>
  <thead><tr><th>Bad</th><th>Good</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  func isEmpty(s []string) bool {
    return s == nil
  }
  ```

  </td><td>

  ```go
  func isEmpty(s []string) bool {
    return len(s) == 0
  }
  ```

  </td></tr>
  </tbody></table>

- 零值切片（用 `var` 宣告的切片）可立即使用，無需調用 `make()` 建立。

  <table>
  <thead><tr><th>Bad</th><th>Good</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  nums := []int{}
  // or, nums := make([]int)

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td><td>

  ```go
  var nums []int

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td></tr>
  </tbody></table>

記住，雖然 nil 切片是有效的切片，但它不等於長度為 0 的切片（一個為 nil，另一個不是），並且在不同的情況下（例如序列化），這兩個切片的處理方式可能不同。

### 缩小變數作用域

如果有可能，盡量缩小變數作用範圍。除非它與 [減少嵌套](#減少嵌套)的規則衝突。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
err := ioutil.WriteFile(name, data, 0644)
if err != nil {
 return err
}
```

</td><td>

```go
if err := ioutil.WriteFile(name, data, 0644); err != nil {
 return err
}
```

</td></tr>
</tbody></table>

如果需要在 if 之外使用函數調用的结果，則不應嘗試缩小範圍。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
if data, err := ioutil.ReadFile(name); err == nil {
  err = cfg.Decode(data)
  if err != nil {
    return err
  }

  fmt.Println(cfg)
  return nil
} else {
  return err
}
```

</td><td>

```go
data, err := ioutil.ReadFile(name)
if err != nil {
   return err
}

if err := cfg.Decode(data); err != nil {
  return err
}

fmt.Println(cfg)
return nil
```

</td></tr>
</tbody></table>

### 避免参數語義不明確 (Avoid Naked Parameters)

函數調用中的`意義不明確的参數`可能會损害可讀性。當参數名稱的含義不明顯時，請為参數加入 C 樣式註釋 (`/* ... */`)

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)
```

</td><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)
```

</td></tr>
</tbody></table>

對於上面的示例程式碼，還有一種更好的處理方式是將上面的 `bool` 類型换成自定義類型。將來，該参數可以支持不僅僅局限於兩個狀態（true/false）。

```go
type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady Status= iota + 1
  StatusDone
  // Maybe we will have a StatusInProgress in the future.
)

func printInfo(name string, region Region, status Status)
```

### 使用原始字串字面值，避免轉義

Go 支持使用 [原始字串字面值](https://golang.org/ref/spec#raw_string_lit)，也就是 " ` " 來表示原生字串，在需要轉義的場景下，我們應該盡量使用這種方案來替换。

可以跨越多行並包含引號。使用這些字串可以避免更難阅讀的手工轉義的字串。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
wantError := "unknown name:\"test\""
```

</td><td>

```go
wantError := `unknown error:"test"`
```

</td></tr>
</tbody></table>

### 初始化結構體

#### 使用成員名初始化結構

初始化結構時，幾乎應該始终指定成員名。目前由 [`go vet`] 强制納秒執行。

[`go vet`]: https://golang.org/cmd/vet/

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
k := User{"John", "Doe", true}
```

</td><td>

```go
k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}
```

</td></tr>
</tbody></table>

例外：當有 3 個或更少的成員時，測試表中的成員名*may*可以省略。

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```
#### 省略結構中的零值成員

初始化具有成員名稱的結構時，除非提供有意義的 context ，否則忽略值為零的成員。
也就是，讓我們自動將這些設置為零值

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
  MiddleName: "",
  Admin: false,
}
```

</td><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
}
```

</td></tr>
</tbody></table>

這有助於通過省略該 context 中的預設值來減少閱讀的障礙。只指定有意義的值。

在成員名提供有意義 context 的地方包含零值。例如，[表驅動測試](#表驅動測試) 中的測試用例可以受益於成員的名稱，即使它們是零值的。

```go
tests := []struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```
#### 對零值結構使用 `var`

如果在宣告中省略了結構的所有成員，請使用 `var` 宣告結構。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{}
```

</td><td>

```go
var user User
```

</td></tr>
</tbody></table>

這將零值結構與那些具有類似於為 [初始化 Maps](#初始化-maps) 建立的，區別於非零值成員的結構區分開來，
並與我們更喜歡的 [宣告空切片] 方式相匹配。

#### 初始化 Struct 引用

在初始化結構引用時，請使用`&T{}`代替`new(T)`，以使其與結構體初始化一致。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
sval := T{Name: "foo"}

// inconsistent
sptr := new(T)
sptr.Name = "bar"
```

</td><td>

```go
sval := T{Name: "foo"}

sptr := &T{Name: "bar"}
```

</td></tr>
</tbody></table>

### 初始化 Maps

對於空 map 請使用 `make(..)` 初始化， 並且 map 是通過编程方式填充的。
這使得 map 初始化在表現上不同於宣告，並且它還可以方便地在 make 後加入大小提示。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 讀寫安全;
  // m2 在寫入時會 panic
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 讀寫安全;
  // m2 在寫入時會 panic
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

宣告和初始化看起來非常相似的。

</td><td>

宣告和初始化看起來差別非常大。

</td></tr>
</tbody></table>

在盡可能的情況下，請在初始化時提供 map 容量大小，詳細請看 [指定 Map 容量提示](#指定Map容量提示)。


另外，如果 map 包含固定的元素 list ，則使用 map literals (map 初始化 list ) 初始化映射。


<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3
```

</td><td>

```go
m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}
```

</td></tr>
</tbody></table>

基本準則是：在初始化時使用 map 初始化 list  來加入一组固定的元素。否則使用 `make` (如果可以，請盡量指定 map 容量)。

### 字串 string format

如果你在函數外宣告 `Printf`-style 函數的格式字串，請將其設置為 `const` 常數。

這有助於 `go vet` 對格式字串納秒執行靜態分析。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
msg := "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td><td>

```go
const msg = "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td></tr>
</tbody></table>

### 命名 Printf 樣式的函數

宣告 `Printf`-style 函數時，請確保 `go vet` 可以检测到它並檢查格式字串。

這意味著您應盡可能使用预定義的 `Printf`-style 函數名稱。`go vet` 將預設檢查這些。有關更多訊息，請参見 [Printf 系列]。

[Printf 系列]: https://golang.org/cmd/vet/#hdr-Printf_family

如果不能使用预定義的名稱，請以 f 结束選擇的名稱：`Wrapf`，而不是 `Wrap`。`go vet` 可以要求檢查特定的 Printf 樣式名稱，但名稱必須以 `f` 结尾。

```shell
$ go vet -printfuncs=wrapf,statusf
```

另請參考 [go vet: Printf family check].

[go vet: Printf family check]: https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/

## 编程模式

### 表驅動測試

當測試邏輯是重複的時候，通過  [subtests] 使用 table 驅動的方式撰寫 case 程式碼看上去會更簡潔。

[subtests]: https://blog.golang.org/subtests

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestSplitHostPort(t *testing.T)

host, port, err := net.SplitHostPort("192.0.2.0:8000")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("192.0.2.0:http")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "http", port)

host, port, err = net.SplitHostPort(":8000")
require.NoError(t, err)
assert.Equal(t, "", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("1:8")
require.NoError(t, err)
assert.Equal(t, "1", host)
assert.Equal(t, "8", port)
```

</td><td>

```go
// func TestSplitHostPort(t *testing.T)

tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  {
    give:     "192.0.2.0:8000",
    wantHost: "192.0.2.0",
    wantPort: "8000",
  },
  {
    give:     "192.0.2.0:http",
    wantHost: "192.0.2.0",
    wantPort: "http",
  },
  {
    give:     ":8000",
    wantHost: "",
    wantPort: "8000",
  },
  {
    give:     "1:8",
    wantHost: "1",
    wantPort: "8",
  },
}

for _, tt := range tests {
  t.Run(tt.give, func(t *testing.T) {
    host, port, err := net.SplitHostPort(tt.give)
    require.NoError(t, err)
    assert.Equal(t, tt.wantHost, host)
    assert.Equal(t, tt.wantPort, port)
  })
}
```

</td></tr>
</tbody></table>

很明顯，使用 test table 的方式在程式碼邏輯擴展的時候，比如新增 test case，都會顯得更加的清晰。

我們遵循這樣的約定：將結構體切片稱為`tests`。 每個測試用例稱為`tt`。此外，我們鼓勵使用`give`和`want`前缀說明每個測試用例的輸入和輸出值。

```go
tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  // ...
}

for _, tt := range tests {
  // ...
}
```

並行測試，比如一些專門的循環（例如，生成goroutine或捕獲引用作為循環體的一部分的那些循環）
必須注意在循環的範圍内顯式地分配循環變數，以確保它們保持预期的值。

```go
tests := []struct{
  give string
  // ...
}{
  // ...
}
for _, tt := range tests {
  tt := tt // for t.Parallel
  t.Run(tt.give, func(t *testing.T) {
    t.Parallel()
    // ...
  })
}
```

在上面的例子中，由於下面使用了`t.Parallel()`，我們必須宣告一個作用域為循環迭代的`tt`變數。
如果我們不這樣做，大多數或所有測試都會收到一個意外的`tt`值，或者一個在運作時發生變化的值。

### 功能選項

功能選項是一種模式，您可以在其中宣告一個不透明 Option 類型，該類型在某些内部結構中紀錄訊息。您接受這些選項的可變编號，並根據内部結構上的選項紀錄的全部訊息採取行動。

將此模式用於您需要擴展的構造函數和其他公共 API 中的可選参數，尤其是在這些功能上已经具有三個或更多参數的情況下。

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// package db

func Open(
  addr string,
  cache bool,
  logger *zap.Logger
) (*Connection, error) {
  // ...
}
```

</td><td>

```go
// package db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

必須始终提供缓存和紀錄器参數，即使使用者希望使用預設值。

```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

只有在需要時才提供選項。

```go
db.Open(addr)
db.Open(addr, db.WithLogger(log))
db.Open(addr, db.WithCache(false))
db.Open(
  addr,
  db.WithCache(false),
  db.WithLogger(log),
)
```

</td></tr>
</tbody></table>

我們建議實作此模式的方法是使用一個 `Option` 介面，該介面保存一個未導出的方法，在一個未導出的 `options` 結構上紀錄選項。

```go
type options struct {
  cache  bool
  logger *zap.Logger
}

type Option interface {
  apply(*options)
}

type cacheOption bool

func (c cacheOption) apply(opts *options) {
  opts.cache = bool(c)
}

func WithCache(c bool) Option {
  return cacheOption(c)
}

type loggerOption struct {
  Log *zap.Logger
}

func (l loggerOption) apply(opts *options) {
  opts.logger = l.Log
}

func WithLogger(log *zap.Logger) Option {
  return loggerOption{Log: log}
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  options := options{
    cache:  defaultCache,
    logger: zap.NewNop(),
  }

  for _, o := range opts {
    o.apply(&options)
  }

  // ...
}
```

注意：還有一種使用閉包實作這個模式的方法，但是我們相信上面的模式為作者提供了更多的靈活性，並且更容易對使用者進行調試和測試。特別是，在不可能進行比較的情況下它允許在測試和模拟中對選項進行比較。此外，它還允許選項實作其他介面，包括 `fmt.Stringer`，允許使用者讀取選項的字串表示形式。

還可以参考下面資料：

- [Self-referential functions and the design of options]
- [Functional options for friendly APIs]

  [Self-referential functions and the design of options]: https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html
  [Functional options for friendly APIs]: https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis

<!-- TODO: replace this with parameter structs and functional options, when to
use one vs other -->

## Linting

比任何 "blessed" linter 集更重要的是，lint 在一個函式庫中始终保持一致。

我們建議至少使用以下 linters，因為我認為它們有助於發現最常見的問題，並在不需要規定的情況下為程式碼質量建立一個高標準：

- [errcheck] 以確保錯誤得到處理
- [goimports] 格式化程式碼和管理 imports
- [golint] 指出常見的語法錯誤
- [govet] 分析程式碼中的常見錯誤
- [staticcheck] 各種靜態分析檢查

  [errcheck]: https://github.com/kisielk/errcheck
  [goimports]: https://godoc.org/golang.org/x/tools/cmd/goimports
  [golint]: https://github.com/golang/lint
  [govet]: https://golang.org/cmd/vet/
  [staticcheck]: https://staticcheck.io/


### Lint Runners

我們推薦 [golangci-lint] 作為 go-to lint 的運作程式，這主要是因為它在較大的函式庫中的性能以及能夠同時配置和使用許多規範。這個 repo 有一個示例配置文件 [.golangci.yml] 和推薦的 linter 設置。

golangci-lint 有 [various-linters] 可供使用。建議將上述 linters 作為基本 set，我們鼓勵團隊加入對他們的项目有意義的任何附加 linters。

[golangci-lint]: https://github.com/golangci/golangci-lint
[.golangci.yml]: https://github.com/uber-go/guide/blob/master/.golangci.yml
[various-linters]: https://golangci-lint.run/usage/linters/


## Stargazers over time

[![Stargazers over time](https://starchart.cc/ianchen0119/uber_go_guide_tw.svg)](https://starchart.cc/ianchen0119/uber_go_guide_tw)
