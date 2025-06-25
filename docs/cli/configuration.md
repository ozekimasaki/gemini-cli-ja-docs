# Gemini CLI構成

Gemini CLIは、環境変数、コマンドライン引数、設定ファイルなど、その動作を構成するためのいくつかの方法を提供します。このドキュメントでは、さまざまな構成方法と利用可能な設定について概説します。

## 構成レイヤー

構成は、次の優先順位で適用されます（数値が小さいほど、数値が大きいものによって上書きされます）。

1.  **デフォルト値:** アプリケーション内でハードコードされたデフォルト。
2.  **ユーザー設定ファイル:** 現在のユーザーのグローバル設定。
3.  **プロジェクト設定ファイル:** プロジェクト固有の設定。
4.  **環境変数:** システム全体またはセッション固有の変数。`.env`ファイルから読み込まれる可能性があります。
5.  **コマンドライン引数:** CLIの起動時に渡される値。

## ユーザー設定ファイルとプロジェクト設定ファイル

Gemini CLIは、永続的な構成に`settings.json`ファイルを使用します。これらのファイルには2つの場所があります。

- **ユーザー設定ファイル:**
  - **場所:** `~/.gemini/settings.json`（`~`はホームディレクトリ）。
  - **スコープ:** 現在のユーザーのすべてのGemini CLIセッションに適用されます。
- **プロジェクト設定ファイル:**
  - **場所:** プロジェクトのルートディレクトリ内の`.gemini/settings.json`。
  - **スコープ:** その特定のプロジェクトからGemini CLIを実行する場合にのみ適用されます。プロジェクト設定はユーザー設定を上書きします。

**設定内の環境変数に関する注意:** `settings.json`ファイル内の文字列値は、`$VAR_NAME`または`${VAR_NAME}`構文を使用して環境変数を参照できます。これらの変数は、設定が読み込まれるときに自動的に解決されます。たとえば、`MY_API_TOKEN`という環境変数がある場合、`settings.json`で次のように使用できます: `"apiKey": "$MY_API_TOKEN"`。

### プロジェクト内の`.gemini`ディレクトリ

プロジェクト設定ファイルに加えて、プロジェクトの`.gemini`ディレクトリには、Gemini CLIの操作に関連する他のプロジェクト固有のファイルを含めることができます。

- [カスタムサンドボックスプロファイル](#sandboxing)（例: `.gemini/sandbox-macos-custom.sb`、`.gemini/sandbox.Dockerfile`）。

### `settings.json`で利用可能な設定:

- **`contextFileName`** (stringまたはarray of strings):

  - **説明:** コンテキストファイルのファイル名を指定します（例: `GEMINI.md`、`AGENTS.md`）。単一のファイル名または受け入れ可能なファイル名のリストにすることができます。
  - **デフォルト:** `GEMINI.md`
  - **例:** `"contextFileName": "AGENTS.md"`

- **`bugCommand`** (object):

  - **説明:** `/bug`コマンドのデフォルトURLを上書きします。
  - **デフォルト:** `"urlTemplate": "https://github.com/google-gemini/gemini-cli/issues/new?template=bug_report.md&title={title}&body={body}"`
  - **プロパティ:**
    - **`urlTemplate`** (string): `{title}`および`{body}`プレースホルダーを含むことができるURL。
  - **例:**
    ```json
    "bugCommand": {
      "urlTemplate": "https://bug.example.com/new?title={title}&body={body}"
    }
    ```

- **`fileFiltering`** (object):

  - **説明:** @コマンドおよびファイル検出ツールのgit対応ファイルフィルタリング動作を制御します。
  - **デフォルト:** `"respectGitIgnore": true, "enableRecursiveFileSearch": true`
  - **プロパティ:**
    - **`respectGitIgnore`** (boolean): ファイルを検出する際に.gitignoreパターンを尊重するかどうか。`true`に設定すると、git-ignoreされたファイル（`node_modules/`、`dist/`、`.env`など）は@コマンドおよびファイル一覧表示操作から自動的に除外されます。
    - **`enableRecursiveFileSearch`** (boolean): プロンプトで@プレフィックスを補完する際に、現在のツリーの下でファイル名を再帰的に検索できるようにするかどうか。
  - **例:**
    ```json
    "fileFiltering": {
      "respectGitIgnore": true,
      "enableRecursiveFileSearch": false
    }
    ```

- **`coreTools`** (array of strings):

  - **説明:** モデルで利用可能にすべきコアツール名のリストを指定できます。これは、組み込みツールのセットを制限するために使用できます。コアツールのリストについては、[組み込みツール](../core/tools-api.md#built-in-tools)を参照してください。
  - **デフォルト:** Geminiモデルで使用できるすべてのツール。
  - **例:** `"coreTools": ["ReadFileTool", "GlobTool", "SearchText"]`。

- **`excludeTools`** (array of strings):

  - **説明:** モデルから除外すべきコアツール名のリストを指定できます。`excludeTools`と`coreTools`の両方にリストされているツールは除外されます。
  - **デフォルト**: ツールは除外されません。
  - **例:** `"excludeTools": ["run_shell_command", "findFiles"]`。

- **`autoAccept`** (boolean):

  - **説明:** CLIが安全と見なされるツール呼び出し（読み取り専用操作など）を明示的なユーザーの確認なしで自動的に受け入れて実行するかどうかを制御します。`true`に設定すると、CLIは安全と見なされるツールの確認プロンプトをバイパスします。
  - **デフォルト:** `false`
  - **例:** `"autoAccept": true`

- **`theme`** (string):

  - **説明:** Gemini CLIの視覚的な[テーマ](./themes.md)を設定します。
  - **デフォルト:** `"Default"`
  - **例:** `"theme": "GitHub"`

- **`sandbox`** (booleanまたはstring):

  - **説明:** ツール実行にサンドボックスを使用するかどうか、およびその方法を制御します。`true`に設定すると、Gemini CLIは事前にビルドされた`gemini-cli-sandbox` Dockerイメージを使用します。詳細については、[サンドボックス](#sandboxing)を参照してください。
  - **デフォルト:** `false`
  - **例:** `"sandbox": "docker"`

- **`toolDiscoveryCommand`** (string):

  - **説明:** プロジェクトからツールを検出するためのカスタムシェルコマンドを定義します。シェルコマンドは、`stdout`で[関数宣言](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations)のJSON配列を返す必要があります。ツールラッパーはオプションです。
  - **デフォルト:** 空
  - **例:** `"toolDiscoveryCommand": "bin/get_tools"`

- **`toolCallCommand`** (string):

  - **説明:** `toolDiscoveryCommand`を使用して検出された特定のツールを呼び出すためのカスタムシェルコマンドを定義します。シェルコマンドは、次の基準を満たす必要があります。
    - 最初のコマンドライン引数として関数`name`（[関数宣言](https://ai.google.dev/gemini-api/docs/function-calling#function-declarations)とまったく同じ）を取る必要があります。
    - [`functionCall.args`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functioncall)と同様に、`stdin`で関数引数をJSONとして読み取る必要があります。
    - [`functionResponse.response.content`](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference#functionresponse)と同様に、`stdout`で関数出力をJSONとして返す必要があります。
  - **デフォルト:** 空
  - **例:** `"toolCallCommand": "bin/call_tool"`

- **`mcpServers`** (object):

  - **説明:** カスタムツールを検出して使用するために、1つ以上のモデルコンテキストプロトコル（MCP）サーバーへの接続を構成します。Gemini CLIは、構成された各MCPサーバーに接続して、利用可能なツールを検出しようとします。複数のMCPサーバーが同じ名前のツールを公開している場合、競合を避けるために、ツール名は構成で定義したサーバーエイリアスでプレフィックスが付けられます（例: `serverAlias__actualToolName`）。システムは、互換性のためにMCPツール定義から特定のスキーマプロパティを削除する場合があることに注意してください。
  - **デフォルト:** 空
  - **プロパティ:**
    - **`<SERVER_NAME>`** (object): 名前付きサーバーのサーバーパラメータ。
      - `command` (string, 必須): MCPサーバーを開始するために実行するコマンド。
      - `args` (array of strings, 任意): コマンドに渡す引数。
      - `env` (object, 任意): サーバープロセスに設定する環境変数。
      - `cwd` (string, 任意): サーバーを開始する作業ディレクトリ。
      - `timeout` (number, 任意): このMCPサーバーへのリクエストのタイムアウト（ミリ秒）。
      - `trust` (boolean, 任意): このサーバーを信頼し、すべてのツール呼び出しの確認をバイパスします。
  - **例:**
    ```json
    "mcpServers": {
      "myPythonServer": {
        "command": "python",
        "args": ["mcp_server.py", "--port", "8080"],
        "cwd": "./mcp_tools/python",
        "timeout": 5000
      },
      "myNodeServer": {
        "command": "node",
        "args": ["mcp_server.js"],
        "cwd": "./mcp_tools/node"
      },
      "myDockerServer": {
        "command": "docker",
        "args": ["run", "i", "--rm", "-e", "API_KEY", "ghcr.io/foo/bar"],
        "env": {
          "API_KEY": "$MY_API_TOKEN"
        }
      },
    }
    ```

- **`checkpointing`** (object):

  - **説明:** 会話とファイルの状態を保存および復元できるチェックポイント機能を構成します。詳細については、[チェックポイントのドキュメント](../checkpointing.md)を参照してください。
  - **デフォルト:** `{"enabled": false}`
  - **プロパティ:**
    - **`enabled`** (boolean): `true`の場合、`/restore`コマンドが利用可能になります。

- **`preferredEditor`** (string):

  - **説明:** 差分を表示するために使用する優先エディタを指定します。
  - **デフォルト:** `vscode`
  - **例:** `"preferredEditor": "vscode"`

- **`telemetry`** (object)
  - **説明:** Gemini CLIのロギングとメトリック収集を構成します。詳細については、[テレメトリ](../telemetry.md)を参照してください。
  - **デフォルト:** `{"enabled": false, "target": "local", "otlpEndpoint": "http://localhost:4317", "logPrompts": true}`
  - **プロパティ:**
    - **`enabled`** (boolean): テレメトリが有効かどうか。
    - **`target`** (string): 収集されたテレメトリの宛先。サポートされている値は`local`と`gcp`です。
    - **`otlpEndpoint`** (string): OTLPエクスポーターのエンドポイント。
    - **`logPrompts`** (boolean): ログにユーザープロンプトのコンテンツを含めるかどうか。
  - **例:**
    ```json
    "telemetry": {
      "enabled": true,
      "target": "local",
      "otlpEndpoint": "http://localhost:16686",
      "logPrompts": false
    }
    ```
- **`usageStatisticsEnabled`** (boolean):
  - **説明:** 使用状況統計の収集を有効または無効にします。詳細については、[使用状況統計](#usage-statistics)を参照してください。
  - **デフォルト:** `true`
  - **例:**
    ```json
    "usageStatisticsEnabled": false
    ```

### `settings.json`の例:

```json
{
  "theme": "GitHub",
  "sandbox": "docker",
  "toolDiscoveryCommand": "bin/get_tools",
  "toolCallCommand": "bin/call_tool",
  "mcpServers": {
    "mainServer": {
      "command": "bin/mcp_server.py"
    },
    "anotherServer": {
      "command": "node",
      "args": ["mcp_server.js", "--verbose"]
    }
  },
  "telemetry": {
    "enabled": true,
    "target": "local",
    "otlpEndpoint": "http://localhost:4317",
    "logPrompts": true
  },
  "usageStatisticsEnabled": true
}
```

## シェル履歴

CLIは、実行したシェルコマンドの履歴を保持します。異なるプロジェクト間の競合を避けるために、この履歴はユーザーのホームフォルダ内のプロジェクト固有のディレクトリに保存されます。

- **場所:** `~/.gemini/tmp/<project_hash>/shell_history`
  - `<project_hash>`は、プロジェクトのルートパスから生成された一意の識別子です。
  - 履歴は`shell_history`という名前のファイルに保存されます。

## 環境変数と`.env`ファイル

環境変数は、特にAPIキーなどの機密情報や、環境間で変更される可能性のある設定に対して、アプリケーションを構成する一般的な方法です。

CLIは、`.env`ファイルから環境変数を自動的に読み込みます。読み込み順序は次のとおりです。

1.  現在の作業ディレクトリにある`.env`ファイル。
2.  見つからない場合は、`.env`ファイルが見つかるか、プロジェクトルート（`.git`フォルダで識別）またはホームディレクトリに達するまで、親ディレクトリを上方に検索します。
3.  それでも見つからない場合は、`~/.env`（ユーザーのホームディレクトリ内）を探します。

- **`GEMINI_API_KEY`** (必須):
  - Gemini APIのAPIキー。
  - **操作に不可欠です。** これがないとCLIは機能しません。
  - シェルプロファイル（`~/.bashrc`、`~/.zshrc`など）または`.env`ファイルで設定します。
- **`GEMINI_MODEL`**:
  - 使用するデフォルトのGeminiモデルを指定します。
  - ハードコードされたデフォルトを上書きします
  - 例: `export GEMINI_MODEL="gemini-2.5-flash"`
- **`GOOGLE_API_KEY`**:
  - Google Cloud APIキー。
  - ExpressモードでVertex AIを使用するために必要です。
  - 必要な権限があることを確認し、`GOOGLE_GENAI_USE_VERTEXAI=true`環境変数を設定します。
  - 例: `export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"`。
- **`GOOGLE_CLOUD_PROJECT`**:
  - Google CloudプロジェクトID。
  - Code AssistまたはVertex AIを使用するために必要です。
  - Vertex AIを使用している場合は、必要な権限があることを確認し、`GOOGLE_GENAI_USE_VERTEXAI=true`環境変数を設定します。
  - 例: `export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"`。
- **`GOOGLE_APPLICATION_CREDENTIALS`** (string):
  - **説明:** Googleアプリケーション資格情報JSONファイルへのパス。
  - **例:** `export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/credentials.json"`
- **`OTLP_GOOGLE_CLOUD_PROJECT`**:
  - Google Cloudでのテレメトリ用のGoogle CloudプロジェクトID
  - 例: `export OTLP_GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"`。
- **`GOOGLE_CLOUD_LOCATION`**:
  - Google Cloudプロジェクトの場所（例: us-central1）。
  - Expressモード以外でVertex AIを使用するために必要です。
  - Vertex AIを使用している場合は、必要な権限があることを確認し、`GOOGLE_GENAI_USE_VERTEXAI=true`環境変数を設定します。
  - 例: `export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION"`。
- **`GEMINI_CODE_ASSIST`**:
  - Code Assist機能を有効にします。
  - `true`、`false`、またはカスタムコマンド文字列を受け入れます。
  - エンタープライズアカウントを使用している場合は、`GOOGLE_CLOUD_PROJECT`環境変数も設定する必要があります。
  - 例: `export GEMINI_CODE_ASSIST=true`。
- **`GEMINI_SANDBOX`**:
  - `settings.json`の`sandbox`設定の代替。
  - `true`、`false`、`docker`、`podman`、またはカスタムコマンド文字列を受け入れます。
- **`SEATBELT_PROFILE`** (macOS固有):
  - macOSでSeatbelt（`sandbox-exec`）プロファイルを切り替えます。
  - `permissive-open`: （デフォルト）プロジェクトフォルダ（および他のいくつかのフォルダ。`packages/cli/src/utils/sandbox-macos-permissive-open.sb`を参照）への書き込みを制限しますが、他の操作は許可します。
  - `strict`: デフォルトで操作を拒否する厳格なプロファイルを使用します。
  - `<profile_name>`: カスタムプロファイルを使用します。カスタムプロファイルを定義するには、プロジェクトの`.gemini/`ディレクトリに`sandbox-macos-<profile_name>.sb`という名前のファイルを作成します（例: `my-project/.gemini/sandbox-macos-custom.sb`）。
- **`DEBUG`または`DEBUG_MODE`** (多くの場合、基になるライブラリまたはCLI自体で使用されます):
  - `true`または`1`に設定して、詳細なデバッグロギングを有効にします。これは、トラブルシューティングに役立ちます。
- **`NO_COLOR`**:
  - CLIのすべてのカラー出力を無効にするには、任意の値に設定します。
- **`CLI_TITLE`**:
  - CLIのタイトルをカスタマイズするには、文字列に設定します。
- **`CODE_ASSIST_ENDPOINT`**:
  - コードアシストサーバーのエンドポイントを指定します。
  - これは、開発とテストに役立ちます。

## コマンドライン引数

CLIの実行時に直接渡される引数は、その特定のセッションの他の構成を上書きできます。

- **`--model <model_name>`** (**`-m <model_name>`**):
  - このセッションで使用するGeminiモデルを指定します。
  - 例: `npm start -- --model gemini-1.5-pro-latest`
- **`--prompt <your_prompt>`** (**`-p <your_prompt>`**):
  - プロンプトをコマンドに直接渡すために使用されます。これにより、Gemini CLIが非対話モードで呼び出されます。
- **`--sandbox`** (**`-s`**):
  - このセッションでサンドボックスモードを有効にします。
- **`--sandbox-image`**:
  - サンドボックスイメージURIを設定します。
- **`--debug_mode`** (**`-d`**):
  - このセッションでデバッグモードを有効にし、より詳細な出力を提供します。
- **`--all_files`** (**`-a`**):
  - 設定されている場合、現在のディレクトリ内のすべてのファイルをプロンプトのコンテキストとして再帰的に含めます。
- **`--help`** (または**`-h`**):
  - コマンドライン引数に関するヘルプ情報を表示します。
- **`--show_memory_usage`**:
  - 現在のメモリ使用量を表示します。
- **`--yolo`**:
  - YOLOモードを有効にします。これにより、すべてのツール呼び出しが自動的に承認されます。
- **`--telemetry`**:
  - [テレメトリ](../telemetry.md)を有効にします。
- **`--telemetry-target`**:
  - テレメトリターゲットを設定します。[テレメトリ](../telemetry.md)の詳細を参照してください。
- **`--telemetry-otlp-endpoint`**:
  - テレメトリのOTLPエンドポイントを設定します。[テレメトリ](../telemetry.md)の詳細を参照してください。
- **`--telemetry-log-prompts`**:
  - テレメトリのプロンプトのロギングを有効にします。[テレメトリ](../telemetry.md)の詳細を参照してください。
- **`--checkpointing`**:
  - [チェックポイント](./commands.md#checkpointing-commands)を有効にします。
- **`--version`**:
  - CLIのバージョンを表示します。

## コンテキストファイル（階層的指示コンテキスト）

CLIの*動作*の構成では厳密にはありませんが、コンテキストファイル（デフォルトは`GEMINI.md`ですが、`contextFileName`設定で構成可能）は、Geminiモデルに提供される*指示コンテキスト*（「メモリ」とも呼ばれます）を構成するために重要です。この強力な機能により、プロジェクト固有の指示、コーディングスタイルガイド、または関連する背景情報をAIに提供でき、その応答をニーズに合わせてより調整され、正確にすることができます。CLIには、フッターに読み込まれたコンテキストファイルの数を示すインジケーターなど、アクティブなコンテキストについて通知するためのUI要素が含まれています。

- **目的:** これらのMarkdownファイルには、対話中にGeminiモデルに認識させたい指示、ガイドライン、またはコンテキストが含まれています。システムは、この指示コンテキストを階層的に管理するように設計されています。

### コンテキストファイルのコンテンツの例（例: `GEMINI.md`）

TypeScriptプロジェクトのルートにあるコンテキストファイルの内容の概念的な例を次に示します。

```markdown
# プロジェクト: 私の素晴らしいTypeScriptライブラリ

## 一般的な指示:

- 新しいTypeScriptコードを生成するときは、既存のコーディングスタイルに従ってください。
- すべての新しい関数とクラスにJSDocコメントがあることを確認してください。
- 適切な場合は、関数型プログラミングパラダイムを優先してください。
- すべてのコードは、TypeScript 5.0およびNode.js 18+と互換性がある必要があります。

## コーディングスタイル:

- インデントには2つのスペースを使用します。
- インターフェイス名の前には`I`を付けます（例: `IUserService`）。
- プライベートクラスメンバーの前にはアンダースコア（`_`）を付けます。
- 常に厳密な等価性（`===`および`!==`）を使用します。

## 特定のコンポーネント: `src/api/client.ts`

- このファイルは、すべてのアウトバウンドAPIリクエストを処理します。
- 新しいAPI呼び出し関数を追加するときは、堅牢なエラー処理とロギングが含まれていることを確認してください。
- すべてのGETリクエストに既存の`fetchWithRetry`ユーティリティを使用します。

## 依存関係について:

- 絶対に必要な場合を除き、新しい外部依存関係を導入しないでください。
- 新しい依存関係が必要な場合は、その理由を述べてください。
```

この例は、一般的なプロジェクトコンテキスト、特定のコーディング規則、さらには特定のファイルやコンポーネントに関するメモを提供する方法を示しています。コンテキストファイルがより関連性があり、正確であるほど、AIはより適切に支援できます。プロジェクト固有のコンテキストファイルは、規則とコンテキストを確立するために強く推奨されます。

- **階層的な読み込みと優先順位:** CLIは、いくつかの場所からコンテキストファイル（`GEMINI.md`など）を読み込むことにより、高度な階層的メモリシステムを実装します。このリストの下位にあるファイル（より具体的）のコンテンツは、通常、上位にあるファイル（より一般的）のコンテンツを上書きまたは補足します。正確な連結順序と最終的なコンテキストは、`/memory show`コマンドを使用して検査できます。通常の読み込み順序は次のとおりです。
  1.  **グローバルコンテキストファイル:**
      - 場所: `~/.gemini/<contextFileName>`（例: ユーザーのホームディレクトリにある`~/.gemini/GEMINI.md`）。
      - スコープ: すべてのプロジェクトのデフォルトの指示を提供します。
  2.  **プロジェクトルートと祖先のコンテキストファイル:**
      - 場所: CLIは、現在の作業ディレクトリで構成されたコンテキストファイルを検索し、次にプロジェクトルート（`.git`フォルダで識別）またはホームディレクトリまでの各親ディレクトリで検索します。
      - スコープ: プロジェクト全体またはその大部分に関連するコンテキストを提供します。
  3.  **サブディレクトリコンテキストファイル（コンテキスト/ローカル）:**
      - 場所: CLIは、現在の作業ディレクトリの*下*のサブディレクトリで構成されたコンテキストファイルもスキャンします（`node_modules`、`.git`などの一般的な無視パターンを尊重します）。
      - スコープ: プロジェクトの特定のコンポーネント、モジュール、またはサブセクションに関連する非常に具体的な指示を可能にします。
- **連結とUI表示:** 見つかったすべてのコンテキストファイルの内容は（その起点とパスを示す区切り文字とともに）連結され、システムプロンプトの一部としてGeminiモデルに提供されます。CLIフッターには、読み込まれたコンテキストファイルの数が表示され、アクティブな指示コンテキストをすばやく視覚的に確認できます。
- **メモリ管理用のコマンド:**
  - `/memory refresh`を使用して、構成されているすべての場所からすべてのコンテキストファイルを強制的に再スキャンして再読み込みします。これにより、AIの指示コンテキストが更新されます。
  - `/memory show`を使用して、現在読み込まれている結合された指示コンテキストを表示し、AIが使用している階層とコンテンツを確認できます。
  - `/memory`コマンドとそのサブコマンド（`show`と`refresh`）の詳細については、[コマンドのドキュメント](./commands.md#memory)を参照してください。

これらの構成レイヤーとコンテキストファイルの階層的な性質を理解して利用することで、AIのメモリを効果的に管理し、Gemini CLIの応答を特定のニーズやプロジェクトに合わせて調整できます。

## サンドボックス化

Gemini CLIは、システムを保護するために、サンドボックス化された環境内で潜在的に安全でない操作（シェルコマンドやファイルの変更など）を実行できます。

サンドボックス化はデフォルトで無効になっていますが、いくつかの方法で有効にできます。

- `--sandbox`または`-s`フラグを使用します。
- `GEMINI_SANDBOX`環境変数を設定します。
- サンドボックスは、デフォルトで`--yolo`モードで有効になっています。

デフォルトでは、事前にビルドされた`gemini-cli-sandbox` Dockerイメージを使用します。

プロジェクト固有のサンドボックス化のニーズに合わせて、プロジェクトのルートディレクトリに`.gemini/sandbox.Dockerfile`でカスタムDockerfileを作成できます。このDockerfileは、ベースのサンドボックスイメージに基づいています。

```dockerfile
FROM gemini-cli-sandbox

# ここにカスタムの依存関係または構成を追加します
# 例:
# RUN apt-get update && apt-get install -y some-package
# COPY ./my-config /app/my-config
```

`.gemini/sandbox.Dockerfile`が存在する場合、Gemini CLIの実行時に`BUILD_SANDBOX`環境変数を使用して、カスタムサンドボックスイメージを自動的にビルドできます。

```bash
BUILD_SANDBOX=1 gemini -s
```

## 使用状況統計

Gemini CLIの改善に役立てるため、匿名化された使用状況統計を収集しています。このデータは、CLIの使用方法を理解し、一般的な問題を特定し、新機能の優先順位を付けるのに役立ちます。

**収集する内容:**

- **ツール呼び出し:** 呼び出されたツールの名前、成功したか失敗したか、実行にかかった時間をログに記録します。ツールに渡された引数やツールから返されたデータは収集しません。
- **APIリクエスト:** 各リクエストに使用されたGeminiモデル、リクエストの期間、成功したかどうかをログに記録します。プロンプトや応答の内容は収集しません。
- **セッション情報:** 有効になっているツールや承認モードなど、CLIの構成に関する情報を収集します。

**収集しない内容:**

- **個人を特定できる情報（PII）:** 名前、メールアドレス、APIキーなどの個人情報は収集しません。
- **プロンプトと応答のコンテンツ:** プロンプトの内容やGeminiモデルからの応答はログに記録しません。
- **ファイルコンテンツ:** CLIによって読み書きされたファイルのコンテンツはログに記録しません。

**オプトアウトする方法:**

`settings.json`ファイルで`usageStatisticsEnabled`プロパティを`false`に設定することで、いつでも使用状況統計の収集をオプトアウトできます。

```json
{
  "usageStatisticsEnabled": false
}
```

**プライバシーポリシー:**
収集されたデータは、[Googleプライバシーポリシー](https://policies.google.com/privacy)の対象となります。