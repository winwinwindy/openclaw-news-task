---
name: email-sender
description: 使用 Python 通过 SMTP 发送邮件。支持 Gmail、163、QQ 等常见邮箱服务。默认使用 HTML 格式发送。
---

# Email Sender

使用 Python smtplib 发送邮件，**默认使用 HTML 格式**。

## 使用方法

```bash
python ~/.agents/skills/email-sender/scripts/send_email.py --to <收件人> --subject <标题> --body <内容>
```

## 参数

- `--to` / `-t`: 收件人邮箱（必填）
- `--subject` / `-s`: 邮件标题（必填）
- `--body` / `-b`: 邮件内容（必填，可多行）
- `--plain`: 使用纯文本格式（默认 HTML）
- `--from`: 发件人邮箱（默认使用配置的发件人）
- `--attachment` / `-a`: 附件路径（可选，新闻邮件自动附带HTML附件）
- `--news`: 使用新闻邮件格式（深色主题）
- `--date`: 新闻日期
- `--ticker`: 市场指数（JSON格式，需含 name/value/price/positive）
- `--sources`: 来源统计（中文）
- `--site-url`: 网站链接（可选，已不部署到Netlify）

## 配置

默认配置已设置为 163 邮箱：
- SMTP 服务器: smtp.163.com
- SMTP 端口: 465 (SSL)
- 发件人: winwinwindy2026@163.com

如需修改配置，编辑 `scripts/send_email.py` 开头的配置变量。

## 示例

```bash
# 默认 HTML 格式
python ~/.agents/skills/email-sender/scripts/send_email.py \
  --to "test@example.com" \
  --subject "测试邮件" \
  --body "这是邮件内容"

# 纯文本格式
python ~/.agents/skills/email-sender/scripts/send_email.py \
  --to "test@example.com" \
  --subject "测试邮件" \
  --body "这是纯文本内容" \
  --plain

# 新闻邮件格式（与网站样式一致）- 深色主题 + 附件
python ~/.agents/skills/email-sender/scripts/send_email.py \
  --to "hasedataapplication@noexternalmail.hsbc.com" \
  --subject "🌍 全球热点新闻 - 2026年4月2日" \
  --body "$(cat /tmp/news_items.html)" \
  --news \
  --date "2026年4月2日" \
  --ticker '[{"name":"标普500","value":"+0.72%","price":"6,575.32","positive":true},{"name":"纳斯达克","value":"+1.16%","price":"21,840.95","positive":true},{"name":"富时100","value":"+1.85%","price":"10,364.79","positive":true},{"name":"日经225","value":"-1.09%","price":"53,153.84","positive":false},{"name":"原油","value":"+3.68%","price":"$103.95","positive":true},{"name":"黄金","value":"-2.45%","price":"$4,695.30","positive":false}]' \
  --sources "路透社 5, 彭博 2" \
  -a "/path/to/news-site/index.html"
```

**重要：邮件必须包含10条新闻内容！**

参数说明：
- `--body`: 10条新闻的HTML内容（从网站index.html解析，必须包含）
- `--news`: 使用新闻邮件格式（深色主题）
- `--ticker`: 6个市场指数（标普500/纳斯达克/富时100/日经225/原油/黄金）
- `--sources`: 来源统计
- `-a`: HTML附件

## 邮件必须包含新闻内容

**邮件 body 中必须直接嵌入10条新闻的完整信息，不能只显示"请查看附件"！**

每条新闻必须包含：
| 字段 | 说明 |
|------|------|
| **新闻标题** | 标题文字 |
| **新闻摘要** | 新闻内容摘要 |
| **发布时间** | 年-月-日 时：分 |
| **新闻来源** | 来源网站名 |
```

## 新闻邮件内容

发送新闻网站邮件时，建议包含以下内容：

### 1. 直接嵌入网站内容（可选）

可以将网站HTML内容直接嵌入邮件，保持相同的视觉样式：

**邮件版HTML结构：**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>全球热点新闻</title>
    <!-- Outlook兼容：仅使用内联style，避免媒体查询和flexbox -->
</head>
<body style="background-color: #ffffff; margin: 0; padding: 20px; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;">
    <table width="100%" border="0" cellspacing="0" cellpadding="0">
        <tr>
            <td align="center">
                <!-- 主内容区：深色背景 -->
                <table width="600" border="0" cellspacing="0" cellpadding="0" style="background-color: #2d333b; border-radius: 16px; border: 1px solid #444c56;">
                    <!-- 标题区域 -->
                    <tr>
                        <td style="padding: 24px 24px 8px 24px; border-bottom: 1px solid #444c56;">
                            <h1 style="font-size: 24px; color: #ffffff; margin: 0 0 8px 0; font-weight: 700;">🌍 全球热点新闻</h1>
                            <p style="color: #adbac7; font-size: 13px; margin: 0 0 16px 0;">2026年4月2日</p>
                        </td>
                    </tr>
                    
                    <!-- 市场指数：红色边框 -->
                    <tr>
                        <td style="padding: 16px 24px 20px 24px;">
                            <table width="100%" border="0" cellspacing="0" cellpadding="0" style="background-color: #2d333b; border-radius: 12px; border: 2px solid #f85149;">
                                <tr>
                                    <td style="padding: 12px 16px 4px 16px;">
                                        <p style="margin: 0; color: #ffffff; font-size: 14px; font-weight: 600;">市场行情</p>
                                    </td>
                                </tr>
                                <tr>
                                    <td style="padding: 0 16px 12px 16px;">
                                        <table width="100%" border="0" cellspacing="0" cellpadding="0">
                                            <tr>
                                                <td align="center" style="padding: 0 12px; border-left: 1px solid #444c56;">
                                                    <span style="display: block; color: #adbac7; font-size: 12px;">标普500</span>
                                                    <span style="display: block; color: #ffffff; font-size: 16px; font-weight: 600;">6,575.32</span>
                                                    <span style="display: block; font-weight: 700; font-size: 16px; color: #f85149;">+0.72%</span>
                                                </td>
                                                <td align="center" style="padding: 0 12px; border-left: 1px solid #444c56;">
                                                    <span style="display: block; color: #adbac7; font-size: 12px;">纳斯达克</span>
                                                    <span style="display: block; color: #ffffff; font-size: 16px; font-weight: 600;">21,840.95</span>
                                                    <span style="display: block; font-weight: 700; font-size: 16px; color: #f85149;">+1.16%</span>
                                                </td>
                                            </tr>
                                        </table>
                                    </td>
                                </tr>
                            </table>
                        </td>
                    </tr>
                    
                    <!-- 新闻列表 -->
                    <tr>
                        <td style="padding: 0 24px 20px 24px;">
                            <table width="100%" border="0" cellspacing="0" cellpadding="0">
                                <!-- 新闻项 -->
                                <tr>
                                    <td style="padding: 0 0 12px 0;">
                                        <table width="100%" border="0" cellspacing="0" cellpadding="0" style="background-color: #373e47; border-radius: 12px; border: 2px solid #545d67;">
                                            <tr>
                                                <td style="padding: 16px;">
                                                    <a href="https://..." style="color: #58a6ff; text-decoration: none; font-size: 15px; font-weight: 600; line-height: 1.4;" target="_blank">新闻标题</a>
                                                    <p style="margin: 8px 0 0 0; color: #c9d1d9; font-size: 13px; line-height: 1.5;">新闻摘要内容...</p>
                                                    <table width="100%" border="0" cellspacing="0" cellpadding="0" style="margin-top: 10px; border-top: 1px solid #545d67;">
                                                        <tr>
                                                            <td style="padding: 4px 10px; font-size: 11px; color: #8b949e; font-weight: 600;">路透社</td>
                                                            <td align="right" style="color: #6e7681; font-size: 11px;">2026-04-02 09:30</td>
                                                        </tr>
                                                    </table>
                                                </td>
                                            </tr>
                                        </table>
                                    </td>
                                </tr>
                            </table>
                        </td>
                    </tr>
                    
                    <!-- 底部 -->
                    <tr>
                        <td style="padding: 16px 24px 24px 24px; border-top: 1px solid rgba(255,255,255,0.1); text-align: center;">
                            <p style="margin: 0 0 8px 0;"><a href="https://xxx.netlify.app" style="color: #00d4ff; text-decoration: none; font-size: 13px;">🔗 打开在线版新闻网站</a></p>
                            <p style="margin: 0; color: #888888; font-size: 10px;">Sent via OpenClaw</p>
                        </td>
                    </tr>
                </table>
            </td>
        </tr>
    </table>
</body>
</html>
```

### 2. 邮件中包含的内容

| 内容 | 说明 |
|------|------|
| 页面标题 | 全球热点新闻 + 日期 |
| 市场指数 | S&P 500 / Nasdaq / Brent Oil / Gold 涨跌 |
| 新闻列表 | 10条新闻（标题+摘要+来源+时间） |
| 跳转链接 | 每条新闻可点击跳转原文 |
| 在线查看 | 底部添加网站链接 |

### 3. 校验报告
邮件内容应包含 `news-quality-checker` 的校验结果，包括：

| 校验项 | 说明 |
|--------|------|
| **完整性** | 标题非空、来源正确、链接有效 |
| **内容准确性** | 标题与内容匹配、无占位符 |
| **时效性** | 发布时间在24小时内、为热点新闻 |
| **多源覆盖** | 来源分布多元化（不止单一来源） |

### 3. 邮件内容模板

```html
<h2>📰 今日热点新闻</h2>

<p>🔗 访问地址：<a href="https://xxx.netlify.app">https://xxx.netlify.app</a></p>

<h3>✅ 校验结果</h3>
<ul>
  <li>完整性检查：
    - 标题: ✅
    - 来源: ✅ (Reuters X条 + Bloomberg Y条 + ...)
    - 链接: ✅ (有效文章链接，非首页)
  </li>
  <li>内容准确性：
    - 标题内容匹配: ✅
    - 无错误信息: ✅
  </li>
  <li>时效性检查：
    - 发布时间: ✅ (均在最近24小时内)
    - 热点新闻: ✅
  </li>
  <li>多源覆盖：
    - Reuters: X条
    - Bloomberg: Y条
    - FT: Z条 (如有)
  </li>
</ul>

<p>🕐 更新时间：2026-04-01 19:00</p>
```

### 4. 重要提示

- 邮件中的网站链接必须是**根 URL**，不能带文件名
- 正确：`https://xxx.netlify.app`
- 错误：`https://xxx.netlify.app/index.html`

## 邮件配色方案 (深色主题)

| 元素 | 颜色代码 | 说明 |
|------|---------|------|
| 邮件背景 | #ffffff | 白色 |
| 主内容区 | #2d333b | 深灰背景 |
| 标题区边框 | #444c56 | 分割线 |
| 市场指数边框 | #f85149 | 红色边框突出 |
| 上涨 | #f85149 | 红色 |
| 下跌 | #3fb950 | 绿色 |
| 新闻卡片背景 | #373e47 | 卡片深色 |
| 新闻卡片边框 | #545d67 | 边框线 |
| 标题文字 | #ffffff | 白色 |
| 正文文字 | #c9d1d9 | 浅灰 |
| 链接 | #58a6ff | 蓝色 |
| 来源/时间 | #8b949e / #6e7681 | 灰色 |

## 新闻邮件 HTML 模板（2026-04-03 更新）

**重要：以下为验证通过的邮件模板，后续热点新闻邮件请使用此模板格式**

### 模板结构

```html
<table width="100%" border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td style="padding: 16px 0;">
      <h2 style="color: #ffffff; font-size: 18px; margin: 0 0 16px 0;">📰 今日热点新闻</h2>
      
      <!-- 新闻1 -->
      <div style="margin-bottom: 20px;">
        <a href="[新闻链接]" style="color: #58a6ff; text-decoration: none; font-size: 15px; font-weight: 600;">[新闻标题]</a>
        <p style="color: #c9d1d9; font-size: 13px; margin: 8px 0; line-height: 1.5;">[新闻摘要]</p>
        <div style="font-size: 11px; color: #8b949e;">
          <span style="font-weight: 600;">[来源]</span> · [发布时间]
        </div>
      </div>

      <!-- 新闻2-10 同上结构 -->
      ...
      
    </td>
  </tr>
</table>
```

### 模板特点

| 特点 | 说明 |
|------|------|
| 新闻无额外背景 | 标题、摘要、来源、时间直接显示，无卡片背景 |
| 无分隔线 | 新闻之间无分隔线，简洁排列 |
| 链接颜色 | #58a6ff (蓝色) |
| 摘要颜色 | #c9d1d9 (浅灰) |
| 来源/时间颜色 | #8b949e (灰色) |
| 标题字体 | 15px, 600 粗体 |
| 摘要字体 | 13px, 1.5 行高 |

### 使用方法

将上述模板中 `[新闻链接]`、`[新闻标题]`、`[新闻摘要]`、`[来源]`、`[发布时间]` 替换为实际新闻内容，共10条新闻。

### 示例命令

```bash
python ~/.agents/skills/email-sender/scripts/send_email.py \
  --to "windy.c.w.chen@noexternalmail.hsbc.com" \
  --subject "🌍 全球热点新闻 - 2026年4月3日" \
  --body "$(cat /tmp/news_email_body.html)" \
  --news \
  --date "2026年4月3日" \
  --ticker '[{"name":"标普500","value":"+0.11%","price":"6,582.69","positive":true},{"name":"纳斯达克","value":"+0.18%","price":"21,879.18","positive":true},{"name":"道琼斯","value":"-0.13%","price":"46,504.67","positive":false},{"name":"富时100","value":"+0.69%","price":"10,436.29","positive":true},{"name":"日经225","value":"+1.26%","price":"53,123.49","positive":true},{"name":"原油","value":"+11.41%","price":"$111.54","positive":true},{"name":"黄金","value":"-2.77%","price":"$4,679.70","positive":false}]' \
  --sources "路透社 6, 彭博 4" \
  -a "/Users/dspa/.openclaw/workspace/news-site/index.html"
```

## HTML 模板

默认使用简洁的 HTML 模板，包含：
- 白色卡片式布局
- 汇丰红色标题样式
- 自动换行处理
