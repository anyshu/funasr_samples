# FunASR Samples

FunASR 实时语音听写服务的示例客户端代码集合。

## 来源

本仓库代码来源于 FunASR 官方文档：
- **下载地址**: https://isv-data.oss-cn-hangzhou.aliyuncs.com/ics/MaaS/ASR/sample/funasr_samples.tar.gz
- **文档**: [FunASR 实时语音听写服务开发指南](https://github.com/modelscope/FunASR/blob/c4ac64fd5d24bb3fc8ccc441d36a07c83c8b9015/runtime/docs/SDK_advanced_guide_online_zh.md)

## 快速开始

```bash
# 下载示例代码
wget https://isv-data.oss-cn-hangzhou.aliyuncs.com/ics/MaaS/ASR/sample/funasr_samples.tar.gz

# 解压
tar -zxvf funasr_samples.tar.gz
```

## 目录结构

```
funasr_samples/
├── audio/          # 测试音频文件
├── python/         # Python WebSocket 客户端
├── cpp/            # C++ WebSocket 客户端
├── java/           # Java WebSocket 客户端
└── html/           # 浏览器端演示
```

## 支持的模式

| 模式 | 说明 |
|------|------|
| `offline` | 非实时，一句话识别 |
| `online` | 实时流式识别 |
| `2pass` | 实时流式 + 离线纠错（兼顾低延迟和高准确率） |

## WebSocket 返回数据结构

### 1. online 模式

```json
{
  "is_final": false,
  "mode": "2pass-zonline",
  "text": "为这不是",
  "wav_name": "h5"
}
```

### 2. 2pass-offline 模式

```json
{
  "is_final": false,
  "mode": "2pass-offline",
  "stamp_sents": [
    {
      "end": -1,
      "punc": "，",
      "start": -1,
      "text_seg": "",
      "ts_list": []
    },
    {
      "end": 86390,
      "punc": "，",
      "start": 84470,
      "text_seg": "那 么 就 不 耽 误 大 家 时 间 了",
      "ts_list": [[84470,84610],[84610,84730],[84730,84910],[84910,85050],[85050,85210],[85210,85330],[85330,85450],[85450,85630],[85630,85750],[85750,85930],[85930,86390]]
    },
    {
      "end": 87730,
      "punc": "，",
      "start": 86390,
      "text_seg": "咱 们 就 地 解 散",
      "ts_list": [[86390,86570],[86570,86710],[86710,86870],[86870,87010],[87010,87170],[87170,87730]]
    },
    {
      "end": 89930,
      "punc": "，",
      "start": 87730,
      "text_seg": "大 家 不 要 紧 张 啊",
      "ts_list": [[87730,87950],[87950,88210],[88210,88310],[88310,88570],[88570,88710],[88710,89310],[89490,89930]]
    },
    {
      "end": 92230,
      "punc": "。",
      "start": 89930,
      "text_seg": "真 正 的 麻 烦",
      "ts_list": [[89930,90210],[90210,90510],[90510,91110],[91270,91650],[91650,92230]]
    },
    {
      "end": 95645,
      "punc": "",
      "start": 92230,
      "text_seg": "真 正 的 事 情 还 要 过 几 天 开 始 呢",
      "ts_list": [[92230,92490],[92490,92730],[92730,92930],[92930,93070],[93070,93670],[93910,94090],[94090,94290],[94290,94530],[94530,94690],[94690,94890],[94890,95050],[95050,95290],[95290,95645]]
    }
  ],
  "text": "，那么就不耽误大家时间了，咱们就地解散，大家不要紧张啊，真正的麻烦。真正的事情还要过几天开始呢",
  "timestamp": "[[84470,84610],[84610,84730],[84730,84910],[84910,85050],[85050,85210],[85210,85330],[85330,85450],[85450,85630],[85630,85750],[85750,85930],[85930,86390],[86390,86570],[86570,86710],[86710,86870],[86870,87010],[87010,87170],[87170,87730],[87730,87950],[87950,88210],[88210,88310],[88310,88570],[88570,88710],[88710,89310],[89490,89930],[89930,90210],[90210,90510],[90510,91110],[91270,91650],[91650,92230],[92230,92490],[92490,92730],[92730,92930],[92930,93070],[93070,93670],[93910,94090],[94090,94290],[94290,94530],[94530,94690],[94690,94890],[94890,95050],[95050,95290],[95290,95645]]",
  "wav_name": "h5"
}
```

**字段说明：**
- `is_final`: 是否是最终结果
- `mode`: 识别模式
- `text`: 识别文本
- `wav_name`: 音频标识
- `stamp_sents`: 带时间戳的句子列表（仅 2pass-offline）
  - `start`/`end`: 句子起止时间（毫秒）
  - `text_seg`: 分词后的文本
  - `punc`: 标点符号
  - `ts_list`: 每个字的时间戳列表 `[[start, end], ...]`
- `timestamp`: 完整的字级时间戳（仅 2pass-offline）

## 客户端使用

### Python

```bash
pip3 install websockets
python python/funasr_wss_client.py --host 127.0.0.1 --port 10095 --mode 2pass
```

### C++

```bash
cd cpp/websocket_client
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=release ..
make
./bin/funasr-wss-client --server-ip 127.0.0.1 --port 10095 --wav-path ../audio/asr_example.wav
```

### Java

```bash
cd java
make downjar && make buildwebsocket && make runclient
```

### HTML (浏览器)

直接在浏览器中打开 `html/static/index.html`，支持麦克风实时录音和文件上传。

## 常用参数

| 参数 | 说明 |
|------|------|
| `--host/--server-ip` | FunASR 服务器地址 |
| `--port` | 服务端口 (默认: 10095) |
| `--mode` | 识别模式: offline/online/2pass |
| `--chunk_size` | 流式分块配置，如 "5,10,5" |
| `--hotword` | 热词文件路径 |
| `--ssl` | 是否启用 SSL (1=启用) |

## 相关链接

- [FunASR GitHub](https://github.com/modelscope/FunASR)
- [FunASR 文档](https://github.com/modelscope/FunASR/tree/main/runtime/docs)
