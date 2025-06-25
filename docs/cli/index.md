# Gemini CLI

Gemini CLI内では、`packages/cli`はユーザーがGemini AIモデルとその関連ツールでプロンプトを送受信するためのフロントエンドです。Gemini CLIの一般的な概要については、[メインドキュメントページ](../index.md)を参照してください。

## このセクションのナビゲート

- **[認証](./authentication.md):** GoogleのAIサービスでの認証設定ガイド。
- **[コマンド](./commands.md):** Gemini CLIコマンド（例: `/help`、`/tools`、`/theme`）のリファレンス。
- **[構成](./configuration.md):** 構成ファイルを使用したGemini CLIの動作の調整ガイド。
- **[トークンキャッシュ](./token-caching.md):** トークンキャッシュによるAPIコストの最適化。
- **[テーマ](./themes.md)**: さまざまなテーマでCLIの外観をカスタマイズするためのガイド。
- **[チュートリアル](tutorials.md)**: Gemini CLIを使用して開発タスクを自動化する方法を示すチュートリアル。

## 非対話モード

Gemini CLIは、スクリプト作成や自動化に役立つ非対話モードで実行できます。このモードでは、CLIに入力をパイプ処理し、コマンドを実行して終了します。

次の例では、ターミナルからGemini CLIにコマンドをパイプ処理します。

```bash
echo "ファインチューニングとは何ですか？" | gemini
```

Gemini CLIはコマンドを実行し、出力をターミナルに出力します。`--prompt`または`-p`フラグを使用しても同じ動作を実現できます。例:

```bash
gemini -p "ファインチューニングとは何ですか？"
```