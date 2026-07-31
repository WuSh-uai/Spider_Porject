```markdown
 🕷️ 爬虫项目合集

> 个人爬虫学习与实践项目集合，涵盖新闻资讯、电商图书、数据解析工具等多维度爬虫技术实践。

---

 📁 项目列表

| 项目 | 目标网站/场景 | 技术栈 | 核心功能 |
|------|---------|--------|---------|
| [新华网教育新闻爬虫](./Xinhua_Spider/) | education.news.cn | Python, curl_cffi, lxml, PyMySQL | 新闻列表获取 → 详情页解析 → 数据去重入库 |
| [当当网图书爬虫](./Dangdang_Spider/) | search.dangdang.com | Python, requests, BeautifulSoup, PyMySQL | 出版社检索 → 分页抓取 → 图书信息入库 |
| [HTML解析工具对比](./FetchWeapon_Use/) | 本地HTML文件 | Python, lxml, BeautifulSoup, PyQuery, html5lib | 四种解析库同场对比 → 数据提取 → JSON导出 |


 📌 项目一：新华网教育新闻爬虫

目标：采集新华网教育频道的新闻标题、日期、来源、正文内容，存入 MySQL。

 核心实现

- 反爬绕过：使用 `curl_cffi` 模拟 Chrome 120 浏览器指纹
- 数据解析：基于 `lxml` 和 XPath 提取结构化数据
- 数据清洗：正则表达式去除正文中的 Unicode 特殊空白符
- 去重更新：字典存储 + `INSERT ... ON DUPLICATE KEY UPDATE` 实现增量更新

 技术栈

`Python 3.10+` | `curl_cffi` | `lxml` | `PyMySQL` | `re`

 快速开始

```bash
cd Xinhua_Spider
pip install curl_cffi lxml pymysql
```

执行前需创建数据库表：

```sql
CREATE DATABASE xinhua DEFAULT CHARACTER SET utf8mb4;
USE xinhua;
CREATE TABLE xinhua_news (
    id INT AUTO_INCREMENT PRIMARY KEY,
    标题 VARCHAR(255) UNIQUE,
    日期 VARCHAR(50),
    来源 VARCHAR(100),
    内容 TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

修改数据库配置后运行：

```bash
python Xinhua_Spider.py
```


 📌 项目二：当当网图书爬虫

目标：根据出版社列表，爬取当当网该出版社的图书信息（书名、价格、出版日期、评论数），存入 MySQL。

 核心实现

- 动态参数构建：解析搜索页 HTML，动态获取 `input` 标签的 `name` 属性，拼接请求参数
- 分页遍历：自动识别总页数，循环爬取每一页数据
- 编码适配：统一编码处理（GB2312 → UTF-8），确保中文关键词精准检索
- 批量入库：使用 PyMySQL 批量插入图书数据

 技术栈

`Python 3.10+` | `requests` | `BeautifulSoup` | `PyMySQL` | `urllib.parse`

 快速开始

```bash
cd Dangdang_Spider
pip install requests beautifulsoup4 pymysql
```

准备出版社列表文件 `press.txt`（每行一个出版社名称）：

```
清华大学出版社
北京大学出版社
人民邮电出版社
```

创建数据库表：

```sql
CREATE DATABASE dangdang DEFAULT CHARACTER SET utf8mb4;
USE dangdang;
CREATE TABLE dangd (
    id INT AUTO_INCREMENT PRIMARY KEY,
    number INT,
    title VARCHAR(255),
    price VARCHAR(50),
    date VARCHAR(50),
    comments VARCHAR(50)
);
```

修改 `main()` 中的文件路径和数据库配置后运行：

```bash
python Dangdang_Spider.py
```


 📌 项目三：HTML解析工具对比

目标：使用同一份 HTML 文件，横向对比四种主流解析库的语法差异与使用方式，并统一输出 JSON 格式结果。

 四种解析方式对比

| 解析方式 | 选择器语法 | 适用场景 |
|----------|-----------|---------|
| lxml + XPath | `//div[@id="xxx"]` | 适合复杂的层级定位，解析速度快 |
| BeautifulSoup | `select_one('.class')` | 语法友好，适合快速开发 |
| PyQuery | `('id')` | 类似 jQuery 语法，前端开发者上手快 |
| html5lib | `iter()` 迭代器遍历 | 容错性强，可处理不规范的 HTML |

 核心实现

- 统一接口：四种解析方式均实现相同的输入输出规范
- 数据提取：解析新闻标题、日期、来源、正文段落
- 结果导出：采集结果统一存储为 `result.json`，便于后续消费

 技术栈

`Python 3.10+` | `lxml` | `BeautifulSoup` | `PyQuery` | `html5lib`

 快速开始

```bash
cd FetchWeapon_Use
pip install lxml beautifulsoup4 pyquery html5lib
```

将待解析的 HTML 文件命名为 `Class_html_report3.html` 放入同目录，运行：

```bash
python FetchWeapon_Use.py
```

输出结果示例（`result.json`）：

```json
{
  "标题": "示例新闻标题",
  "日期": "2026-07-31",
  "来源": "新华网",
  "内容": ["正文段落1", "正文段落2", "正文段落3"]
}
```


 🛠️ 技术栈总览

| 类别 | 技术 |
|------|------|
| 语言 | Python 3.10+ |
| 请求库 | requests, curl_cffi |
| 解析库 | lxml, BeautifulSoup, PyQuery, html5lib |
| 数据库 | MySQL + PyMySQL |
| 编码处理 | urllib.parse, re |
| 数据导出 | json |


 📁 目录结构

```
Spider_Project/
├── Xinhua_Spider/
│   ├── Xinhua_Spider.py
│   └── README.md
├── Dangdang_Spider/
│   ├── Dangdang_Spider.py
│   ├── press.txt
│   └── README.md
├── FetchWeapon_Use/
│   ├── FetchWeapon_Use.py
│   ├── Class_html_report3.html
│   └── README.md
└── README.md                       总项目说明
```


 ⚠️ 注意事项

1. 合规使用：本合集所有爬虫仅供学习研究使用，请遵守各网站的 `robots.txt` 规定
2. 请求频率：请合理控制请求间隔，避免对目标网站造成压力
3. 编码问题：当当网搜索关键词需转为 `gb2312` 编码，注意区分
4. 数据库配置：所有项目均需在代码中修改数据库连接信息
5. HTML解析：`FetchWeapon_Use` 项目需准备本地 HTML 文件作为输入源


 📝 更新日志

| 版本 | 日期 | 更新内容 |
|------|------|----------|
| v1.0 | 2026-07 | 新增新华网教育新闻爬虫（curl_cffi 指纹伪装） |
| v1.1 | 2026-07 | 新增当当网图书爬虫（分页抓取 + 批量入库） |
| v1.2 | 2026-07 | 新增 HTML解析工具对比（lxml / BeautifulSoup / PyQuery / html5lib） |


 👤 作者

WuSh · [GitHub](https://github.com/WuSh-uai)


 📄 License

本项目仅供学习交流使用，请勿用于商业用途。
```
