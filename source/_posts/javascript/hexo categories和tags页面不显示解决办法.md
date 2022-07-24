---
title: hexo categories和tags页面不显示解决办法
date: 2022-03-19 16:28:03
categories: 前端
tags: [hexo]
---
## 一、配置`categories`

**1\. 新建页面`categories`:**

```
hexo new page "categories"
```

执行命令后将新生成文件夹`categories`，并在该文件夹下生成`index.md`文件。

**2\. 编辑`categories/index.md`文件:**

```
vi {path}/source/categories/index.md
```

编辑内容：

```
---
title: 分类
date: 2017-10-25 22:00:00
layout: "categories"
---
```

**3\. 编辑主题配置文件`themes/pure/_config.yml`:**

```
vi {path}/themes/pure/_config.yml
```

将`menu`中的`categories`的注释去掉：

```
# menu
menu:
  Home: .
  Archives: archives  # 归档
  Categories: categories  # 分类
  #Tags: tags  # 标签
  # Repository: repository  # github repositories
  #Books: books  # 豆瓣书单
  #Links: links  # 友链
  About: about  # 关于
```

**4\. 编辑文章在`Front-matter`区域（即`---`分隔的区域）指定`categories`即可：**

```
---
title: test
date: 2017-01-01 00:00:00
categories:
 - 类别名称
tags:
 - 标签
--- 
```

分类具有顺序性和层次性，而标签没有顺序和层次。

## [](https://blog.lanweihong.com/posts/765/#%E4%BA%8C%E3%80%81%E9%85%8D%E7%BD%AEtags "二、配置tags")二、配置`tags`

**1\. 新建页面`tags`:**

```
hexo new page "tags"
```

**2\. 编辑`tags/index.md`文件:**

```
vi {path}/source/tags/index.md
```

编辑内容：

```
---
title: 标签
date: 2017-10-25 22:05:00
layout: "tags"
---
```

**3\. 编辑主题配置文件`themes/pure/_config.yml`:**

```
vi {path}/themes/pure/_config.yml
```

将`menu`中的`tags`的注释去掉：

```
# menu
menu:
  Home: .
  Archives: archives  # 归档
  Categories: categories  # 分类
  Tags: tags  # 标签
  # Repository: repository  # github repositories
  #Books: books  # 豆瓣书单
  #Links: links  # 友链
  About: about  # 关于
```

文章指定`tags`的写法与`categories`一致，在`Front-matter`区域指定即可，写法可参考以上。

## 三、配置`about`

**1\. 新建页面`about`:**

```
hexo new page "about"
```

**2\. 编辑`about/index.md`文件，内容可根据个人编写；**

```
vi {path}/source/about/index.md
```

**3\. 编辑主题配置文件`themes/pure/_config.yml`:**

```
vi {path}/themes/pure/_config.yml
```

将`menu`中的`about`的注释去掉：

```
# menu
menu:
  Home: .
  Archives: archives  # 归档
  Categories: categories  # 分类
  Tags: tags  # 标签
  # Repository: repository  # github repositories
  #Books: books  # 豆瓣书单
  #Links: links  # 友链
  About: about  # 关于
```
