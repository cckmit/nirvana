---
title: GitHub 常用搜索语法
date: 2021-03-09 21:19:18
categories: git
---
**[官网](https://docs.github.com/cn/github/searching-for-information-on-github/searching-for-repositories)**

# 搜索仓库

您可以在 GitHub 上搜索仓库，并使用这些仓库搜索限定符的任意组合缩小结果范围。
您可以在所有 GitHub 内全局搜索仓库，也可以在特定组织内搜索仓库
要在搜索结果中包括复刻，您需要将 `fork:true` 或 `fork:only` 添加到查询。 

**Tips:**

*   有关可以添加到任何搜索限定符以进一步改善结果的搜索语法列表，请参阅“[了解搜索语法](https://docs.github.com/cn/free-pro-team@latest/articles/understanding-the-search-syntax)”。
*   对多个字词的搜索词使用引号。 例如，如果要搜索具有标签 "In progress" 的议题，可搜索 `label:"in progress"`。 搜索不区分大小写。

### 按仓库名称、说明或自述文件内容搜索
通过 `in` 限定符，您可以将搜索限制为仓库名称、仓库说明、自述文件内容或这些的任意组合。 如果省略此限定符，则只搜索仓库名称和说明。

| 限定符 | 示例 |
| --- | --- |
| `in:name` | **jquery in:name** matches repositories with "jquery" in the repository name. |
| `in:description` | **jquery in:name,description** matches repositories with "jquery" in the repository name or description. |
| `in:readme` | **jquery in:readme**  matches repositories mentioning "jquery" in the repository's README file. |
| `repo:owner/name` | **repo:octocat/hello-world** 匹配特定仓库名称。 |

### 基于仓库的内容搜索
除了使用 `in:readme` 以外，无法通过搜索仓库内的特定内容来查找仓库。 要搜索仓库内的特定文件或内容，您可以使用查找器或代码特定的搜索限定符。
| 限定符 | 示例 |
| --- | --- |
| `in:readme` | **octocat in:readme** matches repositories mentioning "octocat" in the repository's README file. |

### 在用户或组织的仓库内搜索

要在特定用户或组织拥有的所有仓库中搜索，您可以使用 `user` 或 `org` 限定符。

| 限定符 | 示例 |
| --- | --- |
| `user:*USERNAME*` | **user:defunkt forks:>100** 匹配来自 @defunkt、拥有超过 100 复刻的仓库。 |
| `org:*ORGNAME*` | **org:github** 匹配来自 GitHub 的仓库。 |

### 按仓库大小搜索

The `size` qualifier finds repositories that match a certain size (in kilobytes), using greater than, less than, and range qualifiers
| 限定符 | 示例 |
| --- | --- |
| `size:*n*` | **size:1000** 匹配恰好为 1 MB 的仓库。 |
|  | **size:>=30000** 匹配至少为 30 MB 的仓库。 |
|  | **size:<50** 匹配小于 50 KB 的仓库。 |
|  | **size:50..120** 匹配介于 50 KB 与 120 KB 之间的仓库。 |

### 按关注者数量搜索

| 限定符 | 示例 |
| --- | --- |
| `followers:*n*` | **node followers:>=10000** 匹配有 10,000 或更多关注者提及文字 "node" 的仓库。 |
|  | **styleguide linter followers:1..10** 匹配拥有 1 到 10 个关注者并且提及 "styleguide linter" 一词的的仓库。 |

### 按复刻数量搜索
| 限定符 | 示例 |
| --- | --- |
| `forks:*n*` | **forks:5** 匹配具有至少 205 个复刻的仓库。 |
|  | **forks:<90** 匹配具有少于 90 个复刻的仓库。 |
|  | **forks:10..20** 匹配具有 10 到 20 个复刻的仓库。 |

### 按星号数量搜索


| 限定符 | 示例 |
| --- | --- |
| `stars:*n*` | **stars:500** 匹配恰好具有 500 个星号的仓库。 |
|  | **stars:10..20** 匹配具有 10 到 20 个星号、小于 1000 KB 的仓库。 |
|  | **stars:>=500 fork:true language:php** 匹配具有至少 500 个星号，包括复刻的星号（以 PHP 编写）的仓库。 |

### 按仓库创建或上次更新时间搜索

您可以基于创建时间或上次更新时间过滤仓库。 对于仓库创建，您可以使用 `created` 限定符；要了解仓库上次更新的时间，您要使用 `pushed` 限定符。 `pushed` 限定符将返回仓库列表，按仓库中任意分支上最近进行的提交排序。

两者均采用日期作为参数。 日期格式必须遵循 [ISO8601](http://en.wikipedia.org/wiki/ISO_8601)标准，即 `YYYY-MM-DD`（年-月-日）。 您也可以在日期后添加可选的时间信息 `THH:MM:SS+00:00`，以便按小时、分钟和秒进行搜索。 这是 `T`，随后是 `HH:MM:SS`（时-分-秒）和 UTC 偏移 (`+00:00`)。

| 限定符 | 示例 |
| --- | --- |
| `created:*YYYY-MM-DD*` | **webos created:<2011-01-01** 匹配具有 "webos" 字样、在 2011 年之前创建的仓库。 |
| `pushed:*YYYY-MM-DD*` | **css pushed:>2013-02-01** 匹配具有 "css" 字样、在 2013 年 1 月之后收到推送的仓库。 |
|  | **case pushed:>=2013-03-06 fork:only** 匹配具有 "case" 字样、在 2013 年 3 月 6 日或之后收到推送并且作为复刻的仓库。 |

### 按语言搜索

You can search repositories based on the language of the code in the repositories.

| 限定符 | 示例 |
| --- | --- |
| `language:*LANGUAGE*` | **rails language:javascript** 匹配具有 "rails" 字样、以 JavaScript 编写的仓库。 |

### 按主题搜索

| 限定符 | 示例 |
| --- | --- |
| `topic:*TOPIC*` | **topic:jekyll** 匹配已归类为 "jekyll" 主题的仓库。 |

### 按主题数量搜索

| 限定符 | 示例 |
| --- | --- |
| `topics:*n*` | **topics:5** 匹配具有五个主题的仓库。 |
|  | **topics:>3**  匹配超过三个主题的仓库。 |

### 按许可搜索
| 限定符 | 示例 |
| --- | --- |
| `license:*LICENSE_KEYWORD*` | **license:apache-2.0**  匹配根据 Apache License 2.0 授权的仓库。 |

### Search by repository visibility
You can filter your search based on the visibility of the repositories. 

| Qualifier | Example | 
|------------- | ------------- | 
| `is:public` | **is:public org:github**  matches public repositories owned by GitHub. |
| `is:internal` | **is:internal test** matches internal repositories that you can access and contain the word "test". |
| `is:private` | **is:private pages**  matches private repositories that you can access and contain the word "pages."|

### 基于仓库是否为镜像搜索


| 限定符 | 示例 |
| --- | --- |
| `mirror:true` | **mirror:true GNOME**  matches repositories that are mirrors and contain the word "GNOME." |
| `mirror:false` | **mirror:false GNOME**  matches repositories that are not mirrors and contain the word "GNOME." |

### 基于仓库是否已存档搜索

| 限定符 | 示例 |
| --- | --- |
| `archived:true` | **archived:true GNOME**  matches repositories that are archived and contain the word "GNOME." |
| `archived:false` | **archived:false GNOME** matches repositories that are not archived and contain the word "GNOME." |

### 基于具有 `good first issue` 或 `help wanted` 标签的议题数量搜索

您可以使用限定符 `help-wanted-issues:>n` 和 `good-first-issues:>n` 搜索具有最少数量标签为 `help-wanted` 或 `good-first-issue` 议题的仓库。。

| 限定符 | 示例 |
| --- | --- |
| `good-first-issues:>n` | **good-first-issues:>2 javascript**  匹配具有超过两个标签为 `good-first-issue` 的议题且包含 "javascript" 字样的仓库。 |
| `help-wanted-issues:>n` | **help-wanted-issues:>4 react**  匹配具有超过四个标签为 `help-wanted` 的议题且包含 "React" 字样的仓库。 |
