---
title: 远景小脸修脸前先 2 倍放大再换脸、换两遍，眼睛和纹理更像本人；脸型仍由换人底片决定
skill: video-reface
schema: video-exp/1
tags: 换人, reface, 修脸, FaceFusion, inswapper_128, 放大, 小脸, 远景, 二次换脸, 原片挑选
severity: medium
date: 2026-11-23
---

## 场景

百炼 animate-mix std 换人后成片 720x1280，全身远景，脸只有约 60px 宽。
用 FaceFusion inswapper_128 + pixel boost 512 拿本人真照修脸，用户反馈「脸不太像」。

## 结论

**先把整段视频 lanczos 放大 2 倍（1440x2560），再跑 inswapper_128 + pixel boost 1024，然后在结果上再跑一遍，最后缩回 720x1280 并调肤色。**
用户在同一裁切的对比里选定了这一版（F）。眼睛、法令纹、皱纹更接近真照。

局限：脸型、发型轮廓、光线没有变化。修脸只换五官，脸型在 animate 底片那一步就定了，免费手段到此为止。

成本：0 元，但第二遍很慢：8s/120 帧，第一遍约 6 分钟，第二遍约 58 分钟（M3 Pro，coreml）。
先用单帧试（每帧约 10s），用户选定后再跑整段。

附带观察：原片舞者是年轻东亚女性（长发双马尾、露腰上衣），换成中老年东亚女性后，长发和露腰都**没有**漏进成片，脸也没有被拉长或西化。
相比之下，黑人女性爆炸头的原片漏了发髻，白人男性的原片漏了骨相。人种和性别对上，比发型、衣服对上更重要。

## 证据

- ~/Movies/dance-6003986/check/facetune.png：单帧 A 原做法 / B 先放大 / C 二次 / F 先放大加二次，第 3、5 秒同裁切
- ~/Movies/dance-6003986/check/final_BF.png：整段成片，真照 / 修前 / 旧版 / B / F
- ~/Movies/dance-6003986/check/compare_v1.png：原片对位，无长发、无露腰
- hyperswap_1b_256、simswap_unofficial_512 首次下载卡住，未参与对比

用法：`ffmpeg -vf scale=iw*2:ih*2:flags=lanczos` 放大 → facefusion `--face-swapper-model inswapper_128 --face-swapper-pixel-boost 1024x1024` → 结果再作 `-t` 跑一遍 → 缩回原尺寸 → tone_and_tuft.py

## 反例

- 只在原尺寸上把 A 再换一遍（C）：几乎没有变化，反而略糊。分辨率不够时，多换几遍没用，要先放大。
- 拿这招去救脸型被原片带偏的底片（如白人男性街舞版）：五官回来了，长脸还在。还是要先挑对原片。
