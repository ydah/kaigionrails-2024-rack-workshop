# Rackを理解するための技術ハンズオンワークショップ

このワークショップでは、Railsを使ったWebアプリケーション開発において重要な役割を果たすRackについて学びます。シンプルなアプリケーションから始めて、ミドルウェアやサーバの実装まで、段階的に理解を深めていきましょう。

---

## コンテンツ
- [README.md](README.md): このファイルです。Rackの概要とこのワークショップの概要について説明します。
- [01-app.md](01-app.md): Rackアプリケーションを作成し、基本的な仕組みを理解します。
- [02-middleware.md](02-middleware.md): Rackミドルウェアを作成しRackアプリケーションのカスタマイズについて学んでいきます。
- [03-server.md](03-server.md): Rackサーバを自作します。

Day 1で02-middleware.mdのRackミドルウェア作成まで完了し、Day 2にRackサーバの自作に取り組む予定です。

## 準備: Rackとは何か

### Rackの概要

Rackは、RubyでWebサーバーとWebアプリケーションをつなぐインターフェースです。
Rackを利用すると、HTTPリクエストを処理するWebアプリケーションは以下のように非常にシンプルなRubyコードで記述できます。

```ruby
class HelloRack
  def call(env) # CGI-style environment
    [
      200,                             # status
      {"content-type" => "text/html"}, # headers
      ["Hello, Rack!"],                # body
    ]
  end
end
```

このRackインターフェースを共通で利用することで、異なるRackサーバー（例: Puma, Pitchfork, Unicorn）と異なるWebアプリケーションフレームワーク（例: Ruby on Rails, Sinatra）を組み合わせることができます。

### Rackの役割

- **統一的なインターフェースの提供**: Rackを使用することで異なるWebサーバーとWebフレームワーク間で共通のやり取りが可能になります。これにより、アプリケーションは特定のサーバーや環境に依存せずに動作します。
- **ミドルウェアの活用**: Rackは、リクエストとレスポンスの間にミドルウェアを挟むことができます。ミドルウェアは、認証、ロギング、セッション管理などの共通機能を実装するのに役立ちます。
- **ライブラリの提供**: RackはWebアプリケーションを開発するための便利なライブラリが多数提供しています。これにより、開発者は簡単に機能を追加したり、カスタマイズしたりすることができます。

### RackとRuby on Railsの関係

Railsは、フルスタックなWebアプリケーションフレームワークであり、内部的にRackを利用しています。具体的には、以下のような関係性があります。

- **Rack互換性**: RailsアプリケーションはRackインターフェースに準拠しており、Rack準拠のWebサーバやRackミドルウェアを利用できます。
- **ミドルウェアの活用**: Railsの多くの機能は多数のRackミドルウェアの形で提供されており、セッション管理、リクエストのパラメータパース、クッキーの処理などを行っています。

### なぜRackを学ぶのか

- **基礎理解の向上**: Rackの仕組みを理解することで、RubyのWebアプリケーションがどのように動作しているかを深く理解できます。
- **パフォーマンス最適化**: RailsアプリケーションパフォーマンスはRackミドルウェアやRackサーバ構成により大きく変わってくるため、アプリケーションのパフォーマンス向上させるにはRack理解が重要です。
- **問題解決力の向上**: Rackレベルでのデバッグやカスタマイズが可能になるため、複雑な問題にも対処しやすくなります。

### 注意事項
- このワークショップでは説明の簡単化のため記述を一ファイルにまとめて提示していますが、実際のアプリケーション開発では適切なファイル分割や設計をしましょう。
- SinatraやRailsといったウェブアプリケーションフレームワークそのものについての説明は省略しています。
- ワークショップのコンテンツは最新リリース版Ruby 3.3.5 で進めることを推奨しています。
- ワークショップ内ではRackの正確な仕様を実装しきるところまではおこないません。より詳細な仕様については公式の仕様 <https://github.com/rack/rack/blob/main/SPEC.rdoc> を参照してください。

### 参考資料
- rack/rack <<https://github.com/rack/rack>>
- Introducing Rack <<https://leahneukirchen.org/talks/euruko-2007/chneukirchen-euruko2007-introducing-rack.pdf>>
