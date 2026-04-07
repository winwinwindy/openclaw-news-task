---
name: news-site-generator
description: |
  将新闻数据生成精美新闻网页（供邮件附件使用）。
  
  使用场景：用户说"生成新闻"、"生成网页"时触发。
  前置条件：已有新闻数据（通过 financial-news-scraper 获取）
---

# 新闻网页生成器

## 功能

接收新闻数据（10条），生成本地 HTML 文件供邮件附件使用。

## 样式要求

### 1. 语言显示规则（重要！）

**网页内容必须全部是中文或全部是英文，不允许中英文混合！**

- 页面所有文本要么全中文，要么全英文
- 点击中英文切换按钮后，整个页面切换到另一种语言
- 新闻标题、摘要、时间、来源等全部需要双语对照
- 默认显示中文

### 2. 中英文切换按钮

- 页面右上角提供两个切换按钮："中" 和 "ENG"
- 点击"中"：激活状态（高亮），全文显示中文
- 点击"ENG"：激活状态（高亮），全文显示英文
- 切换时整个页面所有内容同时切换

**按钮样式示例：**
```html
<style>
  .lang-btn { padding: 6px 12px; border-radius: 8px; cursor: pointer; opacity: 0.6; }
  .lang-btn.active { opacity: 1; font-weight: bold; background: #1f6feb; color: #fff; }
</style>
<button id="btn-zh" class="lang-btn active" onclick="setLang('zh')">中</button>
<button id="btn-en" class="lang-btn" onclick="setLang('en')">ENG</button>
```

### 3. 指数颜色显示

- 指数上升：使用红色（#ff4444 或 #e74c3c）
- 指数下跌：使用绿色（#00c851 或 #27ae60）
- 字体颜色要显眼易读

### 4. 每条新闻显示内容

每条新闻必须包含以下信息：

| 字段 | 说明 | 格式示例 |
|------|------|----------|
| 新闻标题 | 标题文字 | 字符串 |
| 新闻摘要 | 新闻内容摘要 | 字符串 |
| 发布时间 | 年-月-日 时：分 | 2026-04-01 17:30 |
| 新闻来源 | 来源网站名 | Reuters / Bloomberg 等 |
| 跳转链接 | 点击标题跳转 | 完整 URL |

### 5. 时间显示格式

**必须使用精确时间格式：年-月-日 时：分**

正确示例：
- `2026-04-01 17:30`
- `2026-04-01 09:15`
- `2026-03-31 23:45`

不正确示例（不允许）：
- ❌ "刚刚"
- ❌ "17分钟前"
- ❌ "3小时前"
- ❌ "昨天"

### 6. 新闻来源中英文映射

| 中文 | English |
|------|---------|
| 路透社 | Reuters |
| 彭博 | Bloomberg |
| 金融时报 | Financial Times |
| 财新 | Caixin |
| 华尔街见闻 | Wall Street CN |

## 语言切换实现

```javascript
// 双语对照数据
const translations = {
  zh: {
    title: "全球热点新闻",
    date: "2026年4月1日",
    marketTitle: "市场行情",
    newsTitle: "标题",
    newsSummary: "摘要",
    newsTime: "发布时间",
    newsSource: "来源",
    readMore: "阅读更多",
    footer: "新闻来源"
  },
  en: {
    title: "Global Hot News",
    date: "April 1, 2026",
    marketTitle: "Market",
    newsTitle: "Title",
    newsSummary: "Summary",
    newsTime: "Time",
    newsSource: "Source",
    readMore: "Read More",
    footer: "Sources"
  }
};

// 来源名称映射
const sourceNames = {
  zh: { "Reuters": "路透社", "Bloomberg": "彭博", "Financial Times": "金融时报", "Wall Street CN": "华尔街见闻" },
  en: { "路透社": "Reuters", "彭博": "Bloomberg", "金融时报": "Financial Times", "华尔街见闻": "Wall Street CN" }
};

let currentLang = 'zh';

function setLang(lang) {
  currentLang = lang;
  updatePage();
}

function updatePage() {
  const t = translations[currentLang];
  
  // 更新按钮状态
  document.getElementById('btn-zh').classList.toggle('active', currentLang === 'zh');
  document.getElementById('btn-en').classList.toggle('active', currentLang === 'en');
  
  // 更新页面所有文本元素
  document.querySelector('.title').textContent = t.title;
  document.querySelector('.date').textContent = t.date;
  document.querySelector('.market-title').textContent = t.marketTitle;
  
  // 更新新闻内容
  document.querySelectorAll('.news-card').forEach((el, i) => {
    const news = newsData[i];
    el.querySelector('.news-title').textContent = currentLang === 'zh' ? news.title : news.title_en;
    el.querySelector('.news-summary').textContent = currentLang === 'zh' ? news.summary : news.summary_en;
    el.querySelector('.news-source').textContent = currentLang === 'zh' ? news.source : news.source_en;
  });
}
    el.textContent = sourceNames[currentLang][el.textContent] || el.textContent;
  });
}
```

## 工作流程

### 1. 生成网页

根据输入的新闻数据创建 HTML 文件，包含：
- 深色主题设计
- 顶部市场行情 ticker（指数上升红色，下跌绿色）
- 10 条新闻卡片
- 每条新闻显示：标题、摘要、发布时间、来源、跳转链接
- 中英文切换按钮，默认中文
- **所有文本必须有中文和英文两个版本**

**文件保存为 `index.html`**

### 2. 本地预览（可选）

```bash
cd ~/.openclaw/workspace
python3 -m http.server 9999
```

用 browser 工具打开：
```javascript
browser({ action: "open", targetUrl: "http://localhost:9999/" })
```

---

## 完整工作流

```
1. financial-news-scraper  → 爬取新闻数据
2. news-site-generator    → 生成网页（index.html）
   ├── 深色主题
   ├── 指数颜色：上升红色，下跌绿色
   ├── 中英文切换按钮（默认中文）
   ├── 新闻显示：标题、摘要、发布时间、来源、跳转链接
   └── 时间格式：年-月-日 时：分
3. 发送邮件             → 附带HTML附件
```

## 注意事项

| 注意事项 | 说明 |
|----------|------|
| 文件名 | 保存为 `index.html`（本地文件） |
| 指数颜色 | 上升红色，下跌绿色 |
| 语言切换 | 按钮切换，默认中文，内容统一不混排 |
| 时间格式 | 必须为 年-月-日 时：分，如 2026-04-01 17:30 |
| 新闻字段 | 必须包含：标题、摘要、发布时间、来源、跳转链接 |

### 新闻数据格式（双语 - 必须统一）

```javascript
const newsData = [
  {
    title: "特朗普就伊朗战争发表全国讲话",
    title_en: "Trump delivers address on Iran war",
    summary: "特朗普在讲话中表示伊朗战争目标已基本完成。",
    summary_en: "Trump stated goals nearly accomplished.",
    time: "2026-04-02 09:30",
    source: "路透社",
    source_en: "Reuters",
    url: "https://..."
  },
  // ... 共10条，每条必须有 title/title_en/summary/summary_en
];

// 市场指数双语
const tickerData = [
  { name: "标普500", name_en: "S&P 500", value: "+0.72%", price: "6,575.32", positive: true },
  { name: "纳斯达克", name_en: "Nasdaq", value: "+1.16%", price: "21,840.95", positive: true },
  // ...
];
```

**关键：所有内容必须有中英文两个版本，切换时统一切换**

## 输出示例

```
✅ HTML 已生成：news-site/index.html
📎 邮件附件：HTML 文件
