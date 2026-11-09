# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：誤った回答はせずに資料を参照している

$ uv run python -m app.agent "AIE2026の講座の宿題にある「3レベル」 とは何ですか？"
[model] gemini-3.5-flash-lite
AIE2026の宿題における「3レベル」とは、宿題の難易度や取り組み方の段階のことです。

具体的には以下の3つのレベルで構成されています：

1. **基礎**：講義内容の確認
2. **実装**：自分でコードを書く
3. **発展**：調査や試行（応用）

3つのうち**どれか1つ**に取り組めば、その回の宿題は完了となります。なお、原則として採点は行われません。


- `--trace` で見えたツール呼び出し（ツール名と引数）：
$ uv run python -m app.agent --trace "LoRA とQLoRAの違いは？ "
[model] gemini-3.5-flash-lite
講座の用語集によると、LoRAとQLoRAの違いは以下のとおりです。

* **LoRA**
  事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法です。学習するパラメータが少なく、学習結果（アダプタ）も小さなファイルで保存できます。
* **QLoRA**
  ベースモデルの重みを4ビットなどに**量子化**してメモリを節約したうえで、LoRAで学習する手法です。これにより、より大きなモデルを小さなGPUで扱うことが可能になります。

関係する回：第2回

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008244'), details={'text_prompt_tokens': 1198}, input_text_tokens=1198, i

- 1つの質問での LLM 呼び出し回数（`requests`）：2
- 追加したツールと、docstring に書いた説明：make_quiz
    """
    指定した講義回の用語から1つを選び、
    復習用の確認問題と答えを作るときに使う。
    """

## 宿題

- 取り組んだレベル：基礎
- やったこと：search_glossaryのdocstring内の検索条件を変更する。
変更後に
$ uv run python -m app.agent --trace "LoRAとQLoRAの違いを教えて"
を実行

    """講座の用語集から用語を検索し、定義と、どの回で扱うかを返す。

    AIエンジニアリングの用語や、この講座（AIE2026）に固有の言葉について
    答える前に使うこと。query には調べたい用語を複数入れる。
    """

- 結果：trace内のrequest回数が2から1に減少した
- 気づき・疑問：同じ用語集に存在する単語を検索するときはLLMの呼び出し回数を減少させることでトークンや実行時間の減少につながった。
