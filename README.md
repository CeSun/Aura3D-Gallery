# Aura3D Gallery（Web）

Aura3D 引擎的示例浏览器站点的**部署仓库**。线上地址：<https://cesun.github.io/Aura3D-Gallery/>

访问时请带上结尾的斜杠。站点用相对路径加载 `_framework/dotnet.js`，资产根也从页面 URL 推导，
所以 `/Aura3D-Gallery`（无斜杠）这种地址可能解析到站点根而 404。

## 这个仓库里没有什么

没有源码，也没有构建产物。站点内容全部由 `.github/workflows/deploy-pages.yaml` 现场构建产出，
只活在 Pages 的 artifact 里——一次 `wwwroot` 实测 108MB（`_framework` 84MB + `assets` 24MB），
提交进 git 历史几次就是几百 MB。

## 源码在哪

`CeSun/Aura3D` 的 `gallery/Aura3D.Gallery.Browser`（浏览器宿主）+ `gallery/Aura3D.Gallery`（共享示例页面）。
要改内容请去那个仓库，这里的 workflow 只负责把它发出来。

## 上线规则

质量门禁（四个宿主构建、资产预算、CJK 字体覆盖、核心测试）全在 Aura3D 的 CI 里，
**能进 `main` 的就是能上线的**。本仓库的 workflow 不复述任何检查，只有三种触发：

| 触发 | 行为 |
| --- | --- |
| `schedule`（每 6 小时） | 比对 `main` 的提交号与线上 `source-sha.txt`，不同才构建 |
| `workflow_dispatch` | 立即构建，可指定要构建的 ref（分支名或完整 40 位提交号） |
| `repository_dispatch`（`deploy-gallery`） | 留给「合并瞬间上线」，需要 Aura3D 侧先配一个能写本仓库的跨仓库凭据 |

## 一次性准备

Settings → Pages → **Build and deployment** → Source 选 **GitHub Actions**。
不选的话 `configure-pages` 这一步会直接报错——Pages 没有从 Actions 启用过，产物无处可部署。

## 线上跑的是哪一版

```
curl -s https://cesun.github.io/Aura3D-Gallery/source-sha.txt
```

这个文件由构建时烙进 `wwwroot`，是定时任务判断「要不要重发」的唯一依据。
