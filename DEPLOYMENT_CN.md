# 香港插件市场部署记录

部署日期：2026-10-25。

| 项目 | 当前值 |
| --- | --- |
| 市场目录 | https://skill.artai.top/marketplace.json |
| 域名入口 | https://skill.artai.top/（跳转市场目录） |
| 源站 | `rainyun-hong-kong` / `154.9.253.50` |
| Fork 仓库 | https://github.com/OpenX123/zcode-plugins |
| 上游仓库 | https://github.com/zai-org/zcode-plugins |
| 新增技能源码提交 | `15dfff3c8cc4536b51b468bc25dace1246dbd9ac` |
| 复用制品的上游提交 | `e41948941d161e4e52782050e3f5ecec21ce3713` |
| 市场身份 | `zcode-plugins-official` |
| 发布内容 | 54 个插件 ZIP、49 个图标 |
| 当前发布目录 | `/opt/1panel/www/sites/skill.artai.top/releases/20261025-15dfff3` |
| 当前站点链接 | `/opt/1panel/www/sites/skill.artai.top/index` |
| OpenResty 配置 | `/opt/1panel/www/conf.d/skill.artai.top.conf` |
| 初始配置备份 | `/opt/backups/skill-artai/20261025-initial` |
| 证书续期钩子 | `/etc/letsencrypt/renewal-hooks/deploy/skill-artai.sh` |
| 制品准备脚本与上游目录快照 | `/opt/skill-artai-deploy/` |

原有 51 个插件复用官方 CDN 已编译的同版本发布制品，版本逐一与上游源码一致，插件包内容保持原样。本次新增 `ponytail`、`ask-matt` 和 `ui-ux-pro-max` 三个社区技能插件，版本均为 `0.1.0`；使用仓库现有打包器生成 ZIP，保留 MIT 许可与来源说明。全部 54 个 ZIP 均检查了文件大小、SHA-256、ZIP 完整性与包内名称/版本。分发目录中的安装包和图标 URL 使用本域名。

市场身份沿用既有值，使内置插件合并、已安装插件和更新检查继续使用同一身份。ZCode 默认市场地址位于另一仓库的 `packages/shared/src/plugin-marketplaces.ts`；本机已有市场注册记录也已更换来源并备份至 `~/.zcode/cli/backups/skill-artai-20261025T095209Z/`。用户显式添加的其他市场保留。

Cloudflare 公网链路和使用 `--resolve skill.artai.top:443:154.9.253.50` 的源站直连均返回同一份目录，TLS 校验通过。公网显示 Cloudflare 的代理 IP；本次用站点专属 HTTP 验证文件确认实际回源香港服务器。

发布目录中 `marketplace.json` 的 SHA-256：

```text
7746937fcbc2028629f5422bd2a526340c965136251a4e1117c76e5cf37d1bf3
```

证书由 Let's Encrypt 签发，首次证书到期日为 2027-01-23。系统 `certbot.timer` 已启用；续期钩子仅处理此域名，保留历史证书版本并平滑加载 OpenResty。HTTP ACME 路径独立于插件发布目录，`certbot renew --cert-name skill.artai.top --dry-run --no-random-sleep-on-renew` 模拟续期已通过。

本次创建独立发布目录并原子替换 `index` 链接，未修改 OpenResty 配置。旧发布 `releases/20261025-e419489` 完整保留，可通过原子切换链接回滚；原链接记录位于 `/opt/skill-artai-deploy/update-20261025-15dfff3/previous-link.json`。新增包、源目录快照与准备脚本也保留在该更新目录。后续不要覆盖既有版本的插件 ZIP。初始配置备份保存的是首次证书验证阶段的 HTTP 配置。

验证范围：54 条目录通过 `scripts/validate.py`；UI 数据完整性检查通过，36 项既有检索测试与 24 项目录分类/打包安全测试通过。三个新增插件通过现有打包器生成制品，并由真实 ZCode 适配器在隔离目录中从公网下载安装、枚举组件和加载技能；安装后的 UI 设计系统检索脚本成功运行。公网与香港源站 HTTPS 目录摘要一致。未在用户真实配置中安装或启用这三个插件，也未运行实际编码/设计对话。

本次为纯 Skill 插件变更，没有执行全仓库 `pnpm install`、`pnpm build` 或重编既有 Craft WASM；新增三包独立运行现有 `build_dist` 发布入口，既有编译制品保持原样。通用 `skill-creator` frontmatter 校验器不接受上游的 `argument-hint` 和 `disable-model-invocation` 扩展字段；目标 ZCode 的实际发现与加载检查通过。Ask Matt 保留手动调用约定及原始元数据，并在正文明确调用边界；当前宿主对这些扩展字段的强制调用策略支持不在本次变更范围。
