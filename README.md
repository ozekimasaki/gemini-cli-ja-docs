# Gemini CLI 日本語ドキュメント

このリポジトリは、コマンドラインAIワークフローツールである [Gemini CLI](https://github.com/google-gemini/gemini-cli) の公式ドキュメントを日本語に翻訳したものです。CLI の概要・使い方から、アーキテクチャ・各種ツール・設定に至るまでのガイドを Markdown 形式で収録しています。

> **注:** このリポジトリはドキュメント（翻訳）専用であり、Gemini CLI 本体のソースコードは含まれていません。CLI 本体のインストールや実行方法については、以下の「Gemini CLI の概要」を参照してください。

![Gemini CLIスクリーンショット](./docs/assets/gemini-screenshot.png)

## このリポジトリについて

- **目的:** Gemini CLI のドキュメントを日本語で提供する。
- **形式:** すべて Markdown ファイル（`README.md` および `docs/` 配下）。
- **構成技術:** ビルドツールやパッケージマネージャーには依存しておらず、Markdown ファイルのみで構成されています。

## Gemini CLI の概要

Gemini CLI は、ツールに接続し、コードを理解し、ワークフローを高速化するコマンドラインAIワークフローツールです。

Gemini CLI を使用すると、次のことができます。

- Gemini の100万トークンのコンテキストウィンドウ内外の大規模なコードベースをクエリおよび編集します。
- Gemini のマルチモーダル機能を使用して、PDFやスケッチから新しいアプリを生成します。
- プルリクエストのクエリや複雑なリベースの処理など、運用タスクを自動化します。
- ツールと MCP サーバーを使用して、[Imagen、Veo、または Lyria によるメディア生成](https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia)を含む新しい機能を接続します。
- Gemini に組み込まれている [Google 検索](https://ai.google.dev/gemini-api/docs/grounding)ツールでクエリをグラウンディングします。

## 前提条件

- [Node.js バージョン18](https://nodejs.org/en/download)以降がインストールされていること。

## クイックスタート

1. **CLI の実行:** ターミナルで次のコマンドを実行します。

   ```bash
   npx https://github.com/google/gemini-cli
   ```

   または、次のようにインストールします。

   ```bash
   npm install -g @google/gemini-cli
   ```

2. **カラーテーマを選択**
3. **認証:** プロンプトが表示されたら、個人の Google アカウントでサインインします。これにより、Gemini 2.5 Pro を使用して、1分あたり最大60件のモデルリクエスト、1日あたり1,000件のモデルリクエストが付与されます。

これで、Gemini CLI を使用する準備が整いました。

### 高度な使用または制限の引き上げについて

特定のモデルを使用する必要がある場合、またはより高いリクエスト容量が必要な場合は、API キーを使用できます。

1. [Google AI Studio](https://aistudio.google.com/apikey) からキーを生成します。
2. ターミナルで環境変数として設定します。`YOUR_API_KEY` を生成したキーに置き換えます。

   ```bash
   export GEMINI_API_KEY="YOUR_API_KEY"
   ```

Google Workspace アカウントを含むその他の認証方法については、[認証](./docs/cli/authentication.md)ガイドを参照してください。

## 使い方の例

CLI が実行されたら、シェルから Gemini との対話を開始できます。

新しいディレクトリからプロジェクトを開始できます。

```sh
$ cd new-project/
$ gemini
> FAQ.mdファイルを使用して質問に答えるGemini Discordボットを作成してください。
```

または、既存のプロジェクトで作業します。

```sh
$ git clone https://github.com/google-gemini/gemini-cli
$ cd gemini-cli
$ gemini
> 昨日行われたすべての変更の概要を教えてください。
```

## ドキュメントの構成

このリポジトリのドキュメントは `docs/` 配下に整理されています。総合的な入口として [`docs/index.md`](./docs/index.md) を参照してください。

```text
.
├── README.md              # 本ファイル（リポジトリの概要）
└── docs/
    ├── index.md           # ドキュメントの目次・入口
    ├── architecture.md    # アーキテクチャの概要
    ├── deployment.md      # 実行とデプロイ
    ├── checkpointing.md   # チェックポイント機能
    ├── extension.md       # 拡張機能
    ├── telemetry.md       # テレメトリ
    ├── sandbox.md         # サンドボックス
    ├── troubleshooting.md # トラブルシューティング
    ├── integration-tests.md # 統合テスト
    ├── cli/               # CLI の使用法（概要・コマンド・構成・テーマ・認証など）
    ├── core/              # コア（packages/core）とツール API
    ├── tools/             # 各種ツール（ファイルシステム・シェル・Webフェッチなど）
    └── assets/            # スクリーンショット・テーマ画像
```

主なドキュメントへのリンク:

- **[ドキュメント目次](./docs/index.md):** すべてのドキュメントの入口。
- **[CLI コマンド](./docs/cli/commands.md):** 利用可能な CLI コマンドの説明。
- **[構成](./docs/cli/configuration.md):** CLI の設定に関する情報。
- **[認証](./docs/cli/authentication.md):** 認証方法のガイド。
- **[アーキテクチャの概要](./docs/architecture.md):** 高レベルの設計。
- **[トラブルシューティング](./docs/troubleshooting.md):** 一般的な問題と FAQ。

## 開発

このリポジトリは Markdown ファイルのみで構成されており、ビルド・テスト・lint などのツールチェーンは設定されていません（`package.json` などのビルド設定は存在しません）。ドキュメントの編集は、対象の `.md` ファイルを直接編集してください。

編集時の推奨事項:

- リポジトリ内の他のファイルを参照する際は、相対リンクを使用してください。
- 画像などのアセットは `docs/assets/` に配置してください。
- 表記や訳語は既存のドキュメントと一貫性を保ってください。

## ライセンス

このリポジトリにはライセンスファイルが含まれていません。翻訳元である Gemini CLI 本体のライセンスについては、[google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) を参照してください。

## Gemini API

Gemini CLI は、Gemini API を活用して AI 機能を提供します。Gemini API を管理する利用規約の詳細については、使用しているアクセスメカニズムの規約を参照してください。

- [Gemini API キー](https://ai.google.dev/gemini-api/terms)
- [Gemini Code Assist](https://developers.google.com/gemini-code-assist/resources/privacy-notices)
- [Vertex AI](https://cloud.google.com/terms/service-terms)
