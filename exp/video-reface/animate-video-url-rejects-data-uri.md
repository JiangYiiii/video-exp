---
title: animate 的 video_url 不吃 data URI，本地视频必须走 DashScope 临时 OSS；image_url 不受影响
skill: video-reface
schema: video-exp/1
tags: 换人, reface, animate, data URI, OSS上传, InvalidVideo.FileFormat, bvid swap, 端点差异
severity: medium
date: 2026-11-23
---

## 场景

给 `bvid` 接百炼 `wan2.2-animate-mix`。沿用 `bvid gen` 的做法，把本地 mp4
转成 base64 data URI 塞进 `input.video_url`，提交后立刻失败。

## 结论

**`animate` 的 `video_url` 不接受 data URI，哪怕文件就是标准 mp4。**
官方文档只说要公网 HTTP/HTTPS URL，没说本地文件怎么办。

解法是走 DashScope 临时 OSS（文件 48 小时后自动清理）：

1. `GET /api/v1/uploads?action=getPolicy&model=<model>` 拿签名，返回
   `upload_host` / `upload_dir` / `policy` / `signature` / `oss_access_key_id` 等
2. multipart POST 到 `upload_host`，字段：`OSSAccessKeyId` `policy` `Signature`
   `key=<upload_dir>/<文件名>` `x-oss-object-acl` `x-oss-forbid-overwrite`
   `success_action_status=200` `file`
3. 用 `oss://<key>` 当 `video_url` 提交，并带请求头
   **`X-DashScope-OssResourceResolve: enable`**

**`image_url` 不受影响**，人物图照样走 data URI。只有视频有这个限制。

`bvid swap` 已自动处理，传本地路径即可。注意这和 `bailian-video` 第 5 节
「本地文件自动转 data URI，不用先传 OSS」冲突——那句话对 `bvid gen` 成立，
对 `bvid swap` 的**视频**不成立，已在该 skill 标注。

顺带两条同源的接口差异：

- **端点不同**：`gen` 走 `/services/aigc/video-generation/video-synthesis`，
  `swap` 走 `/services/aigc/**image2video**/video-synthesis`
- **返回结构不同**：`gen` 的视频在 `output.results[0].url` 或 `output.video_url`，
  **`swap` 的在 `output.results.video_url`**（results 是对象不是数组）

## 证据

同一个本地文件 `fd928416.mp4`（`file` 确认是标准 MP4，1.9MB，544×720，5.0s）：

- 走 data URI → `task_status: FAILED`，
  `code: InvalidVideo.FileFormat`，`message: Invalid video type. Only mp4/mov/avi is supported.`
  **失败不计费**
- 同一文件走 OSS 上传 + `X-DashScope-OssResourceResolve: enable` → `SUCCEEDED`，正常出片

`getPolicy` 实测返回 `max_file_size_mb: 1024`、`expire_in_seconds: 300`，
所以签名要现拿现用，别缓存。

## 反例

- ❌ 看到 `Only mp4/mov/avi is supported` 就去转码——文件格式本来就是对的，问题在传输方式
- ❌ 把 `bvid gen` 的 data URI 经验直接套到 `swap`——两条命令的端点、入参、返回结构都不同
- ❌ 用 `gen` 的 `o.get("video_url") or results[0].url` 去取 `swap` 的结果——取不到，
      `swap` 的 results 是 dict
