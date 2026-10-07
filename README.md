# Class Interactive Games · 课堂互动游戏

为研究生《学术论文写作》课准备的课堂互动小游戏网站。纯原生 HTML/CSS/JS，单文件、无构建、无依赖，托管于 GitHub Pages，浏览器直接打开即用。

## 页面结构

| 路径 | 说明 |
|---|---|
| `index.html` | 单文件网站（首页游戏中心 + 全部游戏，页内 hash 路由切换） |

- 首页：`#home`（默认）—— 游戏列表与课堂使用提示
- 游戏：
  - `#game` —— Cover Letter 排序挑战（期刊投稿信句子排序，21 句）
  - `#game-intro` —— Introduction 排序挑战（论文引言，12 句）
  - `#game-conclusion` —— Conclusion 排序挑战（论文结论，10 句）

三个游戏的句子内容互不重复（投稿信 / AI 辅导研究引言 / 驾驶模拟实验结论），学生玩过其中一个不会预先知道另外两个的答案。

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

所有句子都在 `index.html` 的 `<script>` 中对应数组里（已按正确顺序排列）：

| 游戏 | 数据数组 |
|---|---|
| Cover Letter | `SENTENCES_COVER` |
| Introduction | `SENTENCES_INTRO` |
| Conclusion | `SENTENCES_CONCL` |

直接编辑文字即可，游戏会自动适应卡片数量、进度条与编号，无需改其他代码。

## 添加新游戏

1. 在 `index.html` 中新增一个视图容器（`<div class="view" id="view-xxx">`），内部元素 ID 使用独立前缀（如 `xxx-`）；
2. 在 `createOrderGame(prefix, sentences, bestKey, viewLabel)` 处新增一行实例化代码；
3. 在 `VIEWS` 数组与 `route()` 中注册对应 hash；
4. 在首页"可用游戏"区新增一张游戏卡片入口（参照现有 `game-card` 结构）。

## 技术说明

- 单文件自包含，仅引入自托管镜像字体（Fredoka / Nunito），无任何外部 JS 库；
- 游戏交互基于 Pointer Events，桌面键鼠与移动触屏通用；
- 通关最少步数纪录存于 `localStorage`（隐私模式下自动降级为不记录）；
- 动效全部为原生 CSS / Canvas，遵循 `prefers-reduced-motion` 降级。
