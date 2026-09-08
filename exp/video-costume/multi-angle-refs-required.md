---
title: 定妆照必须出正面/45°/正侧三档，只有正面会导致侧拍镜头人物不像
skill: video-costume
schema: video-exp/1
tags: 定妆照, 侧脸, 多角度, 一致性, reference_image
severity: high
date: 2026-09-08
---

## 场景

《有关系》的女主只做了一张正面定妆照并固化为 LOCKED 脸。但分镜里按墙、亲吻等
镜头全是侧拍，生成时模型只能从正面脸去推侧面轮廓。

## 结论

**每个角色的定妆照默认就出三档，不要等发现侧脸不像了再补：**

- 正面（LOCKED，五官基准）
- 45° 三分侧（同时看到正面五官和侧面轮廓线）
- 90° 正侧面（**完整呈现额头→鼻梁→人中→嘴唇→下巴→下颌线的轮廓线**，只见一只眼）

做法：拿**已定稿的正面定妆照**当输入图去转角度，不要从原始生活照重新推。
prompt 里强调"必须和参考图是同一个人，脸型骨相、五官比例、发型发色、服装全部一致，
只是拍摄角度不同"。

⚠️ 喂给模型时注意：**万相 wan3.0 的 first_frame 与 reference_image 互斥**，
所以要锁脸的镜头必须整组走 reference_image（关键帧也当参考图传），
并显式指定 --ratio。

## 证据

用户看完成片的原话：**「女主侧脸还是不太像，下次是否考虑定妆有侧脸？」**

万相报错原文（说明为什么不能首帧+参考图并用）：

```
InvalidParameter: first_frame cannot be combined with other media types
except last_frame
```

出图工具用 gptimg（gpt-image-2），修人脸必须 -f high，-q 不给默认 low 会糊。

## 反例

❌ 只出正面 LOCKED 脸 → 侧拍镜头下颌线和轮廓被模型自由发挥，观众觉得不是同一个人
❌ 用 `--first 关键帧 --ref 正面 --ref 侧面` → 直接 InvalidParameter，5 镜全挂
✅ 三档齐全 + 侧拍镜走 `--ref 关键帧 --ref 正面 --ref 45 --ref 正侧 --ratio 9:16`
