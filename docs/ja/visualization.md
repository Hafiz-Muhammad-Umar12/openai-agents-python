---
search:
  exclude: true
---
# エージェント可視化

エージェント可視化を使用すると、 **Graphviz** を利用してエージェントとその関係を構造化されたグラフィカル表現として生成できます。これは、アプリケーション内でエージェント、ツール、ハンドオフがどのように相互作用するかを理解するのに役立ちます。

## インストール

オプションの `viz` 依存グループをインストールします:

```bash
pip install "openai-agents[viz]"
```

## グラフ生成

`draw_graph` 関数を使用してエージェントの可視化を生成できます。この関数は次のような有向グラフを作成します:

-  **エージェント**  は黄色のボックスで表されます。  
-  **ツール**  は緑色の楕円で表されます。  
-  **ハンドオフ**  はエージェント間の有向エッジで表されます。

### 使用例

```python
from agents import Agent, function_tool
from agents.extensions.visualization import draw_graph

@function_tool
def get_weather(city: str) -> str:
    return f"The weather in {city} is sunny."

spanish_agent = Agent(
    name="Spanish agent",
    instructions="You only speak Spanish.",
)

english_agent = Agent(
    name="English agent",
    instructions="You only speak English",
)

triage_agent = Agent(
    name="Triage agent",
    instructions="Handoff to the appropriate agent based on the language of the request.",
    handoffs=[spanish_agent, english_agent],
    tools=[get_weather],
)

draw_graph(triage_agent)
```

![エージェント グラフ](../assets/images/graph.png)

これにより、 **triage agent** の構造とサブエージェントおよびツールとの接続を視覚的に示すグラフが生成されます。

## 可視化の概要

生成されたグラフには次が含まれます:

- エントリーポイントを示す **start node** (`__start__`)  
- 黄色で塗られた **rectangles** として表されるエージェント  
- 緑色で塗られた **ellipses** として表されるツール  
- 相互作用を示す有向エッジ  
  - エージェント間ハンドオフを示す **Solid arrows**  
  - ツール呼び出しを示す **Dotted arrows**  
- 実行終了位置を示す **end node** (`__end__`)

## グラフのカスタマイズ

### グラフの表示
デフォルトでは、`draw_graph` はグラフをインラインで表示します。別ウィンドウで表示するには、次のように記述します:

```python
draw_graph(triage_agent).view()
```

### グラフの保存
デフォルトでは、`draw_graph` はグラフをインラインで表示します。ファイルとして保存するには、ファイル名を指定します:

```python
draw_graph(triage_agent, filename="agent_graph")
```

これにより、作業ディレクトリに `agent_graph.png` が生成されます。