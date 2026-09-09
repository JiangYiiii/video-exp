# 经验索引

由 `video-exp reindex` 自动生成，不要手改。

## video-writer

| slug | 标题 | tags |
|---|---|---|
| [character-voice-anchor-required](exp/video-writer/character-voice-anchor-required.md) | 带 TTS 的引擎必须在剧本阶段给每个角色标注声音特色，否则同性角色全是同一把默认声 | 声音, 音色, TTS, 人物设定, 一致性锚点 |
| [no-emotion-metaphors-in-prompt](exp/video-writer/no-emotion-metaphors-in-prompt.md) | 剧本里的情绪比喻会被模型字面执行，必须改写成可拍摄的生理反应 | 台词, 情绪, 比喻, 负面词, 红眼睛 |

## video-costume

| slug | 标题 | tags |
|---|---|---|
| [angle-turn-drifts-hairstyle](exp/video-costume/angle-turn-drifts-hairstyle.md) | 转正侧面时发型会被"整理"成发髻，必须在 prompt 里点名禁止并复述发型 | 定妆照, 侧脸, 转角度, 发型漂移, 一致性, prompt写法 |
| [framing-rule-needs-measurable-anchor](exp/video-costume/framing-rule-needs-measurable-anchor.md) | 统一取景规则必须给可判定的物理锚点，"七分身"这类词模型会各自发挥 | 定妆照, 取景规则, 头身比, 多角色, 一致性, prompt写法 |
| [multi-angle-refs-required](exp/video-costume/multi-angle-refs-required.md) | 定妆照必须出正面/45°/正侧三档，只有正面会导致侧拍镜头人物不像 | 定妆照, 侧脸, 多角度, 一致性, reference_image |
| [trio-base-first-then-derive-individuals](exp/video-costume/trio-base-first-then-derive-individuals.md) | 多人同框先出同一次生成的基准图，再反推个人定妆照，否则肤色和头身比必然对不上 | 定妆照, 多人同框, 肤色一致, 头身比, 取景规则, 拼贴感 |

## video-director

| slug | 标题 | tags |
|---|---|---|
| [prop-handoff-becomes-static-result](exp/video-director/prop-handoff-becomes-static-result.md) | 道具交接必崩，改成只拍静止结果；交接是手势不是转折，可以省 | 道具, 交接, 校验门槛, 连贯性边界, 插入镜 |
| [shot-continuity-over-editing-tricks](exp/video-director/shot-continuity-over-editing-tricks.md) | 关键动作转折不能用硬切省掉，连贯性优先于剪辑技巧 | 分镜, 连贯性, 硬切, 过渡镜, 返工 |
| [split-shots-need-their-own-action](exp/video-director/split-shots-need-their-own-action.md) | 拆镜必须给每个新镜独立的动作目的，只拆构图会变成摆拍；且人越多脸越不像，情绪特写一律单人镜 | 拆镜, 摆拍, 动作目的, 静默镜, 每镜人数, 人脸保真 |

## video-editor

| slug | 标题 | tags |
|---|---|---|
| [face-fidelity-degrades-with-person-count](exp/video-editor/face-fidelity-degrades-with-person-count.md) | 多人帧的人脸保真度随人数下降且越靠后越差；wan3.0 轮询断线要 bvid get 续拉别重提，输入首帧会被内容审核拦 | 身份平均化, input_fidelity, 多人帧, 轮询断线, DataInspectionFailed, 重复扣费 |
| [first-frame-mode-drift-root-cause-is-the-keyframe](exp/video-editor/first-frame-mode-drift-root-cause-is-the-keyframe.md) | 首帧模式下的漂移根因一定在首帧本身，改视频 prompt 是白改；关键帧质检必须看原图不能看缩略拼图 | first_frame, 首帧模式, 发型漂移, 根因判断, 关键帧质检, 重抽, 白花钱 |
| [intimate-shots-route-to-dreamina](exp/video-editor/intimate-shots-route-to-dreamina.md) | 亲密镜头的关键帧必须走即梦，gpt-image-2 网关硬拦；视频 prompt 不要再描述亲密动作 | 内容审核, 亲密镜头, 即梦, gpt-image-2, 平台分工, 局部特写 |
| [wan3-first-frame-vs-reference-exclusive](exp/video-editor/wan3-first-frame-vs-reference-exclusive.md) | 万相 wan3.0 的 first_frame 与 reference_image 互斥，按镜头二选一 | wan3.0, first_frame, reference_image, 互斥, 比例, 免费额度 |

## video-audience

| slug | 标题 | tags |
|---|---|---|
| [frame-extraction-qc-protocol](exp/video-audience/frame-extraction-qc-protocol.md) | 抽帧质检要先三帧粗筛再密抽，人脸必须与同角度定妆照对比 | 抽帧, 质检, ffmpeg, 一致性, 转场 |
