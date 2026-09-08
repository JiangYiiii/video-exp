---
title: 抽帧质检要先三帧粗筛再密抽，人脸必须与同角度定妆照对比
skill: video-audience
schema: video-exp/1
tags: 抽帧, 质检, ffmpeg, 一致性, 转场
severity: medium
date: 2026-09-08
---

## 场景

对 6 镜 23s 的成片做质检，需要判断剧情是否连贯、人脸是否一致、服装有没有漂移。

## 结论

**抽帧策略：先每镜首/中/尾三帧粗筛，命中问题再对那一镜按 2fps 密抽。**
一上来全片 2fps 是浪费。

```bash
d=$(ffprobe -v error -show_entries format=duration -of csv=p=0 clip.mp4)
ffmpeg -v error -y -ss <t> -i clip.mp4 -frames:v 1 frames/shotN_t<t>.png
ffmpeg -v error -y -i clip.mp4 -vf fps=2 frames/shotN_%03d.png   # 密抽
```

三条容易漏的要点：

1. **转场处必抽**：前一镜的尾帧 + 后一镜的首帧并排看，这是连贯性问题的高发区。
2. **人脸对比必须用同角度的定妆照做基准** —— 侧拍镜要和 angles.side 比，
   不能和正面比，否则会把正常的透视差异误判成漂移。
3. **强动作镜头（拥抱/抵墙/亲吻）是服装漂移高发区**，要逐帧核对 costume_hard。

每条 finding 必须附抽帧路径作为证据，不许只给结论。给建议时要指出**根因和该退回
哪一棒**：侧脸不像→退 costume 补角度；服装漂移→退 editor 改 prompt 重抽；
动作糊→退 director 拆镜；不连贯→退 director 补过渡镜（**重抽修不了**）。

## 证据

本片实测：6 镜 × 3 帧 = 18 张粗筛即可定位问题镜位。
成片规格核对用：

```bash
ffprobe -v error -select_streams v:0 \
  -show_entries stream=width,height,duration -of csv=p=0:nk=1 clip.mp4
```

拼接前必须确认所有 clip 分辨率一致，否则 ffmpeg -c copy 会出问题。
本片 6 镜均为 1080×1920，拼接后 23.2s 与分镜表期望 23s 一致。

## 反例

❌ 只看成片不抽帧 —— 一致性问题在动态播放时容易被忽略，静帧并排才看得出
❌ 拿正面定妆照去比对侧拍镜头的脸 —— 会产生大量假阳性
❌ 连贯性 fail 却给 verdict=retake —— 缺镜头重抽多少次都补不出来
