# AGENTS.md

このファイルは、本リポジトリで作業するコーディングエージェント向けのガイドです。

## リポジトリの概要

- **内容:** [Gemini CLI](https://github.com/google-gemini/gemini-cli) 公式ドキュメントの日本語翻訳。
- **形式:** すべて Markdown ファイル。ソースコードやビルド成果物は含まれません。
- **主要言語:** 日本語（ドキュメント本文）。

## プロジェクト構成

```text
.
├── README.md              # リポジトリの概要（入口）
└── docs/
    ├── index.md           # ドキュメントの目次
    ├── architecture.md    # アーキテクチャの概要
    ├── deployment.md      # 実行とデプロイ
    ├── checkpointing.md   # チェックポイント機能
    ├── extension.md       # 拡張機能
    ├── telemetry.md       # テレメトリ
    ├── sandbox.md         # サンドボックス
    ├── troubleshooting.md # トラブルシューティング
    ├── integration-tests.md # 統合テスト
    ├── cli/               # CLI の使用法（index, commands, configuration, themes, authentication, token-caching, tutorials）
    ├── core/              # コア（index, tools-api）
    ├── tools/             # 各種ツール（index, file-system, multi-file, shell, web-fetch, mcp-server, memory）
    └── assets/            # スクリーンショット・テーマ画像（.png）
```

- **エントリポイント:** ドキュメントの入口は `docs/index.md`、リポジトリ全体の入口は `README.md` です。

## セットアップ

特別なセットアップは不要です。リポジトリをクローンし、Markdown ファイルを直接編集します。

```bash
git clone https://github.com/ozekimasaki/gemini-cli-ja-docs.git
cd gemini-cli-ja-docs
```

## ビルド / テスト / lint / typecheck

このリポジトリには `package.json` などのビルド設定やツールチェーンが**存在しません**。そのため、次のコマンドは定義されていません。

- ビルドコマンド: なし
- テストコマンド: なし
- lint コマンド: なし
- typecheck コマンド: なし

存在しないコマンドを実行・記載しないでください。変更の確認は、Markdown のレンダリング結果と相対リンクの妥当性を目視で確認する形になります。

## コーディング規約（ドキュメント規約）

- **言語:** 本文は日本語で記述します。既存ドキュメントの表記・訳語・トーンと一貫性を保ってください。
- **フォーマット:** 標準的な Markdown を使用します。見出しレベル（`#`, `##`, ...）の階層を適切に保ちます。
- **リンク:** リポジトリ内の他ファイルへの参照は相対リンクを使用します（例: `./docs/cli/commands.md`）。
- **アセット:** 画像などのアセットは `docs/assets/` に配置し、相対パスで参照します。
- **コードブロック:** シェルコマンドやコード例には言語識別子付きのフェンス（```bash など）を使用します。

## 注意点

- 本リポジトリはドキュメント専用です。CLI 本体の挙動を変更するようなコードは含まれません。
- 一部の既存ドキュメントには、このリポジトリに存在しないファイルへのリンクが含まれる場合があります（例: `docs/index.md` からの `../CONTRIBUTING.md` や `./tools/web-search.md`）。翻訳元の構成に由来するものであり、リンク先ファイルは本リポジトリには存在しません。リンクを扱う際は実在するファイルかを確認してください。
- 変更は最小限かつ対象範囲に限定してください。無関係なファイルの一括整形は避けてください。
- コミット前に、追加・変更した相対リンクが実在するファイルを指しているかを確認してください。
