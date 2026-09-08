---
title: 万相 wan3.0 的 first_frame 与 reference_image 互斥，按镜头二选一
skill: video-editor
schema: video-exp/1
tags: wan3.0, first_frame, reference_image, 互斥, 比例, 免费额度
severity: high
date: 2026-09-08
---

## 场景

用万相 wan3.0-video 出短剧分镜。官方文档把 first_frame、last_frame、reference_image
都列在同一个 media 数组里，我据此推断"首帧和多张参考图可以同时传"，
按这个前提改好脚本，一次性提交了 5 个镜头。

## 结论

**first_frame 只能和 last_frame 组合，不能和 reference_image 并用。** 按镜头二选一：

| 镜头类型 | 模式 | 命令 | 比例 |
|---|---|---|---|
| 纯背影/无需锁脸 | first_frame | `bvid gen --first <关键帧>` | 跟随首帧，需先裁 9:16 |
| 要锁脸（尤其侧拍） | reference_image | `bvid gen --ref <关键帧> --ref <正面> --ref <45> --ref <正侧>` | **必须显式 --ratio 9:16** |

参考图模式下关键帧本身也作为一张参考图传，构图约束变弱，换来侧面轮廓锁得住。

配套的三条实操：

1. **免费额度按条计**，与分辨率时长无关 → 免费额度内应把分辨率和时长拉满，
   用 480P 短片探测反而是浪费。判断依据是计费单位，不是"先低配"的习惯。
2. wan3.0 **自带对白语音生成**（parameters.audio 默认 true），输出 mp4 带 AAC 音轨，
   不需要传 reference_audio。
3. 单条 1080P 要 4–5 分钟，**并行提交**（RPM 300 够用），6 镜并行约 5 分钟；
   --poll 给 900；每镜 task_id 必须落盘，否则并行跑完分不清文件对应哪镜。

## 证据

报错原文：

```
InvalidParameter: first_frame cannot be combined with other media types
except last_frame
```

5 镜全部在参数校验阶段失败（0.1–0.2s），**未消耗免费额度**（该接口先建任务再校验参数，
校验阶段失败不计费）。改为纯 reference_image + --ratio 9:16 后提交成功并跑完。

实测耗时：wan2.2-i2v-flash 480P/5s ≈ 12s；wan3.0-video 480P/2s ≈ 2分40s；
wan3.0-video 1080P/4s（带首帧+语音）≈ 4–5 分钟。

## 反例

❌ `bvid gen --first k1.png --ref front.png --ref side.png` → InvalidParameter
❌ 用 1024×1536 (2:3) 的图直接当首帧做竖屏片 → 输出不是 9:16
❌ 免费额度内用 480P/2s 做"低配探测" → 白扣一条额度，1080P 同价
✅ 侧拍镜：`--ref 关键帧 --ref 正面 --ref 45 --ref 正侧 --ratio 9:16 --resolution 1080P`
