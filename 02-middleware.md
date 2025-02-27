# 第2章: Rackミドルウェアをいろいろな書き方で実装してみよう

この章では、Rackミドルウェアの基本的な仕組みを理解し、さまざまな方法でミドルウェアを実装してみます。ミドルウェアを活用することで、アプリケーションの機能を拡張したり、共通の処理を分離することができます。

---

## 1. Rackミドルウェアとは
Rackミドルウェアは、Rackアプリケーションのリクエストとレスポンスの間に介在し、リクエストを加工したり、レスポンスに処理を加えたりするコンポーネントです。ミドルウェアをチェーンのようにつなげることで、複雑な処理をシンプルに組み立てることができます。

---

## 2. シンプルなミドルウェアの作成
まずは、簡単なミドルウェアを作成してみましょう。レスポンスヘッダーにカスタムヘッダーを追加するミドルウェアを実装します。

### 手順
1. 新しいファイル `middleware.ru` を作成します。
2. 適当なRackアプリケーションと、Rackミドルウェアを定義します。
   ```ruby
   class App
     def call(env)
       [200, {}, ["hello"]]
     end
   end

   class Middleware
     def initialize(app, name)
       @app = app
       @name = name
     end
   
     def call(env)
       status, headers, body = @app.call(env)
       headers["hello"] = @name
       [status, headers, body]
     end
   end

   use Middleware, "rails"
   run App.new
   ```

### ポイント

- ミドルウェアは、`initialize` と `call` メソッドを持つクラスで定義します。
- ミドルウェアは `initialize` メソッドで、次のアプリケーションや任意の引数を受け取ります。
- `call` メソッドで、リクエストを受け取って次のアプリケーションに渡し、レスポンスを返します。
    - ここで、リクエストやレスポンスに加工を加えることができます。
- `use` キーワードを使ってミドルウェアを登録します。

### 実行方法

ターミナルで以下のコマンドを実行します。

```console
$ rackup middleware.ru
```

ブラウザやcurlで `http://localhost:9292` にアクセスしてヘッダを確認してみてください。

```console
$ curl -i http://localhost:9292/
HTTP/1.1 200 OK
hello: rails
Content-Length: 5

hello
```

`hello` ヘッダーが追加されていることがわかります。

---

## 3. Rackの標準ミドルウェアを活用する
Rackが提供する標準のミドルウェアを使って機能を拡張してみましょう。ここではリクエストの処理時間を測定する `Rack::Runtime` と、Basic認証を行う `Rack::Auth::Basic` を使用します。

### 手順

1. 必要なライブラリを読み込みます。
   ```ruby
   require "rack/runtime"
   require "rack/auth/basic"
   ```
2. ミドルウェアを追加します。
   ```ruby
   use Rack::Runtime
   use Rack::Auth::Basic do |username, password|
     username == "rubyist" && password == "onrack"
   end
   ```

### ポイント
- ミドルウェアは上から順に適用されます。

### 実行方法

これまでと同様にrackupコマンドで起動し、ブラウザやcurlでアクセスしてみましょう。

```console
$ curl -i http://localhost:9292/
HTTP/1.1 401 Unauthorized
content-type: text/plain
www-authenticate: Basic realm=""
x-runtime: 0.000038
hello: rails
Content-Length: 0
```

`Rack::Auth::Basic` により、認証情報なしでは401 Unauthorizedが返されます。
また `Rack::Runtime` により、レスポンスヘッダーに `x-runtime` ヘッダーが追加されていることがわかります。

```console
$ curl -i http://rubyist:onrack@localhost:9292/
HTTP/1.1 200 OK
x-runtime: 0.000055
hello: rails
Content-Length: 6

hello
```

認証情報を付与すると、`hello` が表示されます。

---

## 4. まとめ

この章では、Rackミドルウェアの基本的な実装方法から、Rackの標準ミドルウェアの活用を学びました。

ここではごくシンプルなRackミドルウェアを実装し、Rack標準ミドルウェアを利用してみました。
世の中にはRack標準以外にも様々なライブラリがRackミドルウェアの形で世の中に公開されており、それらを組み合わせることで、多様な機能をアプリケーションに組み込むことができます。

これまでの内容を通じて、以下のポイントを理解できたと思います。

- **ミドルウェアの基本構造**: `initialize` と `call` メソッドを持つクラス。
- **Rackの標準ミドルウェア**: `Rack::Runtime` や `Rack::Auth::Basic` などの活用方法。

---

次の章では、Rackアプリケーションを起動するためのRackサーバを自分で実装してみましょう。
