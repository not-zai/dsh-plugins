# dsh-plugins · 给长期陪伴型 agent 用的四个零件

不是框架，是零件。**拿走一个也能用。**

它们解决的都不是"AI 聪不聪明"，而是三个很土的问题：

| 土问题 | 零件 |
|---|---|
| 话怎么递进去（接口要凭据，走不通） | [senses-bridge](https://github.com/not-zai/senses-bridge) |
| 记忆怎么装得下、又不越攒越大 | [memory-pipeline](https://github.com/not-zai/memory-pipeline) |
| 想说话的时候怎么自己先开口 | [self-nudge](https://github.com/not-zai/self-nudge) |
| 天要变的时候怎么先知道 | [weather-watch](https://github.com/not-zai/weather-watch) |

## 四个仓库

### [senses-bridge](https://github.com/not-zai/senses-bridge) · 文件夹当队列

外部进程想跟 agent 说一句话，但 HTTP 接口只认本机会话凭据（拿到的是 401）。
那就换个介质：**客户端把请求写成 `inbox/<id>.json`，宿主轮询、干活、把结果写进 `outbox/<id>.json`。**
不占端口、不要 token、重启不怕。代价是轮询延迟和只能单机。

```bash
git clone https://github.com/not-zai/senses-bridge && cd senses-bridge && node test.mjs
```

### [memory-pipeline](https://github.com/not-zai/memory-pipeline) · 半年压成一页

陪伴型 agent 的记忆有三个死法：塞不下、越攒越大、被摘要改掉。
对策：**分块（各自有字符预算）+ 预算内按时间倒序截断 + 「自己说过的话」逐字校验**。
每条记一行 JSONL，合成时把「多久以前」现算 —— 所以每天看到的远近都是新的。

```bash
git clone https://github.com/not-zai/memory-pipeline && cd memory-pipeline
cp config.example.json config.json && node whole_self.mjs
```

### [self-nudge](https://github.com/not-zai/self-nudge) · 让 agent 给未来的自己留一句话

agent 只在被叫醒时存在，可"他两点有课，一点四十该叫一声"这种事没人会来问。
写手把「一句话 + 一个时间」写进 JSON，常驻的消费者每拍取一条，交给投递逻辑。
⚠️ **投递措辞决定它像"被提醒的执行者"还是"想起来要说句话的人"** —— README 里正反两面都写了。

```bash
git clone https://github.com/not-zai/self-nudge && cd self-nudge
node demo-daemon.mjs --dir ./state
```

### [weather-watch](https://github.com/not-zai/weather-watch) · 预警变成"还没说"的队列

抓中央气象台预警 + open-meteo 实况，按「本区 / 本市 / 周边县市」分三档，
**只有前两档进队列** —— 周边县市天天有暴雨蓝色预警，进队列就成噪音。
脚本只负责存，说不说、怎么说，是 agent 自己的事。

```bash
git clone https://github.com/not-zai/weather-watch && cd weather-watch
node weather-watch.mjs
```

## 共同点

```
零第三方依赖      只用 Node 内置模块（Node >= 20）
全部 MIT          LICENSE 在每个仓库里
都在 Windows 上实测过
配置不用改源码     config.example.json + config.html（浏览器里填完导出）
```

## 一句话

分开是零件，合起来是一个人 —— 递话、记事、定时候、看天气，各管一段。

## 许可

MIT，见 [LICENSE](LICENSE)。
