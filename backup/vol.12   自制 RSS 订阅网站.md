# 自制 RSS 订阅网站

`follow` 是一个很好用的工具，可以订阅一些有价值的网站。然而，许多网页并未配置 RSS 订阅源，因此我们需要自己创建 RSS 以便订阅更新。

以下是几种常见的自制 RSS 方法：

---

## 1. 使用 RSS 生成工具

如果不想自己编写代码，可以使用在线 RSS 生成工具，它们大多免费，且易于使用。

- **RSS.app**（免费版有一定限制）：
  - 可以自动抓取网站的更新，并生成 RSS 订阅源。
  - [[官网链接](https://rss.app/)](https://rss.app/)

- **Feed43**（免费版每日生成次数有限）：
  - 支持自定义抓取规则，可将网页的特定部分提取为 RSS。
  - [[官网链接](https://feed43.com/)](https://feed43.com/)

- **FiveFilters**（免费版提供基础功能）：
  - 提供网页到 RSS 的转换服务。
  - [[官网链接](https://fivefilters.org/)](https://fivefilters.org/)

这些工具适合没有开发经验的用户，可以帮助你轻松生成 RSS 订阅源。

---

## 2. 手动创建 RSS（适合小范围使用）

可以手动创建 RSS 文件，并在网站更新时手动修改 XML 内容。这种方式适用于更新不频繁的网站。

### **RSS 基本结构示例**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<rss version="2.0">
  <channel>
    <title>网站名称</title>
    <link>https://example.com</link>
    <description>这是一个 RSS 订阅源</description>
    <item>
      <title>新文章标题</title>
      <link>https://example.com/post/1</link>
      <description>新文章的简短描述</description>
      <pubDate>Mon, 12 Feb 2025 12:00:00 GMT</pubDate>
    </item>
  </channel>
</rss>
```

可以将该文件托管在 GitHub Pages、Vercel 或其他免费服务器上，供自己或他人订阅。

---

## 3. 使用 Python 脚本自动生成 RSS

如果有编程经验，可以编写 Python 脚本来抓取网页内容，并自动生成 RSS 订阅源。

### **示例代码**

```python
import requests
from bs4 import BeautifulSoup
from feedgen.feed import FeedGenerator

def get_new_posts():
    url = 'https://example.com/blog'
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    posts = []

    for item in soup.select('.post-item'):  # 根据网站结构调整选择器
        title = item.select_one('.post-title').get_text()
        link = item.select_one('a')['href']
        posts.append({'title': title, 'link': link})

    return posts

def generate_rss(posts):
    fg = FeedGenerator()
    fg.title('我的网站 RSS')
    fg.link(href='https://example.com', rel='self')
    fg.description('自动生成的 RSS 订阅')

    for post in posts:
        item = fg.add_entry()
        item.title(post['title'])
        item.link(href=post['link'])
        item.description('内容简介')

    return fg.rss_str(pretty=True)

if __name__ == '__main__':
    posts = get_new_posts()
    rss_feed = generate_rss(posts)

    with open('rss.xml', 'wb') as f:
        f.write(rss_feed)
```

可以定期运行该脚本，让 RSS 自动更新。

---

## 4. 使用浏览器扩展

如果不想自己动手，可以使用一些浏览器扩展工具监控网页更新，并生成 RSS 订阅源。

- **Distill Web Monitor**
  - 可监控网页变化，并在内容变化时发送通知或生成 RSS 订阅源。
  - [[官网链接](https://distill.io/)](https://distill.io/)

---

## 5. 自建 RSS 生成服务

如果希望更灵活地管理订阅，可以尝试自己搭建 RSS 生成服务。

- **RSSHub**（功能强大，支持多种网站）
  - [[项目地址](https://github.com/DIYgod/RSSHub)](https://github.com/DIYgod/RSSHub)
  - [[使用教程](https://sspai.com/post/81786)](https://sspai.com/post/81786)

- **RSS-Bridge**（适合高级用户）
  - [[项目地址](https://github.com/RSS-Bridge/rss-bridge)](https://github.com/RSS-Bridge/rss-bridge)

- **RSSFunnel**（更轻量级的选择）
  - 相关介绍：[[点击这里](https://meta.appinn.net/t/topic/66342/2)](https://meta.appinn.net/t/topic/66342/2)

---
