# 第四届大鹏湾统计功能基因组学暑期课程网站

本仓库用于维护第四届大鹏湾统计功能基因组学暑期课程网站。网站使用Hugo和Hugo Book主题生成，面向中文课程和中国学员。

- 代码仓库：https://github.com/StatFunGen/summer-institute-nbc
- 主题模板：https://themes.gohugo.io/themes/hugo-book/
- 主题源码：https://github.com/alex-shpak/hugo-book

课程内容使用Markdown维护，源文件位于 `content/docs/`。生成后的HTML文件位于 `public/`，由GitHub Actions自动生成并提交回本仓库。本仓库不使用GitHub Pages部署流程。

生成目标是让维护者或读者可以直接打开 `public/index.html` 查看网站；不应依赖 `hugo serve` 才能浏览。为此，Hugo配置启用了相对链接和 `.html` 文件式链接。

为了保证 `file://` 方式打开时稳定，站内搜索功能已关闭；搜索脚本在部分浏览器中需要本地服务器才可运行。

## 内容目录

- `content/docs/course/`：课程概览、学术内容、组织架构与授课团队。
- `content/docs/schedule/`：五天课程安排。
- `content/docs/life/`：食宿与后勤，包括签到、住宿、吃饭、车辆、上网、饮水和发票。
- `content/docs/ngb/`：国家基因库指南。
- `content/categories/`、`content/tags/`：分类和标签入口页。
- `layouts/`：Hugo模板覆盖，用于中文页脚、分类页和标签页。
- `public/`：自动生成的HTML文件。

## 如何更新网站

1. 修改 `content/` 下的Markdown文件。
2. 本地提交并推送：

```bash
git add content README.md hugo.yaml layouts
git commit -m "更新课程网站内容"
git push
```

3. 推送到GitHub后，GitHub Actions会自动安装Hugo、生成HTML，并把 `public/` 目录中的生成结果提交回 `main`。
4. 如果看到提交信息 `Build Hugo HTML [skip ci]`，说明HTML已经由自动流程生成。

不要直接修改 `public/` 目录中的HTML文件。`public/` 是自动生成结果，下一次构建时会被覆盖。需要改内容时，请修改Markdown源文件。

## 本地预览

本地已可用 `pixi global install hugo` 安装Hugo。初始化主题后运行：

```bash
git submodule update --init --recursive
hugo server
```

本地生成静态HTML：

```bash
hugo --gc --minify --cleanDestinationDir --noTimes --noChmod --destination public
```

生成后可直接打开 `public/index.html` 检查静态网站。不要用 `hugo serve` 的页面结果替代这个检查，因为 `hugo serve` 会临时注入本地预览脚本和本地地址。

如果本地预览后发现 `public/` 中出现 `localhost` 或 `livereload`，说明预览服务器写入了开发模式页面。此时应停止预览进程并重新运行上面的静态生成命令。

## 自动生成流程

GitHub Actions配置位于 `.github/workflows/build-html.yml`。每次推送Markdown、内容目录、模板、主题配置或工作流文件时，会自动执行以下步骤：

1. 检出仓库和Hugo Book主题子模块。
2. 通过 `pixi global install hugo` 安装Hugo。
3. 运行 `hugo --gc --minify --cleanDestinationDir --noTimes --noChmod --destination public` 生成HTML。
4. 如果 `public/` 有变化，自动提交 `Build Hugo HTML [skip ci]` 并推回 `main`。

这个流程只生成并提交HTML，不发布GitHub Pages。

## 中文写作规则

本站页面标题、菜单、说明文字、食宿后勤信息、国家基因库指南和维护说明应使用中文。英文仅用于必要的学术缩写、软件名称、命令名称、文件路径和链接地址，例如GWAS、QTL、TWAS、PRS、Hugo、GitHub、Markdown和HTML。
