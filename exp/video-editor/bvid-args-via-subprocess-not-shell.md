---
title: 批量提交 bvid 要用 subprocess 传参数列表，shell 变量展开会被 argparse 顶层吞掉
skill: video-editor
schema: video-exp/1
tags: bvid, 并行提交, shell分词, argparse, 批量生成, 踩坑
severity: medium
date: 2026-09-09
---

## 场景

9 镜并行提交万相生成。为了让每镜的参考图组合可配置，我把 `--ref a --ref b --ref c`
这样的一串参数存进 shell 变量，再在命令里不加引号展开：

```bash
while IFS='|' read -r id dur media; do
  ( bvid gen --model wan3.0-video ... --prompt "$(cat p.txt)" $media --out out/s$id ) &
done < plan.txt
```

同样的 `--ref` 写法在单条命令里手写是能跑通的，说明不是参数本身不合法。

## 结论

**批量/并行提交这类 CLI，不要用 shell 变量拼参数，直接用 Python `subprocess` 传参数列表。**

```python
cmd=['bvid','gen','--model','wan3.0-video','--resolution','480P','--ratio','9:16',
     '--duration',str(dur),'--prompt',prompt,'--negative',neg,'--out',f'out/s{i}','--poll','900']
for x in refs: cmd+=['--ref',x]
subprocess.run(cmd,capture_output=True,text=True)
```

这样做还顺手解决三件事：

1. 多行 prompt 和含中文逗号的 negative 不需要考虑任何转义
2. 用 `ThreadPoolExecutor(max_workers=9)` 并行，比 shell 的 `&` + `wait` 好控制
3. 每镜的 `task_id` / `task_status` 直接从 stdout 正则抽出来落盘成 `log/tasks.json`，
   并行跑完不会分不清哪个文件对应哪镜（这是万相经验里明确要求落盘的）

另外 `--out` 传的是**目录**不是文件名：`--out out/s7` 会生成
`out/s7/<task_id>.mp4`，拼接前要先 `find out/sN -name '*.mp4'` 再统一重命名。

## 证据

8 镜（第 9 镜因 `read` 读最后一行无换行被整行跳过）全部在客户端参数解析阶段失败，
报的是 bvid 的**顶层** usage 而不是 `gen` 子命令的 usage：

```
usage: bvid [-h] {gen,get,price} ...
bvid: error: unrecognized arguments: --ref stills/xxx.png --ref stills/yyy.png
```

失败发生在提交之前，**未消耗任何额度**。改用 Python subprocess + ThreadPoolExecutor
重新提交，9 镜全部 SUCCEEDED，约 5 分钟走完。

另外 `--out out/shot02.mp4` 实测生成的是**同名目录** `out/shot02.mp4/`，
里面放 `<task_id>.mp4`，直接对它跑 ffprobe 会报 `Is a directory`。

## 反例

❌ `$media` 这类 shell 变量展开传多个重复参数 → argparse 顶层 unrecognized arguments
✅ Python subprocess 传 list
❌ 以为 `--out out/shot02.mp4` 会得到一个文件 → 实际得到同名目录
❌ `while read` 读计划文件时最后一行没有换行符 → 那一镜被静默跳过，
   并行输出里少一条却不报错，很难发现
