# video-* 流水线标准输入输出契约 v1

五个 skill 串成流水线，**每一棒交接都走固定 schema**，不许口头传递。

```
video-writer → video-costume → video-director → video-editor → video-audience
   script.json   characters.json   shots.json      cut.json      review.json
```

## 通用规则

1. 每个阶段产出**一对文件**：
   - `<name>.json` —— 机读，给下一棒消费，字段固定
   - `<NAME>.md` —— 人读，给用户审阅，写"为什么"
   JSON 是契约，Markdown 是阐述。两者内容必须一致。
2. 每个 JSON 顶层必带 `schema` 字段，值形如 `video-script/1`。**版本号不兼容变更时递增。**
3. 所有 ID 是字符串，全局唯一，跨阶段引用靠 ID：
   角色 `character.id`、场景 `scene.id`、台词 `line.id`、镜头 `shot.id`。
4. 所有路径都是**项目根的相对路径**。
5. 下一棒开工前必须 `video-exp validate <上一棒的 json>`，校验不过就退回，不要硬着头皮往下做。
6. 缺字段一律显式写 `null` 或空数组，不要省略键。

---

## 1. `script.json` — video-writer 产出

```json
{
  "schema": "video-script/1",
  "title": "有关系",
  "logline": "一句话故事：谁想要什么、谁在阻拦、结果如何",
  "spec": {
    "platform": "douyin",
    "form": "single",
    "aspect": "9:16",
    "language": "zh",
    "episodes": 1,
    "target_duration_s": 23
  },
  "genre": "虐恋",
  "emotion": { "primary": "虐", "secondary": "张力" },
  "audience": { "gender": "female", "age": "18-30" },
  "tone_ref": "韩剧电影感",
  "characters": [
    {
      "id": "hero",
      "role": "male_lead",
      "age": "25-26",
      "height_cm": 185,
      "temperament": "矜贵别扭、压着一股火",
      "face_direction": "眉眼清冷，单眼皮偏内双，鼻梁挺直，薄唇，脸型偏窄",
      "hair": "银白色偏月白的蓬松微乱短发，额前刘海略遮眉",
      "costume": "黑色修身西装外套 + 白衬衫领口解开两颗扣 + 黑色细领带松垮垂在胸前",
      "real_ref": null,
      "face_baseline_note": null
    }
  ],
  "height_relation": "男主比女主高约18厘米，对峙时女主略微抬头，但不是悬殊萌差",
  "scenes": [
    {
      "id": "alley",
      "location": "深夜首尔老城区窄巷，两侧红砖墙",
      "time": "深夜",
      "light": "暖黄路灯从侧上方打下形成轮廓光，巷口便利店冷白灯光在背景虚化成光斑",
      "ground": "刚下过雨微微反光",
      "style": "韩剧电影感，浅景深，冷暖对冲色调，轻微胶片颗粒"
    }
  ],
  "beats": [
    { "beat": "hook",   "content": "男主质问" },
    { "beat": "escalate","content": "女主冷淡回击" },
    { "beat": "turn",   "content": "男主转身走开" },
    { "beat": "climax", "content": "折返按墙强吻" },
    { "beat": "close",  "content": "「有关系。」回扣" }
  ],
  "lines": [
    {
      "id": "L1",
      "character": "hero",
      "text": "你就这么喜欢他？",
      "delivery": "气声+咬字，「就这么」咬重，尾音下压不上扬，是指控不是发问"
    }
  ],
  "estimate": { "shots": 6, "duration_s": 23 },
  "constraints": {
    "scene_count": 1,
    "character_count": 2,
    "costume_changes": 0,
    "has_combat_or_props": false,
    "max_line_length": 8
  },
  "forbidden": []
}
```

`beat` 枚举：`hook` / `escalate` / `turn` / `climax` / `close`
`role` 枚举：`male_lead` / `female_lead` / `antagonist` / `supporting`

**校验硬门槛**（不过不许进下一棒）：
`scene_count ≤ 3`、`character_count ≤ 3`、`costume_changes == 0`、
`has_combat_or_props == false`、每条 `lines[].text` ≤ 15 字、`lines[].delivery` 非空。

---

## 2. `characters.json` — video-costume 产出

```json
{
  "schema": "video-characters/1",
  "characters": [
    {
      "id": "heroine",
      "locked_face": "stills/heroine_LOCKED_face.png",
      "angles": {
        "front": "stills/heroine_LOCKED_face.png",
        "q45":   "stills/angles/heroine_45.png",
        "side":  "stills/angles/heroine_side.png"
      },
      "scene_looks": { "alley": "stills/heroine_LOCKED_alley.png" },
      "costume_hard": "女主始终穿米色宽松针织开衫、内搭白色衬衫、深灰色及膝裙，全程不换装、不露肩、衣着完整",
      "locked_at": "2026-09-07",
      "locked_by_user_quote": "这张是最像的，就是用这张吧，其他的都不要了",
      "qc": {
        "no_beautify": true, "bone_structure_match": true, "normal_eyes": true,
        "skin_texture": true, "costume_match": true, "hands_ok": true,
        "no_text": true, "not_anime": true, "same_person_across_angles": true
      }
    }
  ],
  "height_relation": "男主185cm比女主167cm高约18厘米，体型接近，不是悬殊身高差",
  "negative": "红色眼睛，发光的眼睛，异色瞳，特效妆，动漫感，动漫发色，画面出现任何文字、字幕、水印、logo，脸部特征互换，换脸，五官变形，瘦脸，尖下巴，放大眼睛，网红脸，吊带，露肩，无袖上衣，中途换装，多余手指，手部结构错误，非真人质感"
}
```

**校验硬门槛**：每个角色 `angles.front/q45/side` 三档**必须齐全且文件存在**；
`costume_hard` 非空；`qc` 全部为 `true`。

---

## 3. `shots.json` — video-director 产出

```json
{
  "schema": "video-shots/1",
  "aspect": "9:16",
  "total_duration_s": 23,
  "anchor": {
    "consistency": "画面中的人物、服装、场景、光线全部严格延续首帧……",
    "performance": "必须是真人演员的自然表演，真实电影摄影质感……眼睛是正常的深棕色人眼。"
  },
  "negative": "……统一负面词……",
  "shots": [
    {
      "id": "1",
      "scene": "alley",
      "duration_s": 4,
      "shot_size": "medium_close",
      "angle": "low",
      "camera_move": "slow_push_in",
      "handheld": true,
      "tension": 5,
      "characters_in_frame": ["hero"],
      "is_profile": false,
      "keyframe": "stills/keyframes/916/k1.png",
      "line_ids": ["L1"],
      "action": "……一个动作，写成可拍摄的生理反应……",
      "wardrobe_hard": null,
      "mood": "隐忍到快绷断的愤怒和酸涩",
      "prompt": "……最终拼好的完整 prompt……"
    }
  ],
  "keyframe_requests": [
    { "id": "k6", "for_shot": "35", "content": "男主转身折返，抬眼看向她的方向", "characters": ["hero"] }
  ],
  "post": { "subtitles": "后期加", "bgm": "后期加，虐向氛围" }
}
```

枚举：
- `shot_size`：`extreme_close` / `close` / `medium_close` / `medium` / `medium_deep` / `wide`
- `angle`：`low` / `eye` / `high` / `profile` / `back`
- `camera_move`：`static` / `slow_push_in` / `slow_pull_back` / `handheld_push` / `rack_focus` / `defocus_fade`
- `tension`：1–8 整数，用来画节奏曲线

**校验硬门槛**：每镜 `duration_s ≤ 8`；`action` 里只有一个主要动作；
`is_profile == true` 的镜头必须在 `characters_in_frame` 里的角色都有 `angles.side`；
所有 `line_ids` 能在 `script.json` 里找到；相邻两镜 `camera_move` 不得完全相同
（运镜对位）；`post.subtitles` 必须是"后期加"。

---

## 4. `cut.json` — video-editor 产出

```json
{
  "schema": "video-cut/1",
  "engine": "bailian/wan3.0-video",
  "resolution": "1080P",
  "mode": "reference",
  "clips": [
    {
      "shot_id": "1",
      "task_id": "100889d6-d0be-498d-8222-a7b0d2d4e8c0",
      "file": "out/cut_v3/01_shot1.mp4",
      "width": 1080, "height": 1920,
      "duration_s": 4.0,
      "has_audio": true,
      "status": "succeeded",
      "attempt": 1
    }
  ],
  "final": "out/cut_v3/有关系_v3_1080p.mp4",
  "total_duration_s": 23.2,
  "cost": { "free_quota_used": 7, "credits_used": 0, "cny": 0 },
  "post_todo": ["字幕", "BGM"]
}
```

枚举：`mode` = `first_frame`（锁构图）/ `reference`（锁人脸，多角度参考）
`status` = `succeeded` / `failed` / `pending`

**校验硬门槛**：`clips` 覆盖 `shots.json` 里所有镜号，无缺；所有 `status == succeeded`；
所有 clip 的 `width/height` 一致；`duration_s` 与分镜表的期望值误差 ≤ 0.5s。

---

## 5. `review.json` — video-audience 产出

```json
{
  "schema": "video-review/1",
  "target": "out/cut_v3/有关系_v3_1080p.mp4",
  "frames_dir": "out/review/frames",
  "checks": {
    "continuity": "pass",
    "face_consistency": "warn",
    "costume_drift": "pass",
    "hands": "pass",
    "text_garbage": "pass",
    "aspect_duration": "pass",
    "audio_sync": "pass"
  },
  "findings": [
    {
      "shot_id": "4",
      "category": "face_consistency",
      "severity": "medium",
      "summary": "女主侧脸下颌线比定妆照偏宽",
      "evidence": ["out/review/frames/shot4_t2.0.png"],
      "compare_to": "stills/angles/heroine_side.png",
      "suggest": "retake"
    }
  ],
  "verdict": "retake",
  "retake_shots": ["4"]
}
```

枚举：
- `checks.*`：`pass` / `warn` / `fail`
- `category`：`continuity` / `face_consistency` / `costume_drift` / `hands` /
  `text_garbage` / `aspect_duration` / `audio_sync` / `performance`
- `severity`：`low` / `medium` / `high`
- `suggest`：`accept` / `retake` / `repair_in_post`
- `verdict`：`accept` / `retake` / `reshoot_keyframe`

**校验硬门槛**：`findings` 里每条都要有 `evidence` 抽帧路径；
`verdict == "retake"` 时 `retake_shots` 非空。

---

## 6. 经验条目格式（所有 skill 统一）

`exp/<skill>/<slug>.md`：

```markdown
---
title: 一句话标题
skill: video-director
schema: video-exp/1
tags: 分镜, 连贯性, 硬切
severity: high
date: 2026-09-08
---

## 场景
什么任务、什么输入下遇到的。

## 结论
可直接执行的口径。不是感想，是下次照做的规则。

## 证据
实测数据、原始报错文本、生成参数。要能被 grep 命中。

## 反例
错误做法及其后果。比正面结论更容易在下次被识别出来。
```

frontmatter 必填：`title` `skill` `schema` `tags` `severity` `date`
`severity`：`high`（会导致返工/资损）/ `medium`（影响质量）/ `low`（效率优化）

四个二级标题**必须全部存在且非空**，`video-exp save` 会校验。
