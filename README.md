# kian-home

一个自己搭的单文件静态站，兼作跨设备的待办清单。在线地址：

**https://kian-7.github.io/kian-home/**

## 里面有什么

| 子页 | 内容 |
| --- | --- |
| `#/home` | 首页概览 |
| `#/research` | 文献调研：ROI 材料赋值这条线上的核心文献，带 DOI 直达 |
| `#/manual` | 行动手册：研究方向分析、导师沟通准备、基准算例复现、14 天计划 |
| `#/todo` | 待办清单，可跨电脑同步 |
| `#/about` | 关于本站 |

全站只有 `index.html` 一个文件，靠 `#/xxx` 哈希路由切换子页，样式和脚本都内联在里面。
没有构建步骤、没有依赖、没有外部资源——改完直接推上去就生效。

## 怎么改

**方式一（推荐）：本地改，然后推**

改动 `index.html`，然后运行仓库外面的 `push-to-github.sh`（脚本会读本机的口令文件，把文件推到仓库）。

**方式二：直接在 GitHub 网页上改**

在仓库里点 `index.html` → 右上角铅笔图标 → 改 → Commit changes。
提交后 Pages 会自动重新发布，大约 1 分钟生效。

**加一个新子页要动三处**（文件开头注释里也写了）：

1. `<nav>` 里加一条 `<a href="#/newpage" data-nav="newpage">标题</a>`
2. `<main>` 里加一块 `<section class="page" id="page-newpage"> … </section>`
3. 底部 `ROUTES` 对象里加 `'newpage': '标题'`

颜色统一走 `:root` 里的 CSS 变量，改一处全站生效。

## 待办清单怎么跨电脑同步

GitHub Pages 只能托管静态文件，没有数据库，所以清单走 **GitHub API 写回本仓库的 `todos.json`**。
只有点「立即同步」才会写仓库；平时编辑只动本地。

**在两台电脑上各配一次**（每台浏览器各存各的凭证）：

| 字段 | 填什么 |
| --- | --- |
| 仓库 | `Kian-7/kian-home` |
| 分支 | `main` |
| 数据文件路径 | `todos.json` |
| Token | 你自己的 fine-grained token，见下 |

**Token 怎么建**

1. 打开 https://github.com/settings/personal-access-tokens/new
2. Token name 随便写，比如 `kian-home-sync`；Expiration 选 90 days
3. Repository access 选 **Only select repositories** → 勾 `kian-home`
4. Permissions → Repository permissions：
   - **Contents: Read and write**（读写 `todos.json`，必需）
   - **Pages: Read and write**（只有开通/改 Pages 时需要，可留只读）
   - Metadata 会自动带上 Read-only
5. Generate token，复制 `github_pat_…` 那一串
6. 回到待办页，粘进 Token 框，点「保存配置」

**行为说明**

- 两台电脑各自打开页面时会**静默拉取一次**（只读，不写仓库），所以换电脑能看到最新清单。
- 编辑只改本地；想同步出去就点「立即同步」——它会先拉远端、按 `updated` 时间戳合并（同一 id 取更新的那条），再整体写回。
- 删除用的是**墓碑标记**（`del: true`）而不是真删，否则另一台电脑上的旧副本会在下次合并时把它复活。
- 「清空已完成」同理，也是打墓碑。

## 安全与隐私

- 仓库是**公开**的（免费账号的 GitHub Pages 要求公开仓库），所以 `todos.json` 里的内容任何人读得到。**别往清单里写敏感信息。**
- Token 只存在你自己浏览器的 localStorage 里，不在网页文件里、不在仓库里。共用电脑用完点「清除凭证」。
- Token 泄露了就回 https://github.com/settings/personal-access-tokens 撤销重建，然后两台电脑重新填一次。
- 文献的版权归原作者与出版方，站内只做个人学习索引。

## 本地开发

没有构建步骤。直接双击 `index.html` 就能看效果（`file://` 下同步功能用不了，因为浏览器会拦跨域请求，其余都正常）。

想本地起服务：

```bash
python -m http.server 8000
```

然后开 http://localhost:8000/ 。注意：本地起的服务里配同步，填的仓库和线上是同一套，凭证存在浏览器里跟域名绑定，`localhost` 和 `kian-7.github.io` 需要各配一次。
