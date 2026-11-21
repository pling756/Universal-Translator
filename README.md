# 全能翻译

一个单文件的 Web 翻译应用：全部样式与脚本内联在 `全能翻译.html` 中，浏览器直接打开即可使用，无需安装、无需构建、无需本地服务器。

## 功能介绍

### 文本互译
- 输入即出译文，支持源语言自动检测与手动指定目标语言。
- 双引擎结果并排对比（谷歌 gtx / MyMemory），可在设置中选择自动切换、仅谷歌、仅 MyMemory 或仅自定义 AI 接口。
- 译文本地缓存（上限 200 条），同一文本重复查询直接复用缓存结果。
- 结果行内支持朗读、复制、收藏；可选 AI 润色（需自行配置 OpenAI 兼容接口）。
- 快捷键：`Ctrl / Cmd + Enter` 触发翻译。

### 面对面对话
- 中外语句以双语气泡交替呈现，支持双向互译。
- 麦克风语音输入（浏览器 Web Speech API），可导出对话记录为 TXT。

### 拍照 / 图片识别
- 上传图片后由 OCR 识别文字，再翻译为目标语言；OCR 语言可选择。

### 文档翻译
- 支持 TXT、Markdown、SRT、Word（.docx）、PDF；单篇建议 5 万字以内。
- 输出双语对照结果，可复制全部，或导出为 TXT / Markdown。

### 划词释义弹窗
- 单词音标、词性释义、经典例句。
- 五个权威词典来源一键跳转：维基词典 Wiktionary、Merriam-Webster 韦氏、Cambridge 剑桥、Longman 朗文、Oxford 牛津学习者词典。
- 支持朗读发音、一键录入个人词典。

### 收藏与历史
- 常用译文可收藏；历史记录保留近期查询，支持再次使用、收藏或删除。

### 个人词典与闪卡复习
- 词条录入后保存音标、释义、例句与掌握状态。
- 闪卡复习可遮挡释义，标记「认识 / 不认识」，掌握后显示已掌握徽章，并保留进度统计。
- 词典支持 JSON 导出与导入备份。

### 其他
- 浅色 / 深色 / 跟随系统三种主题。
- 本地设置、历史、收藏、词典与缓存均可通过 JSON 导出 / 导入；可一键清空全部本地数据。

## 支持语言

### 翻译语言（40 种）
中文（简体）、中文（繁体）、English、日本語、한국어、Français、Deutsch、Español、Português、Русский、Italiano、العربية、हिन्दी、ไทย、Tiếng Việt、Indonesia、Melayu、Türkçe、Nederlands、Polski、Svenska、Українська、Čeština、Dansk、Suomi、Ελληνικά、עברית、Magyar、Norsk、Română、Slovenčina、Български、فارسی、বাংলা、தமிழ்、اردو、Filipino、Català、Kiswahili、ខ្មែរ、Монгол。

### OCR 识别语言（20 种）
English、中文（简体）、中文（繁体）、日本語、한국어、Français、Español、Deutsch、Русский、Italiano、Português、العربية、हिन्दी、ไทย、Tiếng Việt、Indonesia、Nederlands、Polski、Türkçe、Українська。

## 运行方式

1. 使用现代浏览器（Edge / Chrome / Firefox / Safari）打开 `全能翻译.html`。
2. 首次使用需联网：翻译、OCR 与词典接口在运行时调用；CDN 解析库按需加载。
3. 语音朗读依赖浏览器内置 TTS；语音输入依赖 Web Speech API，部分浏览器需要授权麦克风并保持联网。
4. 所有数据仅保存在当前浏览器的 localStorage 中，不做云端同步；清理浏览器数据会丢失记录，建议定期用 JSON 导出备份。

## CDN 依赖

以下库不打包进源码，在对应功能首次使用时从 CDN 动态加载，主源失败时回退备用源：

| 用途 | 库与版本 | 主源 | 备用源 |
|---|---|---|---|
| 网页字体 | Noto Sans SC | `https://miaoda.feishu.cn/fonts/css2?family=Noto+Sans+SC:wght@300;400;500;700&display=swap` | — |
| 图片 OCR | tesseract.js 5.1.1 | `https://fastly.jsdelivr.net/npm/tesseract.js@5.1.1/dist/tesseract.min.js` | `https://cdnjs.cloudflare.com/ajax/libs/tesseract.js/5.1.1/tesseract.min.js` |
| DOCX 解析 | mammoth 1.8.0 | `https://fastly.jsdelivr.net/npm/mammoth@1.8.0/mammoth.browser.min.js` | `https://cdnjs.cloudflare.com/ajax/libs/mammoth/1.8.0/mammoth.browser.min.js` |
| PDF 解析 | pdfjs-dist 3.11.174 | `https://fastly.jsdelivr.net/npm/pdfjs-dist@3.11.174/build/pdf.min.js` | 同版本 worker：`pdf.worker.min.js` |

## 接口说明

### 翻译引擎
- **谷歌 gtx**：`GET https://translate.googleapis.com/translate_a/single?client=gtx&sl=<源语言>&tl=<目标语言>&dt=t&q=<文本>`
- **MyMemory**：`GET https://api.mymemory.translated.net/get?langpair=<源语言>|<目标语言>&q=<文本>`
- **自定义 AI（可选，OpenAI 兼容）**：`POST <aiBase>/chat/completions`，请求体包含 `model`、`temperature: 0.2` 与系统/用户消息；在设置中填写接口地址、API Key 与模型名称后启用，用于 AI 翻译与润色；留空则不启用。

### 词典查询
- `GET https://api.dictionaryapi.dev/api/v2/entries/en/<英文单词>`，返回音标、释义与例句；失败时弹窗仍保留外部词典跳转链接。

### 外部词典来源
- 维基词典：`https://en.wiktionary.org/wiki/<词头>`
- Merriam-Webster：`https://www.merriam-webster.com/dictionary/<词头>`
- Cambridge：`https://dictionary.cambridge.org/dictionary/english/<词头>`
- Longman：`https://www.ldoceonline.com/dictionary/<词头>`
- Oxford Learner's：`https://www.oxfordlearnersdictionaries.com/definition/english/<词头>`

## 本地存储键

| 键名 | 内容 |
|---|---|
| `fy_settings_v1` | 设置项（目标语言、引擎、主题、AI 接口配置等） |
| `fy_history_v1` | 翻译历史记录 |
| `fy_stars_v1` | 收藏译文 |
| `fy_mydict_v1` | 个人词典词条 |
| `fy_cache_v1` | 译文本地缓存 |

## 限制与说明

- 免费翻译接口存在额度、可用性与网络限制；译文质量不及付费大模型或人工校对。
- OCR 识别与离线能力弱于大厂方案；本应用不提供离线翻译包。
- AI 翻译与润色效果取决于所配置接口与模型；未配置时该入口不可用。
- 界面演示中的例句为占位演示数据，不代表引擎真实返回结果。

## 文件结构

| 文件 | 说明 |
|---|---|
| `全能翻译.html` | 应用本体，内联全部 CSS 与原生 JavaScript，无前端框架依赖 |
| `README.md` | 本说明文档 |
