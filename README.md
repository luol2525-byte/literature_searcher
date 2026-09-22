# Literature Searcher v2.3.0

## v2.3 新增：免费优先多 API 摘要翻译 + 全列表头升降序

### 摘要翻译优先级

默认采用“免费优先、失败自动回退”的策略：

1. **MyMemory**：免 API Key，优先尝试。
2. **百度通用文本翻译 API**：填写 APPID + 密钥后启用，可使用账户对应的免费字符额度。
3. **LibreTranslate**：填写兼容服务地址，可连接自建实例或其他可用实例；若实例要求 Key，可同时填写 API Key。
4. **DeepL API**：填写 DeepL API Key 后启用；`:fx` 免费版 Key 会自动使用 `api-free.deepl.com`。
5. **DeepSeek**：作为最后兜底，只有前面的翻译方式失败或译文质量检查未通过时才调用。

程序会对译文做基本有效性检查；空译文、错误消息、几乎未翻译的结果会自动跳过并切换下一 API。英文摘要仍自动翻译为中文，中文摘要自动翻译为英文。翻译在后台线程运行。

> LibreTranslate 是开源翻译 API，但官方公共实例当前可能要求 API Key；因此程序不强制写死公共实例地址，而允许用户自行填写可用或自建地址。

### 结果表每列升序 / 降序

结果表以下列都可以直接点击表头排序：

- 题目
- 期刊全称/来源
- 年份
- JIF
- 引用
- OA/PDF
- DOI
- 数据源
- 相关度

第一次点击列标题使用升序 `▲`，再次点击同一列切换降序 `▼`，未排序列显示 `↕`。年份、JIF、引用次数、相关度使用数值排序，不会出现 `100 < 20` 这种字符串排序问题；缺失值始终排在末尾。OA/PDF 按 `无 < OA < PDF` 的等级排序。

---

# Literature Searcher v2.2.0

## v2.2 新增：中英文双栏摘要自动翻译

选择任意有摘要的文献后，摘要区域会自动形成左右双栏：左栏显示完整原始摘要，右栏显示对应译文。英文摘要自动翻译为简体中文，中文摘要自动翻译为英文。翻译在后台执行，不会阻塞界面；可启用“同步滚动”进行左右对照阅读，也可以点击“重新翻译”刷新当前摘要。

### 翻译设置

在“API / JIF / OA 设置”页可填写 **DeepSeek API Key** 并选择 `deepseek-flash` 或 `deepseek-v4-pro`。填写 Key 时优先使用 DeepSeek；没有 Key 时程序自动使用公共翻译接口作为回退。科研翻译提示会要求保留化学式、缩写、单位、数值、氧化态、晶面指数和光谱符号。

### Excel 导出

Excel 中现在包含“原始摘要 / 中文摘要 / 英文摘要”三列。已经生成的译文会随记录一起保存。

---

﻿# Literature Searcher v2.1（Windows 文献检索桌面程序）

这是一个 Python/Tkinter 桌面程序，可在 Windows 上打包为单文件 `LiteratureSearcher.exe`。

## v2.1 本次新增

### 中英文互译联合检索

默认启用“中英文互译后联合检索”。程序会保留原始检索式，同时把自然语言关键词翻译成另一种语言，再把两套检索式一起发送给所选数据源。布尔结构 `AND / OR / NOT / ()` 会保留。

例如：

```text
析氧反应 AND NiFe NOT 电池
```

会同时使用原式和类似下面的英文式检索：

```text
"oxygen evolution reaction" AND NiFe NOT battery
```

`Ni(OH)2`、`FeNi-LDH`、`OER` 等化学式/缩写会尽量保持原样，不送去翻译。翻译采用 MyMemory 在线翻译接口；如果翻译接口不可用，程序自动退回原始检索式，不影响其他检索。启用该选项时，检索词会发送给翻译服务。

### 期刊名称统一为全称

程序合并多数据源结果时优先保留更完整的期刊名，并内置常见简称规范化，例如：

- `J. Am. Chem. Soc.` → `Journal of the American Chemical Society`
- `Angew Chem Int Ed` → `Angewandte Chemie International Edition`
- `Nat Commun` → `Nature Communications`
- `ACS Catal.` → `ACS Catalysis`

默认还会根据 DOI/ISSN 查询 Crossref 元数据，进一步补全期刊全称；当 Crossref 摘要缺失或过短时，会尝试利用 OpenAlex DOI 元数据补充更完整摘要。若数据库本身没有提供可公开获取的完整摘要，程序不会虚构内容。

### 摘要界面改版

结果表与摘要区域之间的分隔条可直接上下拖动。摘要区另有“扩大摘要/缩小摘要”按钮。底部使用两个标签页：

- **完整摘要**：只显示摘要正文，不做界面截断，可用鼠标滚轮或右侧滚动条上下移动。
- **文献详情**：题目、作者、期刊全称、年份、JIF、引用次数、DOI、官网、PDF/OA 链接、数据源和命中检索式。

Excel 中仍保存完整可获得摘要。

## v2.x 功能

### 1. 结构化检索数据源

程序可将以下来源直接汇总到同一结果表并自动去重：

- OpenAlex
- Crossref
- Semantic Scholar
- Europe PMC
- arXiv
- Web of Science Starter API（需要 Clarivate API Key）
- Google Scholar via SerpAPI（可选第三方接口，需要 SerpAPI Key）

### 2. 网页检索平台

对于没有适合后台批量抓取的公开接口的平台，程序采用“浏览器检索入口”，避免内置不稳定网页爬虫：

- Google Scholar
- 百度学术
- Bing 学术
- X-MOL 文献高级检索

在“Google/百度/Bing/X-MOL 网页检索”标签页勾选平台后点击“打开所选网站检索”。X-MOL 会打开其高级检索页面，并把第一个检索式复制到剪贴板。

### 3. 高级布尔检索

支持：

- `AND`
- `OR`
- `NOT`
- `()` 分组
- `"精确短语"`
- 普通空格默认按 AND 处理

例如：

```text
("oxygen evolution" OR OER) AND NiFe NOT battery
```

化学式内部括号会被保留，例如：

```text
alpha-Ni(OH)2 AND (OER OR electrocatalysis)
```

程序会尽量把检索式发送给支持高级语法的数据源；对于只支持普通文本搜索的数据源，会用正向关键词进行召回，然后在本地按完整布尔表达式二次筛选。

### 4. 新增元数据

结果和 Excel 中新增：

- 摘要
- 引用次数
- Journal Impact Factor（JIF）
- PDF 链接
- Open Access 链接
- 本地相关度评分
- ISSN（内部用于匹配 JCR）

注意：不同数据库的“引用次数”口径不同。多数据库合并同一篇论文时，程序默认保留可获得的最大引用次数，而不是把不同数据库的引用次数相加。

### 5. Journal Impact Factor

官方 Journal Impact Factor 属于 Clarivate Journal Citation Reports 数据。

有两种方式补充 JIF：

1. 在“API / JIF / OA 设置”填写 **Web of Science Journals API Key**，勾选“检索后补充官方 JIF”，并填写对应 JCR 年份。
2. 加载自己的 `.xlsx` / `.csv` 影响因子表。

本地表至少需要两列：

```text
Journal    IF
Nature     50.5
...
```

列名可使用 `Journal/期刊/Source` 和 `IF/JIF/Impact Factor/影响因子` 等常见写法。

如果没有 JCR API 授权或本地 IF 表，JIF 列保持空白，不会伪造影响因子。

### 6. Open Access / PDF

程序会综合 OpenAlex、Semantic Scholar、Europe PMC、arXiv 等来源中的公开全文信息。

如果填写邮箱并启用 Unpaywall 补充功能，程序还会按 DOI 查询 Unpaywall，为缺失记录补充 OA/PDF 链接。

### 7. 筛选

支持：

- 起止年份
- 最低引用次数
- 最低 JIF
- 仅 Open Access
- 仅有 PDF
- 仅保留指定期刊

默认高影响期刊预设（界面直接显示全称）：

```text
Nature; Science; Journal of the American Chemical Society; Angewandte Chemie International Edition; ACS Catalysis
```

程序内置别名：

- `JACS` → `Journal of the American Chemical Society`
- `Angew` → `Angewandte Chemie International Edition`

默认是精确期刊名匹配。例如 `Nature` 只匹配 `Nature`，不会自动匹配 `Nature Catalysis`。

如果需要期刊系列，可使用尾部 `*`，例如：

```text
Nature*
```

### 8. 排序

支持：

- 相关度
- 年份（新 → 旧）
- 引用次数
- 影响因子

相关度为程序本地评分，主要根据检索词在标题、摘要、期刊和作者字段中的命中情况计算，不等同于各数据库自己的排序分值。

## Excel 导出字段

导出的 `.xlsx` 包含：

1. 检索式
2. 题目
3. 作者
4. 期刊全称/来源
5. 年份
6. 影响因子（JIF）
7. 引用次数
8. 相关度
9. 摘要
10. DOI
11. 官网链接
12. PDF 链接
13. Open Access 链接
14. 数据源

官网/PDF/OA 链接均为可点击超链接。

## API 设置

### OpenAlex

可匿名尝试，也可填写 OpenAlex API Key。

### Semantic Scholar

可匿名尝试；频繁检索建议申请 API Key。

### Crossref

建议填写联系邮箱，以便使用 polite pool。同时该邮箱也可用于 Unpaywall API。

### Web of Science Starter API

必须填写 Clarivate WoS Starter API Key。WoS Starter API 可返回论文元数据；引用次数是否可返回取决于你的 API 计划/机构权限。

### Web of Science Journals API

用于获取官方 JCR/JIF，需要单独授权。

### Google Scholar / SerpAPI

Google Scholar 本身不提供批量 API。程序默认提供浏览器检索入口；如果你自愿使用第三方 SerpAPI，可填写 SerpAPI Key 并勾选 `Google Scholar (SerpAPI)`，将结构化结果直接并入表格。

## Windows 直接运行 Python 版

安装 Python 3.11/3.12 后双击：

```text
run_windows.bat
```

或：

```powershell
pip install -r requirements.txt
python literature_searcher.py
```

## 生成 Windows EXE

双击：

```text
build_windows.bat
```

完成后得到：

```text
dist\LiteratureSearcher.exe
```

当前项目不在 Linux 环境交叉编译 Windows PE 文件，以避免生成不可用 EXE。建议直接在 Windows 10/11 上运行 `build_windows.bat`。

## 使用建议

如果你的目标是材料、电催化、OER 等科研主题，建议同时启用：

```text
OpenAlex + Crossref + Semantic Scholar + Web of Science（有 Key 时）
```

生命科学主题可额外启用 Europe PMC；预印本检索可启用 arXiv。

Google Scholar、百度学术、Bing 学术和 X-MOL 更适合做补充覆盖与人工复核。

## 合规说明

本程序不会后台批量抓取 Google Scholar、百度学术、Bing 学术或 X-MOL 的网页 HTML。对于这些平台采用浏览器入口；需要结构化 Google Scholar 数据时，可由用户自行配置 SerpAPI。

请遵守各数据库/API 的使用协议、机构授权和速率限制。


## pip / 代理故障排查（v2.0.1）

如果构建时看到：

```text
ProxyError('Cannot connect to proxy.' ...)
ERROR: Could not find a version that satisfies the requirement requests...
```

通常不是 `requests` 不存在，而是 pip 正在使用一个失效代理，导致它完全没有访问到 PyPI。

新版 `build_windows.bat` 已做两层处理：

1. 只在当前构建窗口中清空 `HTTP_PROXY`、`HTTPS_PROXY`、`ALL_PROXY`、`PIP_PROXY` 等代理环境变量；不会修改 Windows 的永久设置。
2. 构建时设置 `PIP_CONFIG_FILE=NUL`，让 pip 忽略可能含有错误 `proxy` 或 `index-url` 的 `pip.ini`。先访问官方 PyPI，失败后自动尝试清华 PyPI 镜像。

可用以下命令查看本机到底从哪里读到了代理：

```bat
py -m pip config debug
py -m pip config list -v
set | findstr /I "proxy pip_"
```

如果你所在的单位/校园网 **必须** 使用代理，请不要永久删除正确的代理配置；应把代理地址修正为可访问的地址。程序本体默认不继承系统代理，以免打包成功后检索 API 仍然报 `ProxyError`。如果运行 Literature Searcher 时确实必须使用系统代理，可在启动前设置：

```bat
set LITERATURE_SEARCHER_USE_SYSTEM_PROXY=1
python literature_searcher.py
```

或在启动 EXE 前：

```bat
set LITERATURE_SEARCHER_USE_SYSTEM_PROXY=1
LiteratureSearcher.exe
```


## v2.4 大工作区启动界面

v2.4 默认把屏幕空间优先分给“文献结果网格”和“中英文双栏摘要”。Windows 启动时程序会尝试最大化窗口，高级设置区默认折叠。

顶部保留快速检索栏：

```text
检索式 [................................] [添加] [开始检索] [停止] [展开设置 ▼]
```

需要设置年份、引用次数、JIF、期刊筛选、数据源或 API 时，点击“展开设置 ▼”；设置完成后点击“收起设置 ▲”，即可重新把空间让给结果和摘要。

结果网格与摘要区默认约按 54% / 46% 分配高度，并继续支持拖动中间分隔条。摘要区中的英文原文和中文译文仍为左右双栏，各自支持滚动，并可开启同步滚动。
