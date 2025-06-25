# Gemini CLI拡張機能

Gemini CLIは、その機能を構成および拡張するために使用できる拡張機能をサポートしています。

## 仕組み

起動時に、Gemini CLIは次の2つの場所で拡張機能を検索します。

1.  `<workspace>/.gemini/extensions`
2.  `<home>/.gemini/extensions`

Gemini CLIは、両方の場所からすべての拡張機能を読み込みます。同じ名前の拡張機能が両方の場所に存在する場合、ワークスペースディレクトリの拡張機能が優先されます。

各場所内で、個々の拡張機能は`gemini-extension.json`ファイルを含むディレクトリとして存在します。例:

`<workspace>/.gemini/extensions/my-extension/gemini-extension.json`

### `gemini-extension.json`

`gemini-extension.json`ファイルには、拡張機能の構成が含まれています。ファイルには次の構造があります。

```json
{
  "name": "my-extension",
  "version": "1.0.0",
  "mcpServers": {
    "my-server": {
      "command": "node my-server.js"
    }
  },
  "contextFileName": "GEMINI.md"
}
```

- `name`: 拡張機能の名前。これは、拡張機能を一意に識別するために使用されます。これは、拡張機能ディレクトリの名前と一致する必要があります。
- `version`: 拡張機能のバージョン。
- `mcpServers`: 構成するMCPサーバーのマップ。キーはサーバーの名前で、値はサーバーの構成です。これらのサーバーは、[`settings.json`ファイル](./cli/configuration.md)で構成されたMCPサーバーと同様に起動時に読み込まれます。拡張機能と`settings.json`ファイルの両方が同じ名前のMCPサーバーを構成する場合、`settings.json`ファイルで定義されたサーバーが優先されます。
- `contextFileName`: 拡張機能のコンテキストを含むファイルの名前。これは、ワークスペースからコンテキストを読み込むために使用されます。このプロパティが使用されていないが、拡張機能ディレクトリに`GEMINI.md`ファイルが存在する場合、そのファイルが読み込まれます。

Gemini CLIが起動すると、すべての拡張機能を読み込み、それらの構成をマージします。競合がある場合、ワークスペースの構成が優先されます。