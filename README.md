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
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

<!-- markdownlint-disable MD033 MD041 -->

## [uber-go/guide](https://github.com/uber-go/guide) 的繁體中文翻譯

## [English](https://github.com/uber-go/guide/blob/master/style.md)

## Uber Go 語言撰寫規範

[Uber](https://www.uber.com/) 是一家美國矽谷的科技公司，同時也是 Go 語言的早期 adoptor。它開源了許多 Golang 相關的專案，像是 Gopher 圈熟知的 [zap](https://github.com/uber-go/zap)、[jaeger](https://github.com/jaegertracing/jaeger) 等。2018 年年末 Uber 將內部的 [Go 語言撰寫規範](https://github.com/uber-go/guide) 開源到 GitHub 上，經過一年多的累積與貢獻，該規範已經初具規模，並且廣受 Gopher 們的關注。本文是該規範的繁體中文版本，內容會以原版為參考盡快更新。

> 原翻譯參考並修改自 [uber-go/guide 的中文翻譯](https://github.com/xxjwxc/uber_go_guide_cn)，部分段落有進行重寫使語意通順。在這邊感謝開源社群的貢獻，讓眾多開發者可以免費使用這些學習資源。

## 版本

- 目前版本：[[2023-03-22] commit:#176](https://github.com/uber-go/guide/commit/a61a8f5fb464715f196a2040afe744f7766ae91b)
- 如果讀者發現原版本有任何更新、問題與內容的改進，歡迎大家幫忙貢獻到此專案中

## 目錄

- [uber-go/guide 的繁體中文翻譯](#uber-goguide-的繁體中文翻譯)
- [English](#english)
- [Uber Go 語言撰寫規範](#uber-go-語言撰寫規範)
- [版本](#版本)
- [目錄](#目錄)
- [介紹](#介紹)
- [指導準則](#指導準則)
  - [指向介面的指標](#指向介面的指標)
  - [介面合理性驗證](#介面合理性驗證)
  - [接收器 (receiver) 與介面](#接收器-receiver-與介面)
  - [零值 Mutex 是有效的](#零值-mutex-是有效的)
  - [在邊界處複製切片和 Maps](#在邊界處複製切片和-maps)
    - [接收切片和 Maps](#接收切片和-maps)
    - [回傳切片或 Maps](#回傳切片或-maps)
  - [使用 defer 釋放資源](#使用-defer-釋放資源)
  - [Channel 的 size 要麼是 1，不然就是無暫存的](#channel-的-size-要麼是-1不然就是無暫存的)
  - [列舉從 1 開始](#列舉從-1-開始)
  - [使用 time 處理時間](#使用-time-處理時間)
    - [使用 `time.Time` 表達瞬時時間](#使用-timetime-表達瞬時時間)
    - [使用 `time.Duration` 表達時間區段](#使用-timeduration-表達時間區段)
    - [對外部系統使用 `time.Time` 和 `time.Duration`](#對外部系統使用-timetime-和-timeduration)
  - [錯誤](#錯誤)
    - [錯誤的類型](#錯誤的類型)
    - [錯誤封裝](#錯誤封裝)
    - [錯誤命名](#錯誤命名)
  - [處理斷言失敗](#處理斷言失敗)
  - [不要使用 panic](#不要使用-panic)
  - [使用 go.uber.org/atomic](#使用-gouberorgatomic)
  - [避免可變的全域變數](#避免可變的全域變數)
  - [避免在公用結構體中嵌入類型](#避免在公用結構體中嵌入類型)
  - [避免使用內建名稱](#避免使用內建名稱)
  - [避免使用 `init()`](#避免使用-init)
  - [附加 (Append) 時優先指定切片大小](#附加-append-時優先指定切片大小)
  - [主函式結束方式](#主函式結束方式)
    - [只結束一次](#只結束一次)
  - [在序列化結構中使用成員標籤](#在序列化結構中使用成員標籤)
  - [要對建立的 goroutine 負責](#要對建立的-goroutine-負責)
    - [等待 goroutines 結束](#等待-goroutines-結束)
    - [不要在 `init()` 使用 goroutines](#不要在-init-使用-goroutines)
- [效能](#效能)
  - [優先使用 strconv 而不是 fmt](#優先使用-strconv-而不是-fmt)
  - [避免字串到字元的轉換](#避免字串到字元的轉換)
  - [指定容器容量](#指定容器容量)
    - [指定 Map 容量提示](#指定-map-容量提示)
    - [指定切片容量](#指定切片容量)
- [規範](#規範)
  - [列內文字不要過長](#列內文字不要過長)
  - [一致性](#一致性)
  - [相似的宣告放在一組](#相似的宣告放在一組)
  - [import 分組](#import-分組)
  - [套件名稱](#套件名稱)
  - [函式名稱](#函式名稱)
  - [匯入別名](#匯入別名)
  - [函式分組與順序](#函式分組與順序)
  - [減少巢套（Nesting）](#減少巢套nesting)
  - [不必要的 else](#不必要的-else)
  - [上層變數宣告](#上層變數宣告)
  - [對於未匯出的上層常數和變數，使用 _ 作為前綴](#對於未匯出的上層常數和變數使用-_-作為前綴)
  - [結構體中的嵌入](#結構體中的嵌入)
  - [本地變數宣告](#本地變數宣告)
  - [nil 是一個有效的 slice](#nil-是一個有效的-slice)
  - [縮小變數作用域](#縮小變數作用域)
  - [避免參數語義不明確 (Avoid Naked Parameters)](#避免參數語義不明確-avoid-naked-parameters)
  - [使用原始字串字面值，避免轉義](#使用原始字串字面值避免轉義)
  - [初始化結構體](#初始化結構體)
    - [使用欄位名稱初始化結構體](#使用欄位名稱初始化結構體)
    - [省略結構體中的零值欄位](#省略結構體中的零值欄位)
    - [對零值結構體使用 `var`](#對零值結構體使用-var)
    - [初始化 Struct 引用](#初始化-struct-引用)
  - [初始化 Maps](#初始化-maps)
  - [字串 string format](#字串-string-format)
  - [命名 Printf 樣式的函式](#命名-printf-樣式的函式)
- [編程模式](#編程模式)
  - [表格驅動測試](#表格驅動測試)
  - [函式型選項](#函式型選項)
- [Linting](#linting)
  - [Lint Runners](#lint-runners)
- [Stargazers over time](#stargazers-over-time)

## 介紹

樣式 (*style*) 是支配我們程式碼的慣例。實際上由於這些約定不僅僅只涵蓋程式碼格式化的部分（`gofmt` 能替我們處理），術語「**樣式**」不是很恰當的說法。

本指南的目的是透過詳細描述在 Uber 撰寫 Go 程式碼的注意事項來梳理這種複雜的規則。這些規則的存在是為了使函式庫易於管理，同時也能讓工程師能更有效地運用 Go 語言的功能。

該指南最初由 [Prashant Varanasi] 和 [Simon Newton] 編寫，目的是使一些同事能快速使用 Go。多年來，這份指南已根據其他人的回饋進行數次修訂。

  [Prashant Varanasi]: https://github.com/prashantv
  [Simon Newton]: https://github.com/nomis52

本文件記錄了我們在 Uber 遵循的 Go 程式碼中的慣用約定。其中許多是 Go 的通則，而其他準則是延伸自這些外部資源：

1. [Effective Go](https://golang.org/doc/effective_go.html)
2. [Go Common Mistakes](https://github.com/golang/go/wiki/CommonMistakes)
3. [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)

程式碼範例部分，主要目標是能使最新兩個小[版本](https://go.dev/doc/devel/release)的 Go 得以準確執行。

所有程式碼都應該透過 `golint` 和 `go vet` 的檢查並無錯誤。我們建議您將編輯器設定為：

- 儲存時使用 `goimports`
- 使用 `golint` 和 `go vet` 檢查錯誤

您可以在以下 Go 編輯器工具的相關頁面中找到更為詳細的資訊：
<https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins>

## 指導準則

### 指向介面的指標

大部分情況下都不需要指向介面 (*interface*) 類型的指標 (*pointer*)。您應該將介面作為數值進行傳遞，在這樣的傳遞過程中，實質上傳遞的基底 (*underlying*) 資料仍然可以是指標。

介面由兩個欄位構成：

1. 指向某個特定類型資訊的指標。您可以將其視為「類型 (*type*)」。
2. 資料指標。如果儲存的資料是指標，則直接儲存；如果儲存的資料是數值，則儲存指向這個數值的指標。

如果想要介面方法修改基底資料，則必須使用指標傳遞（將物件指標賦值給介面變數）。

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

// f1.f() 無法修改基底資料
// f2.f() 可以修改基底資料，給介面變數 f2 賦值時使用的是物件指標
var f1 F = S1{}
var f2 F = &S2{}
```

### 介面合理性驗證

可以的話，在編譯時驗證介面的合理性，包括：

- 需要用來實作特定介面以滿足 API 契約的匯出類型
- 實作同個介面，屬於一整組的匯出或非匯出類型
- 會打斷使用者編譯的其它違反介面原則情況

補充[^src-from-cn]：上面 3 點是編譯器對介面的檢查機制，大致意思是錯誤使用介面會在編譯期間報錯。所以可以利用這個機制讓部分問題在編譯期間暴露。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

如果 `*Handler` 與 `http.Handler` 的介面不同，那麼語句 `var _ http.Handler = (*Handler)(nil)` 將無法編譯通過。

賦值的右邊應該是斷言類型的零值——指標類型（如 `*Handler`）、切片和 map (映射) 是 `nil`；結構體類型是空結構體 (`structName{}`)。

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

使用數值接收器的方法，既可以透過數值呼叫，也可以透過指標呼叫。

具指標接收器的方法，只能透過指標或 [可定址數值 (addressable values)] 呼叫。

  [可定址數值 (addressable values)]: https://golang.org/ref/spec#Method_values

例如：

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

// 我們無法取得儲存在 map 中的值的指標，因為它們不是可尋址的數值
sVals := map[int]S{1: {"A"}}

// 我們可以對儲存在 map 中的值呼叫 Read 方法，
// 因為 Read 方法是使用數值接受器，其不要求
// 數值是可以尋址的。
sVals[1].Read()

// 我們不能對儲存在 map 中的值呼叫 Write 方法，
// 因為 Write 方法是使用指標接收器，其無法
// 取得儲存在 map 中的值的指標。
//
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// 如果 map 儲存的是指標，則可以呼叫 Read 和 Write，
// 因為指標本身就可以尋址。
sPtrs[1].Read
sPtrs[1].Write("test")
```

類似的，即使方法有數值接收器，也同樣可以用指標接收器來滿足介面。

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

//  下面程式碼無法透過編譯。因為 s2Val 是數值，而 S2 的 f 方法中沒有使用數值接收器
//   i = s2Val
```

Effective Go 中有一段關於 [Pointers vs. Values] 的精闢講解。

  [Pointers vs. Values]: https://golang.org/doc/effective_go.html#pointers_vs_values

補充[^src-from-cn]：

- 一個類型可以有數值接收器方法集和指標接收器方法集
  - 數值接收器方法集是指標接收器方法集的子集，反之不是
- 規則
  - 數值只能使用數值接收器方法集
  - 指標可以使用 `數值接收器方法集` + `指標接收器方法集`
- 介面的匹配（或稱「實作」）
  - 類型實作了介面的所有方法，叫匹配
  - 具體來說，可以是類型的數值方法集匹配介面，或者是指標方法集匹配介面

具體的匹配分兩種：

- 數值方法集和介面匹配
  - 給介面變數賦值的，不管是數值還是指標都 ok，因為都包含數值方法集
- 指針方法集和介面匹配
  - 只能將指標賦值給介面變數，因為只有指標方法集和介面匹配
  - 如果將數值賦值給介面變數，會在編譯期間報錯（會觸發介面合理性檢查機制）

`i = s2Val` 會報錯的本因，是數值方法集和介面不匹配。

  [^src-from-cn]: 這個補充來源自[簡體中文翻譯](https://github.com/xxjwxc/uber_go_guide_cn)

### 零值 Mutex 是有效的

`sync.Mutex` 和 `sync.RWMutex` 的零值是有效的。所以指向 mutex 的指標基本是不必要的。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

如果你使用結構體 (*struct*) 指標，mutex 應該作為結構體的非指標成員。即使不匯出結構體，也不要直接把 mutex 嵌入 (*embed*) 到結構體中。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

`Mutex` 欄位以及 `Lock` 和 `Unlock` 方法，是 `SMap` 匯出 (*export*) 的 API 中沒有刻意說明的部分。

 </td><td>

mutex 及其方法是 `SMap` 的實作，對其呼叫者來說是不可見的。

 </td></tr>
 </tbody></table>

### 在邊界處複製切片和 maps

切片 (*slices*) 和 maps（映射、對應）包含了指向基底資料的指標，因此在需要複製它們時要特別注意。

#### 接收切片和 maps

請記住，當切片或 map 作為函式參數傳入時，如果您儲存了對它們的引用，則使用者可以對其進行修改。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

> 補充[^src-from-cn]：這是 Go 語言中很基本的深淺複製概念，我們必須要熟記哪些資料結構屬於淺複製，哪些又屬於深複製。在不清楚結構體特性的情況下，很有可能會修改到不應受影響的資料，造成非預期行為發生。

#### 回傳切片或 maps

同樣，留意使用者可能會對暴露內部狀態的切片或 map 進行修改。

<table>
<thead><tr><th>不妥做法</th><th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex

  counters map[string]int
}

// Snapshot 回傳目前狀態。
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// snapshot 不再受互斥鎖保護
// 因此對 snapshot 的任何存取都將受到 data races 影響
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

// snapshot 現在是一個複本
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>

### 使用 defer 釋放資源

使用 defer 釋放資源，諸如檔案和鎖。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

// 當有多個 return 分支時，很容易遺忘 unlock
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

Defer 的開銷非常小，除非您可以證明函式執行時間在奈秒級的程度，否則都應該用 Defer。因為使用 defer 的成本微不足道，因此值得使用 defer 提升可讀性。這尤其適用於那些不單單進行簡單記憶體存取的規模較大方法——在這些方法中，其他計算的資源消耗遠超過 `defer`。

### Channel 的 size 要麼是 1，不然就是無暫存的

channel 通常 size 應為 1 或是無暫存的。預設情況下，channel 是無暫存的，其 size 為 0。任何其他大小都必須經過嚴格的檢查。我們需要考慮如何確定大小，考慮是什麼阻止了 channel 在高負載下和阻塞型寫入時的寫入，以及當這種情況發生時系統邏輯有哪些變化。（翻譯解釋[^src-from-cn]：按照原文意思是需要界定通道邊界、競爭條件，以及邏輯上下文的梳理）

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

### 列舉從 1 開始

在 Go 中引入列舉的標準方法是宣告一個自訂類型和一個使用了 iota 的 const 組。由於變數的預設值為 0，因此通常應以非零值開頭列舉。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

在某些情況下，使用零值是有意義的（列舉從零開始）。例如當零值是理想的預設行為時。

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
3. 一週有七天
4. 一年 365 天
5. [還有更多](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

例如 **第一點**（在瞬時時間加上 24 小時）並不總是產生一個新的日曆日。

因此，在處理時間時始終使用 [`"time"`] 套件，因為它有助於以更安全、更準確的方式處理這些不正確的假設。

  [`"time"`]: https://golang.org/pkg/time/

#### 使用 `time.Time` 表達瞬時時間

使用 [`time.Time`] 處理瞬時時間 (*instant of time*)，以及使用 `time.Time` 中的方法比較、加入或減去時間。

  [`time.Time`]: https://golang.org/pkg/time/#Time

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

回到「往瞬時時間加上 24 小時」的例子，該使用什麼方法，取決於意圖是什麼。如果我們想要下一個日曆日（目前天的下一天）的同一個時間點，我們應該使用 [`Time.AddDate`]。但是，如果我們想保證某一時刻比前一時刻晚 24 小時，我們應該使用 [`Time.Add`]。

  [`Time.AddDate`]: https://golang.org/pkg/time/#Time.AddDate
  [`Time.Add`]: https://golang.org/pkg/time/#Time.Add

```go
newDay := t.AddDate(0 /* years */, 0 /* months */, 1 /* days */)
maybeNewDay := t.Add(24 * time.Hour)
```

#### 對外部系統使用 `time.Time` 和 `time.Duration`

盡可能在與外部系統的互動中使用 `time.Duration` 和 `time.Time`。例如 :

- 命令列旗標: [`flag`] 透過 [`time.ParseDuration`] 支援 `time.Duration`
- JSON: [`encoding/json`] 透過其 [`UnmarshalJSON` 方法]支援將 `time.Time` 編碼為 [RFC 3339] 字串
- SQL: [`database/sql`] 支援將 `DATETIME` 或 `TIMESTAMP` 列轉換為 `time.Time`，如果底層驅動程式支援則回傳
- YAML: [`gopkg.in/yaml.v2`] 支援將 `time.Time` 作為 [RFC 3339] 字串，並透過 [`time.ParseDuration`] 支援 `time.Duration`

  [`flag`]: https://golang.org/pkg/flag/
  [`time.ParseDuration`]: https://golang.org/pkg/time/#ParseDuration
  [`encoding/json`]: https://golang.org/pkg/encoding/json/
  [RFC 3339]: https://tools.ietf.org/html/rfc3339
  [`UnmarshalJSON` 方法]: https://golang.org/pkg/time/#Time.UnmarshalJSON
  [`database/sql`]: https://golang.org/pkg/database/sql/
  [`gopkg.in/yaml.v2`]: https://godoc.org/gopkg.in/yaml.v2

當不能在這些操作中使用 `time.Duration`，請改用 `int` 或 `float64`，並在欄位名稱中加上單位。

例如，由於 `encoding/json` 不支援 `time.Duration`，因此在欄位名稱中加上單位：

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

如果這些操作不能使用 `time.Time`，除非達成一致，否則請使用 `string` 和 [RFC 3339] 中定義的格式時間戳。預設情況下，[`Time.UnmarshalText`] 使用此格式，並可透過 [`time.RFC3339`] 在 `Time.Format` 和 `time.Parse` 中使用。

  [`Time.UnmarshalText`]: https://golang.org/pkg/time/#Time.UnmarshalText
  [`time.RFC3339`]: https://golang.org/pkg/time/#RFC3339

儘管這在實務中並不成問題，但請記住，`"time"` 套件不支援解析閏秒時戳（[8728]），也不在計算中考慮閏秒問題（[15190]）。如果您比較兩個瞬時時間 (*instant time*)，則差異將不包括這兩個瞬間之間可能發生的閏秒。

  [8728]: https://github.com/golang/go/issues/8728
  [15190]: https://github.com/golang/go/issues/15190

<!-- TODO: section on String methods for enums -->

### 錯誤

#### 錯誤的類型

宣告錯誤的選項很少。在選擇最適合您用例的選項之前，請思考：

- 呼叫者是否需要匹配錯誤，方便呼叫者進行處置？如果需要，則必須透過宣告頂層錯誤變數或自訂類型，方可支援 [`errors.Is`] 或 [`errors.As`] 函式
- 錯誤訊息是靜態字串，還是需要上下文 (*context*) 資訊的動態字串？如果是靜態字串，我們可以使用 [`errors.New`]；但對於後者，則必須使用 [`fmt.Errorf`] 或自訂錯誤的類型
- 我們是否正在傳遞由下游函式回傳的新錯誤？如果是這樣，請參考[〈錯誤封裝〉](#錯誤封裝)

  [`errors.Is`]: https://golang.org/pkg/errors/#Is
  [`errors.As`]: https://golang.org/pkg/errors/#As

| 錯誤匹配？ | 錯誤訊息 | 建議                           |
| ---------- | -------- | ------------------------------ |
| 不用       | 靜態字串 | [`errors.New`]                 |
| 不用       | 動態字串 | [`fmt.Errorf`]                 |
| 需要       | 靜態字串 | 頂層 `var` 搭配 [`errors.New`] |
| 需要       | 動態字串 | 自訂 `error` 類型              |

  [`errors.New`]: https://golang.org/pkg/errors/#New
  [`fmt.Errorf`]: https://golang.org/pkg/fmt/#Errorf

舉例來說：使用 [`errors.New`] 表示帶有靜態字串的錯誤。如果呼叫者需要匹配並處理此錯誤，則將此錯誤匯出為變數，這樣子才能將其與 `errors.Is` 匹配。

<table>
<thead><tr><th>無錯誤匹配</th><th>有錯誤匹配</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open() error {
  return errors.New("could not open")
}

// package bar

if err := foo.Open(); err != nil {
  // 無法處置錯誤
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
    // 處置錯誤
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

對於動態字串的錯誤，如果呼叫者不需要匹配它，則使用 [`fmt.Errorf`]；如果呼叫者需要匹配它，則自訂 `error`。

<table>
<thead><tr><th>無錯誤匹配</th><th>有錯誤匹配</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open(file string) error {
  return fmt.Errorf("file %q not found", file)
}

// package bar

if err := foo.Open("testfile.txt"); err != nil {
  // 無法處置錯誤
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
    // 處置錯誤
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

請注意，如果您從套件中匯出錯誤變數或類型，它們將成為套件公用 API 的一部分。

#### 錯誤封裝

如果呼叫其他方法時出現錯誤，通常有三種處理方式可以選擇：

- 將原始錯誤原樣回傳
- 使用 `fmt.Errorf` 搭配 `%w` 加入上下文
- 使用 `fmt.Errorf` 搭配 `%v` 加入上下文

如果沒有要加入其他上下文，則按原樣回傳原始錯誤。這將保留原始錯誤的類型和訊息。如果基底 (*undelying*) 錯誤訊息已有足夠資訊追蹤其來源，則十分適合使用這種做法。

否則，請盡量往錯誤訊息加入上下文，這樣就不會出現諸如「連線被拒」之類的模糊錯誤。加入上下文可以得到更多實用的錯誤，例如「呼叫服務 foo：連接被拒絕。」

使用 `fmt.Errorf` 為你的錯誤加入上下文，並根據呼叫者是否應該可以匹配和擷取基底原因，來在 `%w` 或 `%v` 動詞間抉擇。

- 如果呼叫者應當可以存取基底錯誤，請使用 `%w`。對於大多數封裝型錯誤，可以直接採用這種方式，但注意呼叫者可能會開始依賴此行為。因此，針對封裝型錯誤是已知變數或類型的情況，請將其作為函式契約的一部分進行記錄和測試
- 使用 `%v` 混淆基底錯誤。呼叫者將無法匹配它，但如果需要，未來您可以切換到 `%w`

在往回傳錯誤加上上下文時，請不要加上「無法……」(failed to…) 之類的短語，以維持上下文的簡潔。當錯誤透過堆疊向上增加時，它會被一層一層的堆疊起來：

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

```plain
failed to x: failed to y: failed to create new store: the error
```

</td><td>

```plain
x: y: new store: the error
```

</td></tr>
</tbody></table>

然而，一旦錯誤被寄送到另一個系統，應該寫明訊息是一個錯誤（例如加上 `err` 標籤，或在記錄檔 (*logs*) 中加上 `Failed` 前綴）。

另見 [不要只檢查錯誤，優雅地處理它們]。

  [不要只檢查錯誤，優雅地處理它們]: https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully

#### 錯誤命名

對於儲存為全域變數的錯誤值，根據是否匯出 (*exported*) 使用前綴 `Err` 或 `err`。請參閱指南 [對於未匯出的上層常數和變數，使用 _ 作為前綴](#對於未匯出的上層常數和變數使用-_-作為前綴)。

```go
var (
  // 下面兩個錯誤會被匯出，以便此套件的
  // 使用者可以使用 `errors.Is` 匹配錯誤。

  ErrBrokenLink = errors.New("link is broken")
  ErrCouldNotOpen = errors.New("could not open")

  // 這個錯誤沒有被匯出，因為我們不想讓它
  // 成為我們公用 API 的一部分。我們依然可以
  // 在套件中藉由 errors.As 使用到它。
  errNotFound = errors.New("not found")
)
```

對於自訂錯誤的類型，請改用後綴 `Error`。

```go
// 同樣，下面這個錯誤會被匯出，以便此套件
// 的使用者可以使用 `errors.Is` 匹配錯誤。

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

// 而這個錯誤沒有被匯出，因為我們不想讓它
// 成為我們公用 API 的一部分。我們依然可以
// 在套件中藉由 errors.As 使用到它。
type resolveError struct {
  Path string
}

func (e *resolveError) Error() string {
  return fmt.Sprintf("resolve %q", e.Path)
}
```

### 處理斷言失敗

[類型斷言 (*type assertions*)] 的單一回傳值形式，會在遇到錯誤類型時 panic。因此，請始終使用「逗號 ok」習語。

  [類型斷言 (*type assertions*)]: https://golang.org/ref/spec#Type_assertions

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

在生產環境中運作的程式碼必須避免出現 panic。panic 是 [級聯失敗] 的主要原因。如果發生錯誤，該函式必須回傳錯誤，並允許呼叫方決定如何處理它。

  [級聯失敗]: https://en.wikipedia.org/wiki/Cascading_failure

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

panic/recover 不是個錯誤處理策略。僅當發生不可復原的事情（例如：nil 引用）時，程式才必須 panic。程式初始化是一個例外：程式啟動時應使程式中止的不良情況可能會引起 panic。

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

即使在測試程式碼中，也優先使用 `t.Fatal` 或者 `t.FailNow` 而不是 panic 確保失敗能被標記。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  panic("failed to set up test")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  t.Fatal("failed to set up test")
}
```

</td></tr>
</tbody></table>

<!-- TODO: Explain how to use _test packages. -->

### 使用 go.uber.org/atomic

[sync/atomic] 套件的原子操作函式是在原始類型 (`int32`、`int64` 等）進行操作的，因此很容易忘記用原子操作函式來讀取或修改變數。

[go.uber.org/atomic] 透過隱藏基底類型 (*underlying type*)，增加了這些操作的類型安全性。此外，它附帶方便的 `atomic.Bool` 類型。

  [go.uber.org/atomic]: https://godoc.org/go.uber.org/atomic
  [sync/atomic]: https://golang.org/pkg/sync/atomic/

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
type foo struct {
  running int32  // 原子變數
}

func (f* foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // 已經在執行了……
     return
  }
  // 啟動 Foo
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
     // 已經在執行了……
     return
  }
  // 啟動 Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>

### 避免可變的全域變數

不要改變全域變數，而是採用依賴注入。這條規則不僅適用於函式指標，也適用於其他類型的值。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

### 避免在公用結構體中嵌入類型

嵌入這些類型會洩漏實作細節，阻擾類型的演化 (*type evolution*) ，亦使文件混亂、晦澀難懂。

假設你使用共用的 `AbstractList` 實作了多種 list 類型，請避免在具體的 list 實作中嵌入 `AbstractList`。正確的做法是將方法手寫進具體的 list，再使這些方法委託 (*delegate*) 到 `AbstractList` 上。

```go
type AbstractList struct {}
// Add 將實體加入到 list 中。
func (l *AbstractList) Add(e Entity) {
  // ...
}
// Remove 從 list 中移除實體。
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList 是一個實體 list。
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList 是一個實體 list。
type ConcreteList struct {
  list *AbstractList
}
// Add 將實體加入到 list 中。
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}
// Remove 從 list 中移除實體。
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go 允許使用 [類型嵌入](https://golang.org/doc/effective_go.html#embedding)，是繼承和組合取捨後的折衷做法。外層類型獲取嵌入類型的方法的隱式複本。預設情況下，這些方法委託 (*delegate*) 給嵌入實例的同名方法。

結構體亦得到與類型同名的欄位。因此，倘若嵌入類型是公用的 (*public*)，那麼欄位也會公用的。為了保持向後相容性，外部類型的每個後續版本，都得保留嵌入類型。

嵌入類型通常不會是「缺一不可」的——它只是方便您節省撰寫冗長委託方法的時間。

雖然不嵌入結構體，改嵌入相容的 `AbstractList` 介面，可以讓開發者在未來有更大的修改空間，不過這仍然會洩漏出「具體 list 使用抽象實作」的細節。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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
// Add 將 Entity 加進 list。
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}
// Remove 從 list 中移除 Entity。
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

無論是使用嵌入結構還是嵌入介面，都會限制類型的演化。

- 向嵌入介面加入方法會導致破壞性更動
- 從嵌入結構體刪除方法會導致破壞性更動
- 刪除嵌入類型會導致破壞性的更動
- 即使使用滿足相同介面的類型取代嵌入類型，也會導致破壞性更動

儘管撰寫這些委託方法乏味無趣，但這些多做的工作不僅可以隱藏實作，留下了更多的更改機會，還消除了在文件中間接發現完整 `List` 介面的可能。

### 避免使用內建名稱

Go [語言規範] 概述了幾個內建的，不應在 Go 專案中使用的 [預先宣告的識別元]。

根據情境的不同，將這些識別元作為名稱重複使用，可能會在目前的語意範圍（或任何巢套的語意範圍）中隱藏原始識別元，或者使受影響的程式碼變得令人困惑。在最好的情況下，編譯器會報錯；在最壞的情況下，這樣的程式碼可能會引入潛在的、難以搜尋 (grep) 的錯誤。

[語言規範]: https://golang.org/ref/spec
[預先宣告的識別元]: https://golang.org/ref/spec#Predeclared_identifiers

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
var error string
// `error` 蓋住內建識別元

// 或

func handleErrorMessage(error string) {
    // `error` 蓋住內建識別元
}
```

</td><td>

```go
var errorMessage string
// `error` 指向內建識別元

// 或

func handleErrorMessage(msg string) {
    // `error` 指向內建識別元
}
```

</td></tr>
<tr><td>

```go
type Foo struct {
  // 雖然技術上來說，這些欄位不會蓋住內建識別元，
  // 但搜尋 (grep) `error` 或 `string` 字串，
  // 現在得到的結果很容易造成混淆。
  error  error
  string string
}

func (f Foo) Error() error {
  // `error` 和 `f.error` 看起來相仿
  return f.error
}

func (f Foo) String() string {
  // `string` and `f.string` 看起來相仿
  return f.string
}
```

</td><td>

```go
type Foo struct {
    // `error` 和 `string` 現在十分明確。
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

注意，編譯器不會因為使用預先宣告的識別元而產生錯誤，但像 `go vet` 這樣的工具，應能正確指出這些和其他情況的蓋住情況。

### 避免使用 `init()`

盡量避免使用 `init()`。如果無法避開使用 `init()`，或者確實需要使用 `init()`，程式碼應先嘗試：

1. 無論程式環境或呼叫方式如何，結果都要是完全可以確定的。
2. 避免仰賴其他 `init()` 函式的順序或副作用。雖然 `init()` 順序現在是明確的，但程式碼是會更動的——`init()` 函式之間的關係可能會使程式碼變得脆弱且容易出錯。
3. 避免存取或操作全域或環境狀態，如機器資訊、環境變數、工作目錄、程式引數和輸入等。
4. 避免進行 `I/O` 操作，包括檔案系統、網路和系統呼叫。

無法滿足這些要求的程式碼，可能更屬於要讓 `main()`（或程式生命週期中的其他地方）呼叫的輔助函式，或者寫進 `main()` 裡頭。尤其是打算讓其他程式使用的函式庫，應該要更加注意「完全確定性」而不應搞「init 魔法」。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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
// 或為了更好的可測試性：
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
    // 不妥做法：基於目前目錄
    cwd, _ := os.Getwd()
    // 不妥做法：I/O
    raw, _ := os.ReadFile(
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
    // 處理 err
    raw, err := os.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    // 處理 err
    var config Config
    yaml.Unmarshal(raw, &config)
    return config
}
```

</td></tr>
</tbody></table>

考量上述要求，某些情況下 `init()` 可能更能勝任，或者是必要的。這些情況可能包括：

- 不能表示為單個賦值的複雜表示式
- 可插入的掛勾，如 `database/sql` 延伸物、編碼類型登錄檔等
- 最佳化 [Google Cloud Functions] 和其他形式的確定性預計算

  [Google Cloud Functions]: https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations

### 附加 (append) 時優先指定切片大小

在初始化要附加的切片時，盡量向 `make()` 傳入大小參數。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

```plain
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```plain
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>

### 主函式結束方式

Go 程式使用 [`os.Exit`] 或者 [`log.Fatal*`] 立即結束（使用 `panic` 不是結束程式的好方法，[請不要使用 panic](#不要使用-panic)。）

  [`os.Exit`]: https://golang.org/pkg/os/#Exit
  [`log.Fatal*`]: https://golang.org/pkg/log/#Fatal

**只在** `main()` 中呼叫 `os.Exit` 或 `log.Fatal*`。其他所有函式應回傳錯誤以表示失敗。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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
  b, err := os.ReadAll(f)
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
  b, err := os.ReadAll(f)
  if err != nil {
    return "", err
  }
  return string(b), nil
}
```

</td></tr>
</tbody></table>

基本上，有多個函式會觸發 exit 的程式，可能會有一些問題：

- 不明瞭的控制流程：任何函式都可以結束程式，因此很難推斷控制流程
- 難以測試：結束程式的函式也會結束呼叫它的測試。這使得函式難以測試，並且可能導致 `go test` 還沒進行其他測試，測試程序就先結束的問題
- 跳過清理：函式結束程式時，會跳過已經排入 `defer` 佇列的函式呼叫。這增加了跳過重要清理任務的風險

#### 只結束一次

可以的話，建議 `main()` 中最多只呼叫一次 `os.Exit` 或 `log.Fatal*`。如果有多個錯誤場景會停止程式執行，請將邏輯放在單獨的函式，並從中回傳錯誤。

這能縮短 `main()` 函式的列數，同時也能將所有關鍵的商業邏輯放入單獨的、可測試的函式中。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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
  // 在本列後呼叫 log.Fatal，
  // f.Close 就不能被呼叫到。
  b, err := os.ReadAll(f)
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
  b, err := os.ReadAll(f)
  if err != nil {
    return err
  }
  // ...
}
```

</td></tr>
</tbody></table>

### 在序列化結構中使用成員標籤

任何會序列化到 JSON、YAML 或其他支援以標籤為基礎的欄位命名格式的結構體欄位，都應使用相關的標籤進行註解。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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
  // 可以無慮地將 Name 重新命名成 Symbol。
}
bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td></tr>
</tbody></table>

基本上：結構的序列化形式是不同系統間的契約。對序列化格式結構（包括欄位名稱）的更動會破壞這個契約。在標籤中指定欄位名稱，使得契約更為明確，也能防止因重構或重新命名欄位而意外破壞契約。

### 要對建立的 goroutine 負責

Goroutines 很輕量，但並非完全不需要開銷。至少排程堆疊和 CPU 資源的過程中會消耗記憶體資源。雖然這些成本對 goroutines 來說很小，但當它們不受生命週期的管控，在大量的情況下會拖累整體效能。此外，不受管理的 goroutines 可能會造成其他問題，像是讓 GC 無法回收不再使用的資源。

因此，不要在生產環境中的程式內洩漏 goroutines。使用 [go.uber.org/goleak](https://pkg.go.dev/go.uber.org/goleak) 來測試可能產生 goroutine 的洩漏問題。

一般來說，每個 goroutine 必須：

- 具備可預測的停止運作時間；或者
- 具有一個方法可以使 goroutine 在接收訊號時停止執行

在這兩種情況下，必須要有一個方法阻止程式繼續執行，等待 goroutine 完成工作。

舉例來說：

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
go func() {
  for {
    flush()
    time.Sleep(delay)
  }
}()
```

</td><td>

```go
var (
  stop = make(chan struct{}) // 告訴 goroutine 停止
  done = make(chan struct{}) // 告訴我們 goroutine 結束了
)
go func() {
  defer close(done)
  ticker := time.NewTicker(delay)
  defer ticker.Stop()
  for {
    select {
    case <-ticker.C:
      flush()
    case <-stop:
      return
    }
  }
}()
// 其它……
close(stop)  // 指示 goroutine 停止
<-done       // 並且等待它停止後離開
```

</td></tr>
<tr><td>

無法停止這個 goroutine。它將運作到應用程式結束。

</td><td>

這個 goroutine 可以使用 `close(stop)` 結束，而我們可以使用 `<-done` 等待它結束。

</td></tr>
</tbody></table>

#### 等待 goroutines 結束

給定一個由系統產生的 goroutine，必須有一種方式能等待 goroutine 結束。這邊有兩種常見的方式：

- 使用 `sync.WaitGroup`。如果您要等待多個 goroutine，請採用此方式：

    ```go
    var wg sync.WaitGroup
    for i := 0; i < N; i++ {
      wg.Add(1)
      go func() {
        defer wg.Done()
        // ...
      }()
    }

    // 等待所有程序完成：
    wg.Wait()
    ```

- 新增另一個 `chan struct{}`，goroutine 結束執行時會關閉它。如果只有一個 goroutine，請採用此方式：

    ```go
    done := make(chan struct{})
    go func() {
      defer close(done)
      // ...
    }()

    // 若要等待 goroutine 完成：
    <-done
    ```

#### 不要在 `init()` 使用 goroutines

`init()` 函式不應該產生 goroutines。可以參考〈[避免使用 `init()`](#避免使用-init)〉。

如果套件需要在背景執行的 goroutine，它必須公開一個負責管理 goroutine 生命週期的物件。該物件必須提供一個方法（`Close`、`Stop`、`Shutdown` 等）來指示背景 goroutine 停止動作並等待其結束。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
func init() {
  go doWork()
}
func doWork() {
  for {
    // ...
  }
}
```

</td><td>

```go
type Worker struct{ /* ... */ }
func NewWorker(…) *Worker {
  w := &Worker{
    stop: make(chan struct{}),
    done: make(chan struct{}),
    // ...
  }
  go w.doWork()
}
func (w *Worker) doWork() {
  defer close(w.done)
  for {
    // ...
    case <-w.stop:
      return
  }
}
// Shutdown 告訴 worker 停止
// 並等待它完成。
func (w *Worker) Shutdown() {
  close(w.stop)
  <-w.done
}
```

</td></tr>
<tr><td>

當使用者匯出這個套件時，將無條件的產生背景 goroutine。使用者無法控制 goroutine 或是停止它。

</td><td>

只在使用者請求時產生 worker，另外提供一種關閉 worker 的方式，方便使用者釋放 worker 使用的資源。

請注意，如果 worker 管理多個 goroutines，則應該使用 `WaitGroup`。請參考 [等待 goroutines 結束](#等待-goroutines-結束)。

</td></tr>
</tbody></table>

## 效能

效能方面的特定準則只適用於最忙碌路徑 (hot path)。

### 優先使用 strconv 而不是 fmt

將基本類型轉換為字串或反過來轉換時，`strconv` 速度比 `fmt` 快。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

```plain
BenchmarkFmtSprint-4    143 ns/op    2 allocs/op
```

</td><td>

```plain
BenchmarkStrconv-4    64.2 ns/op    1 allocs/op
```

</td></tr>
</tbody></table>

### 避免字串到字元的轉換

不要反覆從固定字串建立字元切片。相反，請只做一次轉換並擷取其結果。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

```plain
BenchmarkBad-4   50000000   22.2 ns/op
```

</td><td>

```plain
BenchmarkGood-4  500000000   3.25 ns/op
```

</td></tr>
</tbody></table>

### 指定容器容量

盡可能指定容器容量，以便為容器預先分配記憶體。這能最大減少加入元素造成的後續分配（透過複製和調整容器大小）。

#### 指定 Map 容量提示

盡量在使用 `make()` 初始化的時候提供容量提示。

```go
make(map[T1]T2, hint)
```

向 `make()` 提供容量提示，會使之嘗試在初始化時調整 map 至正確大小。這將減少元素加入到 map 時，擴增 map 和分配記憶體的需要。

注意，與切片不同，map 的容量提示不保證完全的搶斷式分配，而是用於估計所需的 hashmap bucket 的數量。因此，將元素加入到 map 時，即使個數還沒到指定的容量，仍可能會進行分配。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[string]os.FileInfo)

files, _ := os.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := os.ReadDir("./files")

m := make(map[string]os.FileInfo, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

`m` 建立時未給予容量提示，運作時分配的次數可能會比較多。

</td><td>

`m` 建立時有給予容量提示，運作時分配的次數可能會比較少。

</td></tr>
</tbody></table>

#### 指定切片容量

盡量在使用 `make()` 初始化切片 (*slice*) 時提供容量參數，尤其是在附加切片時。

```go
make([]T, length, capacity)
```

和 map 不同，切片容量不單單只是提示：編譯器將為提供給 `make()` 的切片容量，分配足夠的記憶體。這意味著後續的 `append()` 操作將不需要進行分配（直到切片的長度和容量相同，在此之後，任何 append 都可能調整大小以容納其他元素）。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

```plain
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```plain
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>

## 規範

### 列內文字不要過長

不要寫出需要使用者橫向捲動，或需要大幅度轉動頭部的程式列。

我們建議將軟性 (soft) 列長度限制在 99 個字元。作者應該在達到這個限制之前換列，但這不是硬性限制。程式碼可以超過這個限制。

### 一致性

本文中概述的一些準則可以客觀評估，而其他準則需因地、場景或主觀因素進行判斷。

最重要的是 **保持一致**。

一致的程式碼更容易維護、更加容易安排、學習成本更少，而且更容易在新的約定出現時進行移植，或在類別的錯誤修正後進行更新。

相反，在一個函式庫中包含多個完全不同或衝突的程式碼風格，會增加維護成本、製造不確定性並導致認知上的偏誤，而這些全會直接導致速度的降低、程式碼檢查上的痛苦，並造出 bugs。

當在程式碼庫 (*codebase*) 中套用這些準則時，建議在套件（或更大）的層級上進行更動：子套件層級的應用程式在同一份程式碼中引入多種風格，是違反上述原則的。

### 相似的宣告放在一組

Go 語言支援將相似的宣告放在一個組內。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

僅將相關的宣告放在一組。不要將不相關的宣告放在一組。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

沒有規定應該在程式碼何處分組。例如：你可以在函式內部進行分組：

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

例外：如果變數宣告與其他變數相鄰，則應將變數宣告（尤其是函式內部的宣告）分在同個群組。即使變數間沒有關聯，也應當如此宣告。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

### import 分組

匯入 (*import*) 應該分為兩組：

- 標準函式庫
- 其他函式庫

預設情況下，這是 goimports 套用的分組方式。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

當命名套件時，請按下面規則選擇一個名稱：

- 全部小寫。沒有大寫或底線
- 大部分呼叫情境都不需要使用「命名匯入」重新命名套件
- 簡短而簡潔。請謹記：每個呼叫套件的地方，都會完整呈現這個名稱
- 不用複數。例如 `net/url`，而不是 `net/urls`
- 不要用 `common`，`util`，`shared` 或 `lib`。這些是不好的，資訊量不足的名稱

另請參考 [Go 套件命名規則] 和 [Go 套件樣式指南]。

  [Go 套件命名規則]: https://blog.golang.org/package-names
  [Go 套件樣式指南]: https://rakyll.org/style-packages/

### 函式名稱

我們遵循 Go 社群關於使用 [MixedCaps 作為函式名] 的約定。有一個例外，為了對相關的測試用例進行分組，函式名可能包含底線，如：`TestMyFunction_WhatIsBeingTested`.

  [MixedCaps 作為函式名]: https://golang.org/doc/effective_go.html#mixed-caps

### 匯入別名

如果套件名稱與匯入路徑的最後一個元素不匹配，則必須使用匯入別名。

```go
import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)
```

在所有其他情況下，除非匯入之間有直接衝突，否則應避免匯入別名。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

### 函式分組與順序

- 函式應按粗略的呼叫順序排序
- 同一文件中的函式應按接收者分組

因此，匯出的函式應先出現在檔案中，放在 `struct`、`const`、`var` 定義的後面。

在定義類型之後，但在接收者的其餘方法之前，可能會放一個 `newXYZ()` 或 `NewXYZ()`。

由於函式是依照接收者分組的，因此純工具函式應放在檔案末尾。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

### 減少巢套（Nesting）

程式碼應當盡量減少巢套 (*Nesting*) 的數量。可以先處理錯誤情況或特殊情況並盡早回傳或繼續循環來減少巢套。減少在多層級巢套之內程式碼的列數。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

如果在 if 的兩個分支中都設定了變數，則可以將其取代為單個 if。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

在上層，使用標準 `var` 關鍵字。除非它與表示式的類型不同，否則不要指定類型。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// 由於 F() 已經明確回傳一個字串類型，
// 因此不用再次指定 _s 的類型

func F() string { return "A" }
```

</td></tr>
</tbody></table>

如果表示式的類型與所需的類型不完全匹配，請指定類型。

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F 回傳一個 myError 類型的實例，但是我們要 error 類型
```

### 對於未匯出的上層常數和變數，使用 _ 作為前綴

在未匯出的頂層 `vars` 和 `consts` 前面加上前綴 `_`，以使它們在使用時明確表示它們是全域符號。

基本上：頂層變數和常數具有套件範圍作用域。使用通用名稱，很容易在其他檔案中意外使用錯誤的值。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

  // 把 Bar() 的第一列程式碼刪掉後，
  // 會發現沒有編譯錯誤。
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

**例外**：未匯出的錯誤值可以使用不帶底線的前綴 `err`。 參見[錯誤命名](#錯誤命名)。

### 結構體中的嵌入

嵌入式類型（例如 mutex）應位於結構體內的成員 list 的頂部，並且必須有一個空行將嵌入式成員與一般成員分隔開。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

要有切實的好處，比如以語義上合適的方式加入或增強功能，才建議內嵌。它應該在對使用者沒有任何不利影響的情況下使用（另請參見：[避免在公用結構體中嵌入類型]）。

例外：即使在未匯出類型中，Mutex 也不應該作為內嵌成員。另請參見：[零值 Mutex 是有效的]。

  [避免在公用結構體中嵌入類型]: #避免在公用結構體中嵌入類型
  [零值 Mutex 是有效的]: #零值-mutex-是有效的

嵌入 **不應該**：

- 純粹是為了美觀或方便
- 使外部類型更難建構或使用
- 影響外部類型的零值。如果外部類型有一個有用的零值，則在嵌入內部類型之後應依然有一個有用的零值
- 作為嵌入內部類型的副作用，從外部類型公開不相關的函式或成員
- 公開未匯出的類型
- 影響外部類型的複製語意
- 更改外部類型的 API 或類型語義
- 嵌入內部類型的非規範形式
- 公開外部類型的實作詳情
- 允許使用者觀察或控制類型內部
- 透過包裝的方式改變內部函式的一般行為，這種包裝方式會給使用者帶來一些意料之外的情況

簡單地說：要有意識且有目的性的嵌入。有個很好的檢驗準則：「是否想要把所有這些匯出的內部方法或欄位，直接加入到外部類型？」如果答案是「只有一些」或「不想」，則不要嵌入內部類型，而是改用欄位。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // 不妥做法：現在可以使用 A.Lock() and A.Unlock()
    // 但這些方法不提供任何功能性好處，並允許使用者控制
    // 有關 A 的內部細節。
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // 較佳做法：Write() 在外層提供用於特定目的，
    // 並且委託工作到內部類型的 Write() 中。
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
    // 不妥做法：指標更改零值的可用性
    io.ReadWriter
    // 其它欄位
}
// 稍後
var b Book
b.Read(…)  // panic: nil pointer
b.String()   // panic: nil pointer
b.Write(…) // panic: nil pointer
```

</td><td>

```go
type Book struct {
    // 較佳做法：包含可用的零值
    bytes.Buffer
    // 其它欄位
}
// 稍後
var b Book
b.Read(…)  // ok
b.String()   // ok
b.Write(…) // ok
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

如果將變數明確設定為某個值，則應使用短變數宣告形式 (`:=`)。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

但是，在某些情況下，使用 `var` 關鍵字能使預設值更清晰，例如[宣告空切片]。

  [宣告空切片]: https://github.com/golang/go/wiki/CodeReviewComments#declaring-empty-slices

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

- 您不應明確回傳長度為零的切片。應該改回傳 `nil`

  <table>
  <thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

- 要檢查切片是否為空，請始終使用 `len(s) == 0`。而非 `nil`

  <table>
  <thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

- 零值切片（用 `var` 宣告的切片）可立即使用，無需呼叫 `make()` 建立

  <table>
  <thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

記住，雖然 `nil` 切片是有效的切片，但它不等於長度為 0 的切片（一個為 `nil`，另一個不是），並且在不同的情況下（例如序列化），這兩個切片的處理方式可能不同。

### 縮小變數作用域

如果有可能，盡量縮小變數作用範圍。除非它與 [減少巢套](#減少巢套nesting) 的規則衝突。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
err := os.WriteFile(name, data, 0644)
if err != nil {
 return err
}
```

</td><td>

```go
if err := os.WriteFile(name, data, 0644); err != nil {
 return err
}
```

</td></tr>
</tbody></table>

如果需要在 `if` 之外使用函式呼叫的結果，則不應嘗試縮小範圍。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
if data, err := os.ReadFile(name); err == nil {
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
data, err := os.ReadFile(name)
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

### 避免參數語義不明確 (Avoid Naked Parameters)

函式呼叫中的 **意義不明確的參數** 可能會損害可讀性。當參數名稱的含義不明顯時，請為參數加入 C 樣式註解 (`/* ... */`)

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

對於上面的範例程式碼，還有一種更好的處理方式，是將上面的 `bool` 類型換成自訂類型。之後這個參數就不會被兩元狀態 (true/false) 所局限。

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
  // 之後還可以有個 StatusInProgress。
)

func printInfo(name string, region Region, status Status)
```

### 使用原始字串字面值，避免轉義

Go 支援使用 [原始字串字面值 (*raw string literal*)](https://golang.org/ref/spec#raw_string_lit)，不僅可以跨多個程式列，還可以包含引號（`"`）。使用這些字串可以避免寫出很難閱讀的手寫轉義字串。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

#### 使用欄位名稱初始化結構體

初始化結構時，絕大多數情況下應指定欄位名稱。現在 [`go vet`] 會強制檢查這方面的問題。

  [`go vet`]: https://golang.org/cmd/vet/

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

例外：當有 3 個或更少的成員時，測試表中的成員名 **或許** 可以省略。

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```

#### 省略結構體中的零值欄位

初始化具有成員名稱的結構體時，除非這些欄位能引出有意義的上下文 (*context*)，否則不要手寫，讓 Go 自動設定這些零值。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

省略這個上下文中的預設值，可以減少讀者閱讀程式碼的障礙。只指定有意義的數值。

如果欄位名稱能引出有意義的上下文，則請寫出其零值。比如 [表格驅動測試](#表格驅動測試) 中的測試用例，即使欄位的數值是零值，但可以從欄位名稱中獲益。

```go
tests := []struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```

#### 對零值結構體使用 `var`

如果在宣告中省略了結構體的所有成員，請使用 `var` 宣告結構。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

這將零值結構與那些具有類似於為 [map 初始化] 建立的，區別於非零值成員的結構區分開來，並與我們更偏好的 [宣告空切片] 方式相匹配。

  [map 初始化]: #初始化-maps

#### 初始化 Struct 引用

在初始化結構引用時，請使用 `&T{}` 代替 `new(T)`，以使其與結構體初始化一致。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
<tbody>
<tr><td>

```go
sval := T{Name: "foo"}

// 不一致
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

空 map 請使用 `make(…)` 初始化，並且 map 是透過程式填充的。這使得 map 初始化在表現上不同於宣告，並且它還可以方便地在 `make` 後加入大小提示。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

宣告和初始化在視覺上容易混淆。

</td><td>

宣告和初始化能在視覺上進行區分。

</td></tr>
</tbody></table>

盡量在初始化時提供 map 容量大小，詳細請看 [指定 Map 容量提示](#指定-map-容量提示)。

另外，如果 map 包含固定的元素列表 (*list*)，則使用 map 字面值初始化 map。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

基本準則是：在初始化時使用 map 字面值加入一組固定的元素。否則使用 `make`（如果可以，最好指定 map 容量。）

### 字串 string format

如果你在函式外宣告 `Printf` 風格函式的格式字串，請將其設定為 `const` 常數。

這有助於 `go vet` 對格式字串執行靜態分析。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

### 命名 Printf 樣式的函式

宣告 `Printf` 風格的函式時，請確保 `go vet` 可以檢測到它並檢查格式字串。

這意味著您應盡可能使用預定義的 `Printf` 風格函式名稱。`go vet` 將預設檢查這些函式。更多資訊請參見 [Printf 系列]。

  [Printf 系列]: https://golang.org/cmd/vet/#hdr-Printf_family

如果不能使用預定義的名稱，請以 f 結束選擇的名稱：`Wrapf`，而不是 `Wrap`。可以要求 `go vet` 檢查特定的 Printf 樣式名稱，但名稱必須以 `f` 結尾。

```shell
go vet -printfuncs=wrapf,statusf
```

另請參考 [go vet: Printf family check]。

[go vet: Printf family check]: https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/

## 編程模式

### 表格驅動測試

當測試邏輯重複時，搭配 [subtests] 使用表格驅動的方式可以減少重複程式碼。

[subtests]: https://blog.golang.org/subtests

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

「測試表格」(*test table*) 不僅方便加上錯誤訊息的上下文、減少重複的邏輯，還簡化了加入新測試案例的流程。

我們遵循這樣的約定：將這種結構體切片稱為 `tests`，每個測試用例稱為`tt`。此外，我們建議使用 `give` 和 `want` 前綴說明每個測試用例的輸入和輸出值。

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

平行測試，比如一些特化的循環（例如產生 goroutine、或者會捕捉循環體中引用的循環）必須注意在循環的範圍內顯式地指派循環變數，以確保它們保持預期的值。

```go
tests := []struct{
  give string
  // ...
}{
  // ...
}
for _, tt := range tests {
  tt := tt // 指派給 t.Parallel
  t.Run(tt.give, func(t *testing.T) {
    t.Parallel()
    // ...
  })
}
```

在上面的例子中，由於下面使用了 `t.Parallel()`，我們必須宣告一個作用域為循環疊代的 `tt` 變數。如果我們不這樣做，大多數或所有測試會收到一個非預期的 `tt` 值，或者一個在運作時會發生變化的值。

### 函式型選項

「函式型選項」是一種模式，您可以在其中宣告一個不透明 Option 類型，在特定內部結構中記錄資訊。您接受不定個數的這些選項，並根據內部結構體上選項記錄的完整資訊採取行動。

這個模式請用在您需要擴充的建構子和其他公用 API 中的可選參數，尤其是在這些功能上已經具有三個或更多參數的情況下。

<table>
<thead><tr><th>不妥做法</th> <th>較佳做法</th></tr></thead>
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

必須始終提供快取和記錄器參數，即使使用者希望使用預設值。

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

我們建議實作此模式的方法是使用一個 `Option` 介面，該介面儲存一個未匯出 (*unexported*) 的方法，並在一個未匯出的 `options` 結構體上記錄選項。

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

注意：還有一種使用閉包實作這個模式的方法，但是我們相信上面的模式為作者提供了更多的靈活性，並且更容易對使用者進行除錯和測試。特別是，閉包不可能進行比較，而我們的模式可以在測試和模擬中對選項進行比較。此外，它還允許選項實作其他介面，包括 `fmt.Stringer`，允許使用者讀取選項的字串表示形式。

還可以參考下面資料：

- [Self-referential functions and the design of options]
- [Functional options for friendly APIs]

  [Self-referential functions and the design of options]: https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html
  [Functional options for friendly APIs]: https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis

<!-- TODO: replace this with parameter structs and functional options, when to
use one vs other -->

## Linting

比任何「豐富」linter 集更重要的是，lint 規則在一個程式碼庫始終保持一致。

我們建議至少使用以下 linters，因為我們認為它們有助於發現最常見的問題，並在不需要規定的情況下為程式碼品質建立一個高標準：

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

我們推薦將 [golangci-lint] 作為 go-to lint 的運作程式，主要是因為它在較大函式庫中有著優異效能，以及能夠同時設定和使用許多規範。這個 repo 有一個範例組態檔案 [.golangci.yml] 以及建議的 linters 和設定。

golangci-lint 有 [多種 linters] 可供使用。建議將上述的 linters 當作基礎設定，而我們鼓勵團隊往專案中加入其他對專案有意義的 linters。

  [golangci-lint]: https://github.com/golangci/golangci-lint
  [.golangci.yml]: https://github.com/uber-go/guide/blob/master/.golangci.yml
  [多種 linters]: https://golangci-lint.run/usage/linters/

## Stargazers over time

[![Stargazers over time](https://starchart.cc/ianchen0119/uber_go_guide_tw.svg)](https://starchart.cc/ianchen0119/uber_go_guide_tw)
