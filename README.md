# ローカルLLMを活用したWeb検索チャット

Qwen3をローカル環境で動作させ、TavilyによるWeb検索、会話履歴、Reflection（回答評価・再生成）を組み合わせたチャットシステムです。

単純にWeb検索を毎回実行するのではなく、ユーザーの質問から検索の必要性を判定し、必要な場合のみWeb情報を取得する構成を試みました。

## 主な機能

- Qwen3によるローカルLLMでの回答生成
- 質問内容に応じたWeb検索要否の判定
- Tavilyを利用したWeb検索
- 「今日」「昨日」などの相対日付を具体的な日付へ変換
- 会話履歴を利用した継続的な対話
- 別モデルによるReflection（回答評価）
- Reflectionで問題を検出した場合の回答再生成
- 各処理および全体の処理時間計測

## 使用モデル

- **Qwen/Qwen3-1.7B**
  - Web検索要否判定
  - 初回回答生成

- **Qwen/Qwen3-4B**
  - Reflection
  - Reflection失敗時の回答再生成

両モデルを4bit量子化してローカル環境で実行しています。

## 処理フロー

```text
ユーザー入力
    ↓
相対日付の変換
    ↓
Web検索要否判定
    ↓
必要な場合のみTavily検索
    ↓
Qwen3-1.7Bによる回答生成
    ↓
Qwen3-4BによるReflection
    ↓
FAILの場合はQwen3-4Bで再生成
    ↓
最終回答
    ↓
会話履歴へ保存
```

## 使用技術

- Python
- Jupyter Notebook
- Qwen3
- Hugging Face Transformers
- PyTorch
- bitsandbytes（4bit量子化）
- Tavily API

## 実行環境

- Windows
- NVIDIA GeForce RTX 4070 12GB
- Python 3.12
- CUDA環境

Tavilyを使用するため、環境変数 `TAVILY_API_KEY` の設定が必要です。

## 動作検証と課題

動作検証では、最新情報に対するWeb検索、相対日付の変換、会話履歴の参照、Reflectionと再生成が動作することを確認しました。

一方で、検索不要と考えられる質問でもWeb検索が選択されるケースや、長い会話履歴を参照した際に履歴に存在しない内容が生成されるケースも確認しました。

また、Reflectionで問題を検出しても再生成によって必ず修正されるとは限らず、小型ローカルLLMによる判定・生成品質と処理時間のトレードオフも確認できました。

これらの実際の検証結果と今後の改善点についてはNotebook内に記載しています。

## Notebook

`02_ローカルLLMを活用したWeb検索チャット.ipynb`

実装コード、各処理の説明、実際の動作結果、総括をNotebook内にまとめています。
