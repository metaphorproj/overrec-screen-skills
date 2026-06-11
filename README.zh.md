# overrec-screen

一个 Claude Code 技能，让 AI 助手能够通过 [OverRec](https://apps.microsoft.com/detail/9pdn41kpj3hg) 控制屏幕覆盖层、截取屏幕截图，以及将应用程序窗口精确定位到指定位置。

## 系统要求

- Windows 10/11（或可访问 Windows 可执行文件的 WSL 环境）
- 已安装 [OverRec](https://apps.microsoft.com/detail/9pdn41kpj3hg) 并添加到 PATH

## 功能说明

当用户提出以下需求时触发此技能：

- 截取屏幕指定区域（保存到文件和/或剪贴板）
- 将屏幕指定区域录制为动态 GIF 或 H.264 MP4（同步或后台运行）
- 在屏幕上绘制高亮/覆盖矩形框（支持颜色名称或 `#RRGGBB` 十六进制颜色）
- 列出所有已连接显示器的分辨率、位置和缩放比例
- 通过标题关键词查找正在运行的窗口（可选显示位置和尺寸详情）
- 将窗口精确移动并调整到指定位置和尺寸（Snap）
- 并排平铺或排列多个窗口
- 定时捕获指定屏幕区域（监控/录制帧）

## 示例提示语

```
截取屏幕左上角 800x600 区域的截图
在位置 100,100 绘制一个红色矩形框，大小 640x480，持续 3 秒
用 #FF4400 颜色高亮工具栏区域
找到 Chrome 窗口并将其对齐到屏幕左半边
在显示器 1 上并排平铺记事本和资源管理器
列出所有可见窗口
显示 Chrome 当前的位置和尺寸
每 5 秒监控屏幕右上角区域
将左上角 1280x720 区域录制为 GIF，时长 10 秒
将显示器 1 以 30fps 录制为 MP4，时长 15 秒，在后台运行
```

## CLI 命令速查

| 命令 | 功能 |
|---|---|
| `cli monitors [--all]` | 列出显示器（简洁模式或完整详情） |
| `cli window [--all] [关键词...]` | 按标题查找窗口；`--all` 显示位置和尺寸 |
| `cli snap --windowid ID --location X,Y --size WxH` | 移动并调整窗口大小 |
| `cli draw --location X,Y --size WxH [--color] [--timeout]` | 绘制覆盖矩形框 |
| `cli screenshot --location X,Y --size WxH [--output] [--no-clipboard]` | 截取屏幕区域 |
| `cli record --location X,Y --size WxH --output 文件.gif\|文件.mp4 --timeout 秒 [--fps] [--ffmpeg] [--async]` | 将屏幕区域录制为 GIF/MP4 |

### 命令详情

#### `monitors` — 列出显示器
```
overrec cli monitors [--all]
```
不带 `--all` 时输出简洁的 ID/分辨率列表；带 `--all` 输出完整信息：分辨率、绝对位置、缩放比例、刷新率、旋转角度、是否为主显示器。

#### `window` — 查找窗口
```
overrec cli window [--all] [<关键词...>]
```
列出标题包含所有指定关键词的可见窗口（不区分大小写）。不指定关键词则列出所有可见窗口。

- 默认：简洁的 ID/标题列表
- `--all`：同时显示所在显示器编号、位置和尺寸（最大化/最小化显示 `max`/`min`）

#### `snap` — 精确定位窗口
```
overrec cli snap --windowid ID --location X,Y --size WxH [--monitor ID]
```
将指定窗口移动并调整到精确位置和尺寸。DWM 阴影边距自动修正，可见边框精确落在请求的坐标位置。

#### `draw` — 绘制覆盖矩形
```
overrec cli draw --location X,Y --size WxH [--color 颜色] [--timeout 秒] [--monitor ID]
```
颜色支持：`red`、`green`、`blue`、`yellow`、`white`、`black` 或 `#RRGGBB` 十六进制格式，默认为蓝色。

#### `screenshot` — 截图
```
overrec cli screenshot --location X,Y --size WxH [--output 文件.png] [--no-clipboard] [--monitor ID]
```
默认将图像复制到剪贴板。使用 `--output` 保存为文件，使用 `--no-clipboard` 跳过剪贴板复制。

#### `record` — 录制屏幕区域为 GIF 或 MP4
```
overrec cli record --location X,Y --size WxH --output 文件.gif|文件.mp4 --timeout 秒 [--fps N] [--monitor ID] [--ffmpeg 路径] [--async]
```
`--timeout` 为必填项。`.gif` 使用内置编码器；`.mp4` 需要 FFmpeg（默认从 PATH 中调用 `ffmpeg`，也可通过 `--ffmpeg 路径` 指定）。`--fps` 范围为 1-240（默认 10）。在 Windows 上，MP4 录制使用 DXGI Desktop Duplication，录制区域必须完全位于一个未旋转的显示器内。不带 `--async` 时命令会阻塞直到超时；带 `--async` 时会启动一个分离的后台进程并立即返回。

## 典型工作流

```bash
# 1. 查看显示器布局
overrec cli monitors --all

# 2. 查找目标窗口
overrec cli window chrome

# 3. 将窗口对齐到显示器 0 左半边
overrec cli snap --windowid 657846 --location 0,0 --size 960x1080

# 4. 绘制参考矩形验证区域（2 秒后自动关闭）
overrec cli draw --location 0,0 --size 960x1080 --timeout 2

# 5. 截取该区域
overrec cli screenshot --location 0,0 --size 960x1080 --output left-half.png

# 6. 或将该区域录制为短视频
overrec cli record --location 0,0 --size 960x1080 --output left-half.gif --timeout 10
```

技能定义文件位于 [`skills/overrec-screen/SKILL.md`](skills/overrec-screen/SKILL.md)。
