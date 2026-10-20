# digestly-feeds

[digestly](https://github.com/juncaifeng/digestly) 的**订阅源市场**——自维护的 RSS/Atom 源清单（OPML）。

挂载本仓库地址，即可在 digestly 市场页浏览这些订阅源并一键订阅。

## 用法：挂载到 digestly

1. 打开 digestly → 市场 → **订阅源市场**
2. 填入本仓库地址：`https://github.com/juncaifeng/digestly-feeds`
3. 点「挂载」——清单里的订阅源会进市场网格，可按标签筛选、搜索、订阅

也可直接挂载 OPML 文件地址：
`https://raw.githubusercontent.com/juncaifeng/digestly-feeds/main/feeds.opml`

## 维护：添加订阅源

清单只有一个文件 [`feeds.opml`](./feeds.opml)。往对应分组里加一条：

```xml
<outline type="rss" text="站点名" title="站点名"
         xmlUrl="https://example.com/feed.xml"
         htmlUrl="https://example.com"/>
```

新分组就再加一层 `<outline text="分组名">…</outline>`。

### 规则

- **只加验证过的源**：提交前确认 `xmlUrl` 能返回 200 且是 RSS/Atom。死链会污染整个市场
- `xmlUrl` 必填（订阅地址），`htmlUrl` 选填（站点首页）
- 分组名会成为 digestly 里的标签，用于市场筛选
- 兼容 RSSHub 网关地址：`xmlUrl` 填 `https://<你的 rsshub 实例>/<路由>` 即可

### 校验清单是否可用

```bash
# 批量探测 xmlUrl 是否存活
grep -o 'xmlUrl="[^"]*"' feeds.opml | sed 's/xmlUrl="//;s/"//' | while read u; do
  code=$(curl -s -L --max-time 15 -o /dev/null -w '%{http_code}' "$u")
  echo "$code  $u"
done
```

## 清单格式

digestly 市场源解析约定：按序找 `feeds.opml` → `feeds.json` → `opml.xml` → `manifest.json`，首个命中生效。本仓库用 OPML（`feeds.opml`）。
