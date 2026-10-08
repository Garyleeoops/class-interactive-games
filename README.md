# Class Interactive Games · 课堂互动游戏

为研究生《学术论文写作》课准备的课堂互动小游戏网站。纯原生 HTML/CSS/JS，无构建、无依赖，托管于 GitHub Pages，浏览器直接打开即用。

## 页面结构

| 路径 | 说明 |
|---|---|
| `index.html` | 网站主文件（首页游戏中心 + 三个排序游戏，页内 hash 路由切换） |
| `games/abstract-game.html` | Abstract 结构闯关（独立游戏文件，由主文件 iframe 加载） |
| `games/title-game.html` | Title Match 挑战（独立游戏文件，由主文件 iframe 加载） |

- 首页：`#home`（默认）—— 游戏列表与课堂使用提示
- 排序游戏（单文件内嵌）：
  - `#game` —— Cover Letter 排序挑战（期刊投稿信句子排序，21 句）
  - `#game-intro` —— Introduction 排序挑战（论文引言，12 句）
  - `#game-conclusion` —— Conclusion 排序挑战（论文结论，10 句）
- 复杂游戏（独立文件，iframe 懒加载）：
  - `#game-abstract` —— Abstract 结构闯关（迷宫 + 摘要结构分类，5 关）
  - `#game-title` —— Title Match 挑战（台阶 + 摘要选标题，5 关）

五个游戏的句子/论文内容互不重复。两个复杂游戏各自带姓名登录与 localStorage 防重复机制（每人限玩一次、可续玩）。注意：Abstract 与 Title 两个游戏共用同一批 5 篇论文素材（一个做结构分类、一个做标题匹配），如需彻底隔离可更换其中一方的素材。

## 课堂使用

- **投影模式**：浏览器打开网站 → 点击游戏卡片进入，请学生上台操作，全班判断对错。
- **学生模式**：把部署后的网址发给学生，手机浏览器打开即可，支持触屏拖拽/点选。
- **演示模式**：网址后加 `?demo=win`（如 `https://<用户名>.github.io/class-interactive-games/?demo=win`）直接展示通关庆祝画面，适合课前演示。

## 本地预览

```bash
python -m http.server 8777
# 浏览器打开 http://127.0.0.1:8777/
```

> 注意：直接双击用 `file://` 打开不影响玩法，但为了与线上行为一致，建议用本地服务器预览。

## 部署到 GitHub Pages

仓库已包含 `.github/workflows/pages.yml`，push 到 `main` 分支后自动构建部署。只需在仓库开启一次 Pages 权限：

1. 推送代码到 GitHub 仓库 `main` 分支；
2. 打开仓库 **Settings → Pages**；
3. **Build and deployment → Source** 选择 **GitHub Actions**；
4. 等待 1–2 分钟，访问 `https://<用户名>.github.io/class-interactive-games/`。

## 修改游戏内容

排序游戏的所有句子都在 `index.html` 的 `<script>` 中对应数组里（已按正确顺序排列）：

| 游戏 | 数据数组 |
|---|---|
| Cover Letter | `SENTENCES_COVER` |
| Introduction | `SENTENCES_INTRO` |
| Conclusion | `SENTENCES_CONCL` |

直接编辑文字即可，游戏会自动适应卡片数量、进度条与编号，无需改其他代码。

Abstract / Title 两个复杂游戏的内容（5 篇论文的标题、摘要、句子块与干扰项）在各自游戏文件 `<script>` 的 `ROUNDS` 数组里，直接编辑即可。

## 添加新游戏

简单排序类游戏（推荐）：

1. 在 `index.html` 中新增一个视图容器（`<div class="view" id="view-xxx">`），内部元素 ID 使用独立前缀（如 `xxx-`）；
2. 在 `createOrderGame(prefix, sentences, bestKey, viewLabel)` 处新增一行实例化代码；
3. 在 `VIEWS` 数组与 `route()` 中注册对应 hash；
4. 在首页"可用游戏"区新增一张游戏卡片入口（参照现有 `game-card` 结构）。

复杂多屏幕游戏（迷宫/台阶/关卡制等，与排序游戏存在样式与 ID 冲突，建议独立文件）：

1. 将游戏 HTML 放入 `games/` 目录；
2. 在 `index.html` 新增 `<div class="view-frame" id="view-xxx">`，内含返回栏 + `<iframe data-src="games/xxx.html">`；
3. 在 `VIEWS` 数组与 `route()` 中注册 hash，并调用 `lazyLoadFrame('view-xxx')`；
4. 在首页"可用游戏"区新增游戏卡片入口。

## 技术说明

- 排序游戏内嵌于主文件自包含，仅引入自托管镜像字体（Fredoka / Nunito），无任何外部 JS 库；
- Abstract / Title 为独立文件，通过 iframe 懒加载嵌入（进入对应视图时才挂载，避免首屏同时初始化）；
- 游戏交互基于 Pointer Events，桌面键鼠与移动触屏通用；
- 通关最少步数纪录存于 `localStorage`（隐私模式下自动降级为不记录）；复杂游戏的姓名与会话状态同样存于 `localStorage`；
- 动效全部为原生 CSS / Canvas，遵循 `prefers-reduced-motion` 降级。
