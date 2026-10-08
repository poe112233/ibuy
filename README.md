# ibuy.ores.cc → OE 品牌官网（GitHub Pages）

访问 `https://ibuy.ores.cc/` 自动跳到 `https://oe-brand.app.workbuddy.host/`。

## 组成

| 文件 | 作用 |
| --- | --- |
| `index.html` | 中转页：JS `location.replace` + `<meta http-equiv="refresh">` + 手动按钮，三重兜底 |
| `CNAME` | 内容就一行 `ibuy.ores.cc`，告诉 GitHub Pages 这个仓库绑的是哪个自定义域名 |

## 托管位置

- 仓库：https://github.com/poe112233/ibuy （public，main 分支根目录）
- Pages：已开启，`build_type: legacy`，source = `main` / `/`
- 站点地址：`https://ibuy.ores.cc/`（GitHub 已识别 CNAME）

## DNS

Spaceship → `ores.cc` → DNS → 新增 **CNAME**：

```
主机记录：ibuy
记录值：  poe112233.github.io
```

> GitHub Pages 的 CNAME 目标是固定值 `poe112233.github.io`，不需要等控制台给随机串，这也是选它比 EdgeOne 省事的原因。

DNS 生效后 GitHub 会自动签发 Let's Encrypt 证书（几分钟）。证书就绪后可在仓库 Settings → Pages 勾选 **Enforce HTTPS**（或调 API `PUT /repos/poe112233/ibuy/pages {"https_enforced":true}`）。

## 验证

```bash
nslookup ibuy.ores.cc 8.8.8.8
curl -sI https://ibuy.ores.cc/
# 期望 200（GitHub Pages 是客户端跳转，不会有 301）
```

浏览器访问该域应立刻跳到 OE 官网。

## 改跳转目标

改 `index.html` 里三处 `https://oe-brand.app.workbuddy.host/`（title 无关，主要是 `canonical`、`meta refresh`、`TARGET` 变量、按钮 `href`），推到仓库 main 即可，30～60s 生效。

## 注意

- **GitHub Pages 没有服务端 301**，只有客户端跳转，所以该域返回的是 `200` 而非 `301`。若哪天需要真 301，得换回 EdgeOne Pages（`edgeone.json` 的 `redirects`）或前面套一层支持规则的 CDN。
- 同一个仓库不能再给第二个子域用；要加别的子域就新建仓库。
