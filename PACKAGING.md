# 《南瓜乔哥的悲惨生活》打包指南

> 你的游戏本体就是**一个纯静态网页**。所谓"打包"，Windows 就是给网页套个浏览器壳（Electron），安卓就是把网页塞进官方模板的 assets 里。

---

## 第零步：编译输出 = 静态网页包

WebGAL 网页版**没有编译过程**，你的项目根目录本身就是成品。已为你打好：

📦 **`桌面\南瓜乔哥的悲惨生活-网页包.zip`**（141MB，217 个文件）

里面是：`index.html + assets/(引擎) + game/(剧情素材+磨砂样式)`，这就是"编译输出"。
解压后用任意静态服务器（`python -m http.server 8437`）即可运行验证。

---

# 第一部分：Windows 打包（Electron → 双击 exe）

### 1. 准备网页运行模板

1. 下载 Electron 官方快速起步模板（quick-start），或直接用下面第 2 步的最小结构
2. 把你解压后的**整个静态网页内容**（index.html、assets、game、icons 等）放进模板目录

### 2. 最小工程结构

```
webgal-app/
├── package.json
├── main.js            ← 主进程（见下）
└── www/               ← ★ 你的整套静态网页原样放这里
    ├── index.html
    ├── assets/
    ├── game/
    └── icons/
```

**package.json**

```json
{
  "name": "nanhua-qiaoge-webgal",
  "productName": "南瓜乔哥的悲惨生活",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": { "start": "electron .", "dist": "electron-builder --win portable" },
  "devDependencies": {
    "electron": "^31.0.0",
    "electron-builder": "^24.13.3"
  },
  "build": {
    "appId": "com.carlown.nanhuaqiaoge",
    "productName": "南瓜乔哥的悲惨生活",
    "win": { "icon": "icon.ico" },
    "files": ["main.js", "www/**/*"]
  }
}
```

**main.js**

```js
const { app, BrowserWindow } = require('electron');
const path = require('path');

function createWindow() {
  const win = new BrowserWindow({
    width: 1280, height: 720,
    autoHideMenuBar: true,
    webPreferences: { contextIsolation: true }
  });
  win.loadFile(path.join(__dirname, 'www', 'index.html'));
}

app.whenReady().then(createWindow);
app.on('window-all-closed', () => app.quit());
```

### 3. 打包成 exe

```bash
cd webgal-app
npm install
npm run start     # 先试玩验证
npm run dist      # 输出 exe
```

- **输出目录**：`webgal-app\dist\` → `南瓜乔哥的悲惨生活.exe`（portable 单文件）
- **替换游戏图标**：把你的 ico 放到工程根目录 `icon.ico`，`build.win.icon` 已指向它；重新 `npm run dist` 生效

### 4. ⚠️ 重要提醒

1. **素材文件名必须全英文**：不能有中文/空格/特殊符号。检查一遍：
   - ✅ 你的 `game/` 内目前全部合规（bg_stove.jpg、s01.mp3…）
   - ⚠️ 唯一注意：以后加新素材别用中文名；视频文件名也别带中文
2. **发给别人必须发完整文件夹/压缩包**：exe 只是壳，`www`（含 game）是数据。portable 版已内嵌全部资源可直接发单文件；若用安装版/nsis，发整个输出文件夹或打成一个 zip

---

# 第二部分：安卓打包（无编辑器导出，全手动）

> 明确：**不存在"编辑器一键导出安卓工程"这回事**，以下每一步都是手动操作。

### 1. 编译输出静态网页包

就用上面第零步的 zip（含 index.html、game、userStyleSheet.css 磨砂样式）。解压备用。

### 2. 下载官方安卓模板

GitHub 搜索 **OpenWebGAL/WebGAL_4_android**（或 WebGAL 官方文档里给的安卓原生模板仓库），`Code → Download ZIP`，解压。

### 3. 放入网页游戏

把静态网页包里的**全部内容**复制到模板的这个路径：

```
WebGAL-android/
└── app/src/main/assets/webgal/
    ├── index.html        ← 覆盖模板自带的
    ├── assets/
    ├── game/             ← 你的剧情、配音、磨砂样式都在这
    └── icons/
```

### 4. Android Studio 操作

1. **导入**：Open → 选模板根目录（第一次会自动 Gradle Sync 下载 SDK 依赖，需联网耐心等）
2. **改应用名**：`app/src/main/res/values/strings.xml` → `app_name` 改为「南瓜乔哥的悲惨生活」
3. **改包名**：`app/build.gradle` 里 `applicationId`（如 `com.carlown.nanhuaqiaoge`）；同步改 AndroidManifest 和源码目录的包路径（模板内有注释指引）
4. **换 APP 图标**：右键 `res` → New → Image Asset → Icon Type: Launcher Icons → 选你的 ico/png → Next → Finish

### 5. 生成签名 APK

```bash
# 在项目根目录生成 keystore（记住密码和别名！丢了无法更新应用）
keytool -genkey -v -keystore nanhua.keystore -alias nanhua \
        -keyalg RSA -keysize 2048 -validity 36500
```

然后二选一：

- **图形界面**：菜单 Build → Generate Signed Bundle / APK → APK → 选择 keystore → 输密码 → release → 完成后在 `app/release/` 拿到 APK
- **命令行**：在 `app/build.gradle` 配好 signingConfigs 后执行 `gradlew assembleRelease`，产物在 `app/build/outputs/apk/release/`

### 6. 兼容性坑（重要）

| 问题 | 说明 |
|---|---|
| `backdrop-filter` 失效 | 老旧安卓 WebView 不支持毛玻璃模糊 → **按钮丢失模糊只剩半透明边框，属正常现象，不会报错**。样式表里已有 `@supports` 降级：底色自动加深保证文字可读 |
| 部分手机黑屏 | WebView 内核过旧的机型可能白/黑屏；可在系统设置更新"Android System WebView"，或引导用户用 Chrome 打开在线版 |
| 大文件加载慢 | intro.mp4 有 46MB，低端机首次进入尾声章节要等几秒，属正常 |

### 7. iOS 说明

❌ **本方案无法生成 iOS 的 ipa 安装包**。iOS 需要 Mac + Xcode + 苹果开发者账号（$99/年）做原生壳或网页套壳，且上架需苹果审核。个人分发请用网页版链接（iPhone Safari 可直接玩）。
