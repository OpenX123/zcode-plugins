# 香港插件市场部署记录

最近更新：2026-11-02（首次部署：2026-10-25）。

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
| 发布内容 | 54 个插件 ZIP、54 个图标 |
| 当前发布目录 | `/opt/1panel/www/sites/skill.artai.top/releases/20261102-icons` |
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
f466ffbaca0a51f21edb0132888a24ece5b1c6b25fd7966e3895badbe45fd05a
```

证书由 Let's Encrypt 签发，首次证书到期日为 2027-01-23。系统 `certbot.timer` 已启用；续期钩子仅处理此域名，保留历史证书版本并平滑加载 OpenResty。HTTP ACME 路径独立于插件发布目录，`certbot renew --cert-name skill.artai.top --dry-run --no-random-sleep-on-renew` 模拟续期已通过。

本次创建独立发布目录并原子替换 `index` 链接，未修改 OpenResty 配置。旧发布 `releases/20261025-e419489` 完整保留，可通过原子切换链接回滚；原链接记录位于 `/opt/skill-artai-deploy/update-20261025-15dfff3/previous-link.json`。新增包、源目录快照与准备脚本也保留在该更新目录。后续不要覆盖既有版本的插件 ZIP。初始配置备份保存的是首次证书验证阶段的 HTTP 配置。

验证范围：54 条目录通过 `scripts/validate.py`；UI 数据完整性检查通过，36 项既有检索测试与 24 项目录分类/打包安全测试通过。三个新增插件通过现有打包器生成制品，并由真实 ZCode 适配器在隔离目录中从公网下载安装、枚举组件和加载技能；安装后的 UI 设计系统检索脚本成功运行。公网与香港源站 HTTPS 目录摘要一致。未在用户真实配置中安装或启用这三个插件，也未运行实际编码/设计对话。

本次为纯 Skill 插件变更，没有执行全仓库 `pnpm install`、`pnpm build` 或重编既有 Craft WASM；新增三包独立运行现有 `build_dist` 发布入口，既有编译制品保持原样。通用 `skill-creator` frontmatter 校验器不接受上游的 `argument-hint` 和 `disable-model-invocation` 扩展字段；目标 ZCode 的实际发现与加载检查通过。Ask Matt 保留手动调用约定及原始元数据，并在正文明确调用边界；当前宿主对这些扩展字段的强制调用策略支持不在本次变更范围。

## 2026-11-02 图标补齐与发布门禁

原市场中的 `ask-matt`、`ponytail`、`ui-ux-pro-max`、`jev-decisions`、`ruic-card` 没有 `icon` 字段。已补齐 5 张 256px PNG：Ponytail 复用上游项目 logo；Ask Matt、UI UX Pro Max 和 Jev Decisions 复用上游作者或组织头像；RuiC 上游只有默认头像，改用本仓库绘制的全息卡片图标。图片及来源、变换和 SHA-256 均位于 `assets/`。

`validate.py` 和 `build_dist.py` 共用图标校验，拒绝缺字段、非 HTTPS 地址、缺失文件、链接文件、损坏 PNG 和非正方形画布。发布目录使用 `CDN_BASE_URL` 下的本地资源。CI 在编译前执行图标与登记回归测试；缺图标时停止发布。图标不进入插件安装包，因此本次插件版本、54 个 ZIP 的大小和 SHA-256 均保持不变。

服务器切换前校验了旧发布和回滚记录。新发布目录复制自旧发布，只更新市场图标与共享资源；原发布 `releases/20261025-15dfff3` 完整保留。回滚信息和更新包保存在 `/opt/skill-artai-deploy/icon-update-20261102T085200Z/`。**`index` 必须使用相对链接 `releases/<发布名>`**：OpenResty 的容器路径是 `/www/sites/...`，宿主机绝对链接在容器内无法解析。本次绝对链接切换后的 HTTP 检查曾返回 404，已立即回滚恢复 200，再使用相对链接完成发布。

验收：公网和香港源站 HTTPS 目录摘要与上述 SHA-256 一致；54/54 公网图标下载及 PNG 校验通过，新增 5 图标与源码摘要一致；真实运行的 YCode 页面刷新后，5 个插件卡片均有已加载的图片。33 项图标、登记、打包安全与 MCP 清单回归通过；Lint 为 0 错误、1 项既有警告。全量 `pnpm typecheck` 未通过：OpenPencil 上游依赖安装长时间无进展后终止，重试也因 Bun 安装失败退出；排除 OpenPencil 后其他 15 个工作区的类型检查通过。未重编未改动的 Craft/WASM 安装包。
