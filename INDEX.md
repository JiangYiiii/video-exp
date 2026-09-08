# 经验索引

由 `video-exp reindex` 自动生成，不要手改。

## video-writer

| slug | 标题 | tags |
|---|---|---|
| [no-emotion-metaphors-in-prompt](exp/video-writer/no-emotion-metaphors-in-prompt.md) | 剧本里的情绪比喻会被模型字面执行，必须改写成可拍摄的生理反应 | 台词, 情绪, 比喻, 负面词, 红眼睛 |

## video-costume

| slug | 标题 | tags |
|---|---|---|
| [multi-angle-refs-required](exp/video-costume/multi-angle-refs-required.md) | 定妆照必须出正面/45°/正侧三档，只有正面会导致侧拍镜头人物不像 | 定妆照, 侧脸, 多角度, 一致性, reference_image |
| [trio-base-first-then-derive-individuals](exp/video-costume/trio-base-first-then-derive-individuals.md) | 多人同框先出同一次生成的基准图，再反推个人定妆照，否则肤色和头身比必然对不上 | 定妆照, 多人同框, 肤色一致, 头身比, 取景规则, 拼贴感 |

## video-director

| slug | 标题 | tags |
|---|---|---|
| [prop-handoff-becomes-static-result](exp/video-director/prop-handoff-becomes-static-result.md) | 道具交接必崩，改成只拍静止结果；交接是手势不是转折，可以省 | 道具, 交接, 校验门槛, 连贯性边界, 插入镜 |
| [shot-continuity-over-editing-tricks](exp/video-director/shot-continuity-over-editing-tricks.md) | 关键动作转折不能用硬切省掉，连贯性优先于剪辑技巧 | 分镜, 连贯性, 硬切, 过渡镜, 返工 |

## video-editor

| slug | 标题 | tags |
|---|---|---|
| [wan3-first-frame-vs-reference-exclusive](exp/video-editor/wan3-first-frame-vs-reference-exclusive.md) | 万相 wan3.0 的 first_frame 与 reference_image 互斥，按镜头二选一 | wan3.0, first_frame, reference_image, 互斥, 比例, 免费额度 |

## video-audience

| slug | 标题 | tags |
|---|---|---|
| [frame-extraction-qc-protocol](exp/video-audience/frame-extraction-qc-protocol.md) | 抽帧质检要先三帧粗筛再密抽，人脸必须与同角度定妆照对比 | 抽帧, 质检, ffmpeg, 一致性, 转场 |
