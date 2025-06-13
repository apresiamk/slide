---
marp: true
theme: custom_theme
paginate: true
backgroundColor: white
footer: (c) 2025 APRESIA Systems, Ltd.
---

<!-- _class: lead -->

# Go

![bg](images/top_back.png)

2025/6/13


---

<!--
backgroundImage: url('images/sec_back.png')
-->

## アジェンダ

<div class="agenda-columns">
  <ul>
    <li>1. What is Go?</li>
    <li>2. Get Started</li>
    <li>3. How to use</li>
    <li>4. <strong>GoとCの違い</strong></li>
    <li>5. <strong>Goのイディオム</strong>
  </ul>
  <ul>
    <li>6. Goの言語仕様</li>
    <li>7. Tools</li>
    <li>8. <strong>C to Go</strong></li>
    <li>9. Live Coding</li>
  </ul>
</div>

---

## 1. What is Go?

<div class="layout">
  <div class="text">
    <ul>
      <li><strong>Google</strong>が開発（2009年）</li>
      <li>C言語のような<strong>高速さ</strong>＋Pythonの<strong>シンプルさ</strong></li>
      <li>コンパイル型：<strong>1つの実行ファイルにビルド</strong></li>
      <li><strong>goroutine</strong>による並行処理が得意</li>
      <li>例：<strong>Docker / Kubernetes</strong> もGo製</li>
    </ul>
  </div>
  <div class="image">
    <img src="images/gopher.png" alt="Go Gopher">
  </div>
</div>

---

## 2. Get Started

<https://Go.dev/>

- Windows11 : [go1.24.4.windows-amd64.msi]をダウンロード
- Ubuntu :

  ```bash
  wget https://Go.dev/dl/go1.24.4.linux-amd64.tar.gz
  sudo tar -C /usr/local -xzf go1.24.4.linux-arm64.tar.gz
  echo 'export PATH=$PATH:/usr/local/Go/bin' >> ~/.profile
  source ~/.profile
  ```

---

## 3. How to use

- `Go version` : インストールされているか確認
- `Go env` : 環境を確認
- `Go build` : 実行ファイルを作成
- `Go run` : プログラムを実行
- `Go install` : Go製プログラムを取得
- `Go help` : その他コマンドを表示

---

<!-- _class: lead -->

## 4. GoとCの違い

---

### 型

<div class="table-columns">
  <div class="table-box">
    <h3>基本型</h3>
    <table>
      <thead>
        <tr><th>Go</th><th>C</th></tr>
      </thead>
      <tbody>
        <tr><td>int8</td><td>char</td></tr>
        <tr><td>uint8</td><td>unsigned char</td></tr>
        <tr><td>int16</td><td>short</td></tr>
        <tr><td>int32</td><td>int</td></tr>
        <tr><td>int64</td><td>long</td></tr>
      </tbody>
    </table>
  </div>

  <div class="table-box">
    <h3>特殊型</h3>
    <table>
      <thead>
        <tr><th>Go</th><th>C</th></tr>
      </thead>
      <tbody>
        <tr><td>bool</td><td>(true/false)</td></tr>
        <tr><td>byte</td><td>unsigned char</td></tr>
        <tr><td>rune, string</td><td>(文字列)</td></tr>
        <tr><td>uintptr</td><td>*</td></tr>
      </tbody>
    </table>
  </div>
</div>

---

### その他

<!-- class: custom-list -->

- 簡潔さよりも**理解しやすさ**、**安全**に重きが置かれている
- 型の違う変数へ代入するには、明示的に型変換をしてから代入する
- 変数は変数名が先、型の指定は後に宣言する`var a int`
- 定義だけして使わない変数があるとコンパイルできない。定数は使わなくてもコンパイル可能
- importして使わないライブラリがあるとコンパイルできない
- ループはfor文だけ。whileやdo-whileもforで書ける
- if文やfor文の条件などは(...) で囲まないで書く

---

### その他 つづき

- 三項演算子`? a : b`はない。if文を使って書く
- **配列**と**スライス**があるが**スライス**のほうがよく使われ便利
- 例外処理はない。関数が返す複数の値の最後にerror型を指定して処理する
- ポインタ変数を使うがアドレスに対する演算はできない
- 構造体Sのフィールドfを参照するにはx.fと書く。x->fとは書かない
  `p := &s[i]`とアドレスを取って`*p = 111`と代入することはできる
- 関数の引数は値渡し。スライスとマップはポインタが渡される
- `defer`を使って関数を抜ける前の処理を必ず実行できる

---

<!-- _class: lead -->

## 5. Goのイディオム

---

<!-- _class: footnoted-slide -->

### 基本

- `x := byte(20)`と型変換を使わず`var x byte = 20`と書く
- スネークケースは使わず、キャメルケースを使う
- 関数が複数の値を返しても使わない値は`_`<sup>*1</sup>に代入する
- `fmt.Println`関連は2つの値を返すが戻り値は無視する
- データソースから読込むたびにメモリを割当てない
  バイトのスライスを一時的に作成しバッファとする

<div class="footer-note">
  <hr>
  <div class="footnote">
    *1: ブランク識別子。使わない変数を宣言するとエラーになるため。
  </div>
</div>

---

<!-- _class: var-naming -->

### 変数名

関数のスコープが狭いほど短くシンプルな名前を使う

| 型 / 意味          | 短縮名 |
|------------------|-------|
| 整数（integer）    | i     |
| 浮動小数点（float） | f     |
| 真偽値（boolean）  | b     |
| 文字列（string）   | s     |
| 結果（result）     | r     |
| ジェネリクス型      | T     |

---

### 構造体内変数名

| 用途                     | 名前例       | 意味       |
|-------------------------|------------|-----------|
| 配列・スライス・文字列の添え字 | i, j       | index      |
| マップのキー               | k          | key       |
| マップや構造体の値          | v, val     | value      |

ネストが深くなる or 複雑な処理があるときは、説明的な名前を使う

```go
for lineIdx, lineText := range lines {
  ...
}
```

---

### カンマokイディオム

マップやチャネルなどで値が存在するかを確認する便利な書き方

```go
val, ok := m["key"]
if ok {
  fmt.Println("キーが存在:", val)
} else {
  fmt.Println("キーが存在しない")
}
```

---

<!-- _class: footnoted-slide -->

### その他

- 型にポインタレシーバのメソッドがひとつでもあれば、
すべてのメソッドにポインタレシーバを使う
- 構造体へはフィールドに直接アクセスする
- switchのcase節では対象となる変数を同名の変数に代入する`i := i.(int)`<sup>*1</sup>

<div class="footer-note">
  <hr>
  <div class="footnote">
    *1: シャドーイング
  </div>
</div>

---

<!-- _class: lead -->

## 5. Goの言語仕様

---

### 宣言

変数

```go
var x int = 10 // 型と初期値を指定する
var x = 10     // 型省略（この場合int型になる）
```

配列とスライス

```go
// 配列（サイズ固定）
var x [3]int // サイズを指定する

// スライス（サイズ可変）
var x = []int{} // nilスライス
```

---

マップと構造体

```go
// マップ
ages := map[string]int{
  "Alice": 30,
  "Bob":   25,
}

// 構造体
type User struct {
  Name string
  Age  int
}
```

---

### 合成型

| 種類     | 説明                    | 例               |
|---------|------------------------|------------------|
| 配列     | 固定長の同じ型の集合        | `[3]int`         |
| スライス  | 可変長の配列のようなもの     | `[]string`       |
| マップ   | キーと値の対応             | `map[string]int` |
| 構造体   | 名前付きフィールドの集まり    | `struct`         |
| ポインタ | 値のアドレスを持つ           | `*int`          |
| チャネル | 並行処理で使うデータ通路      | `chan int`       |

---

### if文

| 種類 | 例 | 説明 |
|------|----|------|
| 基本形 | `if x > 0 {...}` | 基本 |
| if-else | `if x > 0 {...} else {...}` | 条件が成立しない場合 |
| if-else if | `if x > 0 {...} else if x == 0 {...}` | 複数条件を判定 |
| 前置文 | `if err := f(); err != nil {...}` | 条件の前に変数を定義 |

---

### if文 使い方

```go
// 前置文：エラー処理でよく使う
if err := doSomething(); err != nil {
    fmt.Println("エラー:", err)
}
```

```go
// if/elseのネストは避けてcontinueを使う
for _, v := range list {
  if v == 0 {
    continue
  }
  doSomething(v)
}
```

---

### for文

```go
// 基本
for i := 0; i < 10; i++ {
  fmt.Println(i)
}
// for-range
for _, v := range slice {
  fmt.Println(v)
}
// while風
for x > 0 {  // x > 0 は省略可能。その場合無限ループになる。
  fmt.Println(x)
  x--  // --x とは書かない
}
```

---

### switch文

```go
switch color {  // 基本
case "red":
  fmt.Println("赤")
case "blue":
  fmt.Println("青")
}

switch {  // ブランクswitch(if/elseよりこちらを推奨)
case x < 0:
  fmt.Println("負の値")
case x == 0:
  fmt.Println("ゼロ")
default:
  fmt.Println("正の値")
}
```

---

### エラー処理

- 関数の最後にerror型を戻してエラー処理する
- 関数が正常に実行されたら`error`には`nil`が返る
- 異常の場合エラーを表す値が返る
- 呼び出し側は`error`の戻り値を`nil`と比較して処理する

---

<!-- _class: footnoted-slide -->

### エラー処理 続き

- センチネルエラー<sup>*1</sup>の名前は`Err`で始める
- エラーフォーマット文字列の最後に`: %w`を追加して`fmt.Errorf`に渡されるよう最後の引数をラップする
- `panic`を使う場合`recover`でログを出力し`os.Exit(1)`で終了する

<div class="footer-note">
  <hr>
  <div class="footnote">
    *1: 継続不能なエラー
  </div>
</div>

---

## コメント

- パッケージ宣言の前に置かれたコメントはパッケージレベルのコメントとなる。
  パッケージ用に長いコメントを書いた場合、パッケージ内にdoc.goというファイルを置いてここにコメントを置く
- コンテキストは最初の引数として関数に渡す。引数名はctx
- 値を伴うコンテキストを生成する関数名はcontextWithで始め、コンテキストから値を返す関数はFromContextで終わる名前にする

---

## テスト

- キャンセレーション関数を表す変数名はcancel
- テスト関数は、名前を`Test`で始め`*testing.T型`の引数をひとつだけ取る。引数の名前は`t`とする
- ユニットテストは、名前を`Test`で始めその後に関数名を続ける
- ベンチマークは、テストファイル内に`Benchmark`で始まる関数を書く
- パッケージ内の関数をテストするのにサンプルデータを使う場合`testdata`というサブディレクトリを作成してファイルを置く
  Goではこの名前がテストファイル置場として予約されている

---

定数

- 大文字小文字

---

## 4 Goの特徴 制御

- ブロック
- switch

---

## Goの特徴 関数

- 基本
- クロージャ

---

## Goの特徴 ポインタ

- 基本使わない
- 最後の手段

---

## Goの特徴 型

- 抽象型
- 具象型

---

## Goの特徴 メソッド

- レシーバ
- 関数との使い分け
- nil
- iota

---

## Goの特徴 インターフェース

- 埋め込み型
- インターフェース
- ダックタイピング

---

## Goの特徴 エラー処理

- 基本
- ラップ
- IsとAs
- defer
- パニックとリカバー

---

## Goの特徴 構造

- モジュール
- パッケージ

---

## Goの特徴 並行処理

- ゴルーチン
- チャネル
- select

---

## Goの特徴 標準ライブラリ

- io
- time
- encoding

---

## Goの特徴 コンテキスト

- context
- キャンセレーション
- タイマー

---

## Goの特徴 テスト

- 基本
- テーブルテスト
- カバレッジ
- ベンチマーク
- スタブ

---

## Goの特徴 その他

- リフレクション
- unsafe
- ジェネリクス

---

## Tools

- go-callvis
  コード内の関数呼び出し関係を可視化するツール。
  `go install github.com/ofabry/go-callvis@latest`

- mockgen
  インターフェースのモック生成ツール。テスト駆動開発に役立ちます。
  `go install github.com/golang/mock/mockgen@latest`

- cobra-cli
  CLIアプリケーション開発を支援するスキャフォールディングツール。
  `go install github.com/spf13/cobra-cli@latest`

---

## C to Go

### amiosをどのようにGoへ変換するか？

---

## Live Coding

---

## おまけ
