# 南瓜乔哥的悲惨生活

一个用 [WebGAL](https://github.com/OpenWebGAL/WebGAL) 引擎制作的网页端视觉小说，讲述 39 岁的乔哥一个人生活的日常与心声。

## 🎮 在线游玩 / 本地运行

### 方式一：任意静态服务器

```bash
# Python
python -m http.server 8437

# 或 Node.js
npx serve .
```

然后浏览器打开 `http://127.0.0.1:8437/`

> ⚠️ 必须通过 HTTP 访问，直接双击 index.html 无法加载剧情资源。

## 📖 游戏内容

| 章节 | 内容 |
|---|---|
| 序章 | 一个人生活 |
| 第一章 | 一个人的晚饭（做饭） |
| 第二章 | 三十九岁 |
| 第三章 | 希望 |
| 尾声 | 结局选择（可回看原始视频） |

- 全程配音：从原视频逐句切分的 33 段原声（`game/vocal/story/`）
- 结尾可完整回看 5 分半的原始视频

## 🛠 自行修改

### 改台词

台词都在 `game/scene/` 下，用记事本即可编辑：

```
ch1_alone.txt    序章 · 一个人生活
ch2_cooking.txt  第一章 · 一个人的晚饭
ch3_39.txt       第二章 · 三十九岁
ch4_hope.txt     第三章 · 希望
ch5_end.txt      尾声 + 结局选择
```

一行一句，格式：

```
这里写你想说的话。 -story/s01.mp3;
```

- `-story/s01.mp3` 是这句的配音，不需要就删掉（保留行尾分号 `;`）
- 改完保存，刷新浏览器即生效

### 改标题界面样式

- 按钮/落地页样式：`game/userStyleSheet.css`（磨砂毛玻璃按钮效果也在这里）
- 标题背景图（含烧录的标题字）：`game/background/title_bg_v2.jpg`
- 网页标签图标：`icons/favicon.ico`

## 📂 目录结构

```
├── index.html              入口页（含落地页、按钮磨砂样式）
├── assets/                 WebGAL 引擎本体（请勿改动）
├── game/
│   ├── config.txt          游戏名、标题图等全局配置
│   ├── scene/              ★ 剧情脚本（改台词改这里）
│   ├── background/         背景图
│   ├── vocal/story/        ★ 剧情配音（s01~s33）
│   ├── video/intro.mp4     原始视频
│   └── userStyleSheet.css  ★ 全局自定义样式
└── _server.py              本地开发服务器（禁缓存）
```

## 📄 版权说明

- 引擎：[WebGAL](https://github.com/OpenWebGAL/WebGAL)（MIT License）
- 剧情/素材：仅供学习交流

---
*Powered by WebGAL Framework*
