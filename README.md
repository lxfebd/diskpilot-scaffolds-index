# DiskPilot 插件市场 · 官方社区索引

DiskPilot 桌面端插件市场的官方索引仓库。应用内「设置 → 插件市场 → 索引地址」填：

```
https://cdn.jsdelivr.net/gh/lxfebd/diskpilot-scaffolds-index@main/index.json
```

刷新后即可浏览、安装、更新、回滚社区插件（安装/卸载进回收站，写操作需确认）。

> 为什么默认推荐 jsDelivr CDN：`raw.githubusercontent.com` 的 Fastly 边缘
> IP 段在国内网络常年不可达（`github.com` 主站正常、raw 超时），直接引导它会让
> 市场"拉不到索引"。jsDelivr 自动镜像本仓库 `main` 分支，内容同源、国内可访问。
> 用不了 CDN 的环境可改填官方原始地址：
> `https://raw.githubusercontent.com/lxfebd/diskpilot-scaffolds-index/main/index.json`
>（两类地址内容完全一致，`index.json` 信封里的 `source_url` 一律标注官方原始源）。

## 这是什么

`index.json` 是一个 schema 1 的信封（契约见主仓库 [`docs/plugin-market-index.md`](https://github.com/lxfebd/diskpilot/blob/main/docs/plugin-market-index.md)）：

```json
{
  "schema": 1,
  "plugins": [
    { "id": "...", "name": "...", "version": "1.0.0", "author": "...",
      "description": "...", "category": "...", "risk": "low",
      "url": "https://.../plugin-1.0.0.zip", "sha256": "...", "signer": "" }
  ]
}
```

- `url` 必须 https 直链（GitHub Releases 最省事）。
- `sha256` 期望值，客户端下载后强校验。
- 可选 Ed25519 签名：对 `plugins[]` 的规范 JSON 做签名（无签名时市场显示"未验签"）。

## 发布新插件

1. 用主仓库的 `export_plugin_zip` 把插件目录打成 zip（内含 `tool.plugin.json`）。
2. 上传 zip 到 GitHub Releases（本仓库或作者自己的仓库均可），拿 https 直链。
3. 在 `index.json` 的 `plugins[]` 加一条：`id`（kebab-case）/`name`/`version`/`author`/`description`/`category`/`risk`/`url`/`sha256`/`license`。
4. 提 PR。CI 自动校验：JSON 可解析、schema=1、必填字段齐全、url 均为 https。

### 发布清单

- [ ] zip 能被 `install_plugin_zip` 安装（含合法 `tool.plugin.json`）
- [ ] `url` 是 https 直链
- [ ] `sha256` 与 zip 实际哈希一致
- [ ] `id` 是合法 kebab-case，不与内置工具净化名撞车

## 重新生成 index.json

内置工具的真实元数据在主仓库 `crates/toolbelt/assets/tool_manifests.json`：

```bash
python scripts/gen-index.py > index.json
```

新插件条目手动 append 到 `plugins[]` 即可（发布者填直链后，`url` 不再是空串）。

## CI

`.github/workflows/ci.yml` 在每次 PR/推送校验 `index.json` 契约，不通过不开 PR。

## License

MIT
