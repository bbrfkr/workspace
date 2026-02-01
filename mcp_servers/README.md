# MCP Servers Docker Build

このディレクトリは、プロジェクトで使用する **MCP サーバー** の Docker イメージをビルドするための設定を格納します。

## ディレクトリ構成

```
mcp_servers/
├─ .dockerignore        # ビルドコンテキストに不要なファイルを除外
├─ Makefile             # `make` で全イメージを一括ビルド
├─ README.md            # 本ファイル
├─ example-python/      # Python ベースのサーバー例
│   └─ Dockerfile
└─ example-node/        # Node.js ベースのサーバー例
    └─ Dockerfile
```

## ビルド手順

1. **Docker がインストールされていることを確認**
2. ルートで `make` を実行すると、サブディレクトリ内のすべての `Dockerfile` が検出され、`<ディレクトリ名>-mcp` という名前のイメージが作成されます。

```bash
cd mcp_servers
make            # すべてのイメージをビルド
```

個別にビルドしたい場合は、`make <対象ディレクトリ名>` を実行してください。

```bash
make example-python
```

## クリーンアップ

ビルドしたイメージをすべて削除したいときは、`make clean` を使用します。

```bash
make clean
```

個別に削除したいときは、`make clean-<対象ディレクトリ名>` を実行してください。

```bash
make clean-example-python
```

## 追加サーバーの作成方法

1. 新しいサブディレクトリを作成（例: `myservice/`）
2. その中に適切なベースイメージを選んだ `Dockerfile` を配置
3. 必要なら `scripts/` ディレクトリを作り、`Dockerfile` から参照できるようにする
4. `make` を再実行すると自動的にビルド対象に含まれます

---

この設定は **ベース Dockerfile を共通化せず**、各サーバーが独自のランタイムや依存関係を持てるように設計されています。
