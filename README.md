# video-exp — AI 短剧流水线共享经验库

`video-*` 系列 skill 的经验真源。五个 skill 共用这一个仓，互相能读到对方的经验。

| skill | 职责 |
|---|---|
| `video-writer` | 编剧：题材、人物、结构、台词，并按 AI 生成成本约束剧本 |
| `video-costume` | 定妆照：多角度、妆造硬约束、真人质感校验 |
| `video-director` | 导演：分镜、景别、运镜、镜头语言、关键帧策略 |
| `video-editor` | 剪辑：调生成引擎出片、拼接、音轨字幕、成本核算 |
| `video-audience` | 观众：抽帧质检连贯性/人脸一致性/服装漂移 |

## 目录

```
exp/<skill>/<slug>.md    该 skill 的经验条目
exp/_shared/             跨 skill 通用经验
INDEX.md                 自动生成的索引（video-exp reindex）
```

## 用法

```bash
video-exp sync                    # 开工前拉最新
video-exp search 侧脸 一致性       # 搜经验
video-exp show <slug>             # 看全文
video-exp new <skill> <slug> 标题 # 打印模板
video-exp save <skill> <slug>     # 从 stdin 存入并 push
```

## 经验条目写法

一条经验必须包含四段：**场景 / 结论 / 证据 / 反例**。

- **结论**要写成可直接执行的口径，不是感想
- **证据**放实测数据和原始报错文本，便于以后 grep 命中
- **反例**写错误做法及其后果，比正面结论更容易在下次被识别出来

只沉淀**可复用**的东西。一次性的任务状态、某部片的具体剧情，不入库。
