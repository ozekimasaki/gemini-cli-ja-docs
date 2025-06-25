# Webフェッチツール (`web_fetch`)

このドキュメントでは、Gemini CLIの`web_fetch`ツールについて説明します。

## 説明

`web_fetch`を使用して、Webページから情報を要約、比較、または抽出します。`web_fetch`ツールは、プロンプトに埋め込まれた1つ以上のURL（最大20）のコンテンツを処理します。`web_fetch`は自然言語のプロンプトを受け取り、生成された応答を返します。

### 引数

`web_fetch`は1つの引数を取ります。

- `prompt` (string, 必須): フェッチするURL（最大20）と、そのコンテンツを処理する方法に関する具体的な指示を含む包括的なプロンプト。例: `"https://example.com/article を要約し、https://another.com/data からキーポイントを抽出してください"`。プロンプトには、`http://`または`https://`で始まるURLが少なくとも1つ含まれている必要があります。

## Gemini CLIで`web_fetch`を使用する方法

Gemini CLIで`web_fetch`を使用するには、URLを含む自然言語のプロンプトを指定します。ツールはURLをフェッチする前に確認を求めます。確認されると、ツールはGemini APIの`urlContext`を介してURLを処理します。

Gemini APIがURLにアクセスできない場合、ツールはローカルマシンから直接コンテンツをフェッチするようにフォールバックします。ツールは、可能な場合は出典の帰属と引用を含めて応答をフォーマットします。その後、ツールはユーザーに応答を提供します。

使用法:

```
web_fetch(prompt="https://google.com などのURLを含むプロンプト。")
```

## `web_fetch`の例

単一の記事を要約します。

```
web_fetch(prompt="https://example.com/news/latest の要点を要約してもらえますか")
```

2つの記事を比較します。

```
web_fetch(prompt="これら2つの論文の結論の違いは何ですか: https://arxiv.org/abs/2401.0001 と https://arxiv.org/abs/2401.0002？")
```

## 重要な注意点

- **URL処理:** `web_fetch`は、指定されたURLにアクセスして処理するGemini APIの機能に依存します。
- **出力品質:** 出力の品質は、プロンプト内の指示の明確さによって異なります。