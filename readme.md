# aiSearch：Perplexity AI vs. Gemini API 比較ツール

この Colab ノートブックは、Perplexity AI API と Google Gemini API の両方に対して同じ質問を行い、その回答を直接比較するためのツールです。回答の質、スタイル、引用の有無などを分析することができます。

## はじめに

### 前提条件

*   Google Colab 環境
*   Perplexity AI API キー ([https://www.perplexity.ai/](https://www.perplexity.ai/))
*   Google Generative AI API キー ([https://ai.google.dev/tutorials/setup](https://ai.google.dev/tutorials/setup))

### インストール

Colab ノートブックで次のセルを実行して、必要なライブラリをインストールします。

```python
!pip install requests google-generativeai
```

### API キーの設定

1.  **Perplexity AI API キー:** Perplexity AI から API キーを取得し、ノートブック内の `perplexity_api_key` 変数に設定します。

    ```python
    perplexity_api_key = "YOUR_PERPLEXITY_API_KEY"
    ```

2.  **Google Generative AI API キー:** Google API キーを設定する方法は2つあります。

    *   **直接設定（共有には非推奨）:** `genai.configure` 行の `"YOUR_GOOGLE_API_KEY"` を実際の API キーに置き換えます。

        ```python
        genai.configure(api_key="YOUR_GOOGLE_API_KEY")
        ```

    *   **環境変数（推奨）:**
        *   Colab 環境で `GOOGLE_API_KEY` 環境変数を設定します。 これは、`genai.configure` ステートメントを実行する*前に* `os.environ` コマンドを使用するか、Colab のシークレット機能（キー名は "GOOGLE_API_KEY" である必要があります）を使用することで行えます。
        *   コードは、環境変数が設定されている場合は自動的に検出し、使用します。

        ```python
        import os
        import google.generativeai as genai

        # オプション 1: Colab から手動で環境変数を設定
        # os.environ["GOOGLE_API_KEY"] = "YOUR_GOOGLE_API_KEY"

        google_api_key = os.environ.get("GOOGLE_API_KEY")

        if google_api_key:
            genai.configure(api_key=google_api_key)
        else:
            print("エラー: GOOGLE_API_KEY 環境変数が設定されていません。")
            exit()
        ```

    **重要:** API キーを公開リポジトリに直接コミットしないでください。 環境変数または Colab のシークレット機能を使用することを強くお勧めします。

### 質問の入力

`question` 変数を変更して、比較に使用する質問を指定します。

```python
question = "トヨタの役員の役職と名前は何ですか？"
```

## 比較の実行

Colab ノートブックを実行します。 ノートブックは次の処理を行います。

1.  指定された質問を Perplexity AI API と Google Gemini API の両方に尋ねます。
2.  各 API からの応答を Markdown 形式で出力します。
3.  応答の分析をガイドするための比較メモを提供します。

## コードの説明

### `ask_perplexity(question, api_key)`

この関数は、指定された API キーを使用して、Perplexity AI の `/chat/completions` エンドポイントにリクエストを送信します。 次のパラメータを使用して JSON ペイロードを構築します。

*   `model`: 使用するモデルを指定します（現在は `"sonar-pro"`）。
*   `messages`: 会話のコンテキストを定義します。 モデルに正確かつ簡潔であること、および引用を提供することを指示するシステムメッセージと、ユーザーの質問が含まれます。
*   `max_tokens`: 応答の長さを制限します。
*   `temperature`: 応答のランダム性を制御します。
*   `top_p`: 応答の多様性を制御します。
*   `stream`: `False` に設定すると、応答全体を一度に受信します。

この関数は、ネットワークの問題や不正な形式の API 応答などの潜在的なエラーを処理します。

### `ask_gemini(question)`

この関数は、`google-generativeai` ライブラリを使用して Google Gemini API と対話します。 `"gemini-2.0-pro-exp-02-05"` モデルを使用して `GenerativeModel` を初期化し、`generate_content` メソッドを呼び出して応答を取得します。 また、潜在的な例外に対するエラー処理も含まれています。

## 結果の分析

ノートブックを実行した後、次の基準に基づいて Perplexity AI と Gemini からの応答を注意深く比較します。

*   **正確性:** 提供される情報は正確で事実ですか？
*   **完全性:** 応答は質問に完全に答えていますか？ 不足している詳細はありますか？
*   **明瞭さ:** 応答は理解しやすいですか？ 整理されていますか？
*   **引用:** 応答はその主張を裏付ける引用またはソースを提供していますか？ 引用は関連性があり信頼できますか？
*   **スタイルとトーン:** 応答の全体的なスタイルとトーンは何ですか？ 専門的で客観的ですか？

異なる質問でプロセスを繰り返して、各 API の長所と短所をより包括的に理解します。

## 免責事項

このノートブックは、教育および比較目的のみを意図しています。 結果は、特定の質問と使用する API バージョンによって異なる場合があります。 AI モデルから提供された情報を信頼する前に、必ず確認してください。 このノートブックで指定されているモデルは例であり、変更される可能性があります。

## リポジトリ: aiSearch

このノートブックと関連するドキュメントは、GitHub リポジトリ [aiSearch](<https://github.com/terisuke/aiSearch>) で管理されています。
