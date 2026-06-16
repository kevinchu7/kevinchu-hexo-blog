---
title: OCR文本识别增强工具Demo（基于DeepSeek和腾讯云API）
index_img: https://static.kevinchu.top/blog/assets/img/cover_038.jpeg
date: 2026-06-16 11:06:43
updated: 2026-06-16 11:06:43
tags:
    - DeepSeek
    - 腾讯云
    - OCR
categories:
    - 雕虫小技
---

## 1. 前言

本文介绍一个轻量化搭建的OCR文本识别工具Demo，主要基于腾讯云OCR服务和DeepSeek API，实现对图片文本内容的识别、智能纠错、摘要提取以及关键词分析。

什么，你问我为什么不直接把图片发给豆包😅？别问，问就是搭着玩儿🤣。

PC、手机多端浏览器兼容，点击即刻参与限时体验➡️： [**OCR文本识别增强工具Demo**](https://ocr.kevinchu.top/)

## 2. 功能展示

简洁的操作界面🤣：
![](https://static.kevinchu.top/blog/public/20260616233843.png)

上传图片文件（支持选择文件、拖拽上传、URL）：
![](https://static.kevinchu.top/blog/public/20260616234317.png)


一键识别，展示分析结果（内容摘要、纠错后的文本、原始识别文本、TOP5关键词）：

<div class="markdown-body">
{% gi 4 1-2-1 %}
![](https://static.kevinchu.top/blog/public/20260616234635.png)
![](https://static.kevinchu.top/blog/public/20260616235257.png)
![](https://static.kevinchu.top/blog/public/20260616235418.png)
![](https://static.kevinchu.top/blog/public/20260616235648.png)
{% endgi %}
</div>

支持导出分析文档（markdown、doc）：
![](https://static.kevinchu.top/blog/public/20260617000423.png)


## 3. 开发细节 

项目已开源➡️ [**ocr-enhance-demo**](https://github.com/kevinchu7/ocr-enhance-demo)

### 3.1 技术栈

| 层级 | 技术 | 版本 |
|------|------|------|
| 后端框架 | SpringBoot | 2.7.15 |
| 前端 | 纯静态 HTML5 + LayUI | 2.8 |
| OCR 引擎 | 腾讯云通用印刷体识别 SDK | 3.1.782 |
| AI 文本增强 | DeepSeek Chat API | / |
| 图片处理 | Thumbnailator | 0.4.19 |
| 并发控制 | Guava + JUC Semaphore | 31.1-jre |


### 3.2 架构设计

#### 3.2.1 调用链路

```TEXT
用户提交图片/URL
  → 前端格式校验 + 防抖
  → RateLimitInterceptor（四层限流）
  → 后端图片校验 + 压缩 + 增强（降噪/扶正）
  → 腾讯云通用印刷体 OCR（获取原始文本）
  → DeepSeek Chat API（固定 Prompt：纠错 + 摘要 + 关键词）
  → 返回四大结果 + Base64 预览图
  → 前端渲染 + 临时文件即时删除
```

#### 3.2.2 限流策略

| 层级 | 规则 |
|------|------|
| 全局并发 | 单机最多 5 个并发请求 |
| 防抖 | 同 IP 5 秒内不可重复提交 |
| IP 日限 | 每 IP 每日最多 30 次 |
| IP 分钟限 | 每 IP 每分钟最多 3 次（滑动窗口） |

跨天自动清零，服务重启计数归零。

####  3.2.3 数据安全

- 上传图片 **全程不落数据库、不写永久文件、不记日志**
- URL 图片 **纯内存流处理**，不生成临时文件
- 所有密钥通过环境变量注入，**代码零硬编码**

#### 3.2.4 临时文件管理

本地上传的图片仅在本机临时目录短暂停留，处理完成立即删除。三层兜底机制确保不留残留：

| 层级 | 机制 | 触发时机 | 说明 |
|------|------|---------|------|
| 即时删除 | `finally` 块 | 每次接口返回（无论成功/失败/异常） | finally 块中代码确保请求结束即删 |
| 定时清理 | `@Scheduled` | 每 30 分钟 | 扫描临时目录中以 `ocr_` 开头的残留文件，删除超过 5 分钟的（应对 JVM 被 kill -9 硬杀等极端情况） |
| JVM 钩子 | ShutdownHook + `@PreDestroy` | 正常关服 | 应用关闭前最后兜底，清理残留 |

**临时文件位置**：由 `System.getProperty("java.io.tmpdir")` 决定：

| 操作系统 | 典型路径 | 特点 |
|----------|---------|------|
| Linux | `/tmp` | 通常为 tmpfs（内存文件系统），系统重启自动清空 |
| macOS | `/var/folders/xx/.../T/` | 系统定期清理 |
| Windows | `C:\Users\<用户名>\AppData\Local\Temp\` | 磁盘持久，依赖应用自清理 |

> Docker 部署时 `/tmp` 为容器层，容器销毁后自动消失，是天然的第四层保障。

**URL 模式**：图片直接从 HTTP 连接读入 `byte[]`，用 `ByteArrayInputStream` / `ByteArrayOutputStream` 全内存流转，不调用 `File.createTempFile`，零文件残留。

#### 3.2.5 真实 IP 获取

兼容 Nginx / CDN / 云代理场景，四级降级链：

```
X-Forwarded-For → X-Real-IP → Proxy-Client-IP → RemoteAddr
```


###  3.3 服务部署


#### 3.3.1 Docker服务器部署
>**本地打包 + build 目录** 模式：在开发机上打好 JAR，把 `build/` 目录上传服务器，服务器无需安装 Maven 或 JDK

源码➡️ [**ocr-enhance-demo**](https://github.com/kevinchu7/ocr-enhance-demo)
```bash
# ===== 步骤1：本地打包 =====
mvn clean package -DskipTests
cp target/ocr-enhance-*.jar build/ocr-enhance.jar

# ===== 步骤2：编辑 build/.env 填入真实密钥 =====
# 密钥获取地址：
#   腾讯云 SecretId/Key → https://console.cloud.tencent.com/cam/capi
#       使用前需开通 OCR 服务：https://console.cloud.tencent.com/ocr/overview
#       通用印刷体识别 API 文档：https://cloud.tencent.com/document/product/866/33526
#   DeepSeek API Key    → https://platform.deepseek.com/api_keys

# ===== 步骤3：上传 build/ 目录到服务器 =====
rsync -av build/ user@your-server:/opt/ocr-enhance/

# ===== 步骤4：服务器上启动 =====
cd /opt/ocr-enhance/build
chmod +x start.sh
./start.sh                     # 自动检测 docker-compose / docker，一键启动
```


#### 3.3.2 可配置参数

 `.env` 中可配置的完整参数：

| 变量名 | 默认值 | 说明 |
|---|---|---|
| `SERVER_PORT` | 8787 | 服务端口 |
| `TENCENT_SECRET_ID` | — | 腾讯云 SecretId |
| `TENCENT_SECRET_KEY` | — | 腾讯云 SecretKey |
| `TENCENT_REGION` | ap-guangzhou | 腾讯云地域 |
| `TENCENT_OCR_TIMEOUT` | 30 | 腾讯云 OCR 超时（秒） |
| `DEEPSEEK_API_KEY` | — | DeepSeek API Key |
| `DEEPSEEK_API_URL` | ```https://api.deepseek.com/v1/chat/completions``` | DeepSeek 接口地址 |
| `DEEPSEEK_TIMEOUT` | 90 | DeepSeek 调用超时（秒） |
| `MAX_FILE_SIZE` | 8MB | 上传文件大小限制 |
| `RATE_LIMIT_CONCURRENT` | 5 | 全局并发上限 |
| `RATE_LIMIT_PER_MINUTE` | 3 | IP 每分钟上限 |
| `RATE_LIMIT_PER_DAY` | 30 | IP 每日上限 |
| `IMAGE_COMPRESS_MAX_WIDTH` | 1920 | 压缩后最大宽度 |
| `IMAGE_COMPRESS_QUALITY` | 0.85 | 压缩质量 |
| `LOGGING_PATH` | /app/logs | 日志输出目录 |


---
***演示项目，仅供参考🤣。***