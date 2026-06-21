# 第四届大鹏湾统计功能基因组学暑期课程网站

本仓库用于维护第四届大鹏湾统计功能基因组学暑期课程网站。网站使用Hugo和Hugo Book主题生成，面向中文课程和中国学员。

- 代码仓库：https://github.com/StatFunGen/summer-institute-nbc
- 主题模板：https://themes.gohugo.io/themes/hugo-book/
- 主题源码：https://github.com/alex-shpak/hugo-book

课程内容使用Markdown维护，源文件位于 `content/docs/`。生成后的HTML文件位于 `public/`，由GitHub Actions自动生成并提交回本仓库。

生成目标是让维护者或读者可以直接打开 `public/index.html` 查看网站；不应依赖 `hugo serve` 才能浏览。为此，Hugo配置启用了相对链接和 `.html` 文件式链接。

为了同时兼容 `hugo serve` 和直接打开 `public/index.html`，本仓库在 `layouts/partials/docs/html-head.html` 覆盖了Hugo Book的页面头部模板。这个覆盖只调整样式和脚本的引用方式，去掉会影响 `file://` 本地打开的完整性校验和跨域属性；主题布局本身仍然使用Hugo Book。

为了保证 `file://` 方式打开时稳定，站内搜索功能已关闭；搜索脚本在部分浏览器中需要本地服务器才可运行。

## 内容目录

- `content/docs/course/`：课程概览、学术内容、组织架构与授课团队。
- `content/docs/schedule/`：五天课程安排。
- `content/docs/practice/`：上机练习说明，包括连接无线网络和进入个人上机环境。
- `content/docs/slides.md`：课件下载入口页。
- `content/docs/life/`：食宿与后勤，包括签到、住宿、吃饭、车辆、上网、饮水和发票。
- `content/docs/ngb/`：国家基因库指南。
- `data/course_students_urls.xlsx`：学员上机入口数据源，包含学员、Linux文件夹和Jupyter专属链接。
- `content/categories/`、`content/tags/`：分类和标签入口页。
- `static/slides/`：课件文件固定存放位置。替换这里的同名文件后，Hugo会复制到 `public/slides/`。
- `scripts/generate_student_redirects.py`：根据 `data/course_students_urls.xlsx` 生成英文入口跳转页。
- `layouts/`：Hugo模板覆盖，用于中文页脚、分类页和标签页。
- `public/`：自动生成的HTML文件。

## 如何更新网站

1. 修改 `content/` 下的Markdown文件；如果更新课件，请替换 `static/slides/` 下的同名文件；如果更新学员上机入口，请替换 `data/course_students_urls.xlsx`。
2. 本地提交并推送：

```bash
git add content data scripts static README.md hugo.yaml layouts .github/workflows/build-html.yml
git commit -m "更新课程网站内容"
git push
```

3. 推送到GitHub后，GitHub Actions会自动安装Hugo、生成HTML，并把 `public/` 目录中的生成结果提交回 `main`。
4. 如果看到提交信息 `Build Hugo HTML [skip ci]`，说明HTML已经由自动流程生成。

不要直接修改 `public/` 目录中的HTML文件。`public/` 是自动生成结果，下一次构建时会被覆盖。需要改内容时，请修改Markdown源文件。

## 上机入口维护

学员上机入口由 `data/course_students_urls.xlsx` 自动生成。工作簿需要保留以下三列：

- `学员`：学员中文姓名，仅用于维护核对。
- `Linux文件夹`：英文入口名，也是生成的HTML文件名，例如生成 `public/<Linux文件夹>.html`。
- `Jupyter专属链接`：该学员对应的个人上机环境链接。

生成规则：

- 入口文件名来自 `Linux文件夹` 列。
- 入口文件位于网站根目录，例如 `public/<Linux文件夹>.html`。
- 每个入口页面会自动跳转到对应的个人上机环境。
- 如果更新表格，只需要替换 `data/course_students_urls.xlsx` 并推送；GitHub Actions会自动重新生成入口页面。
- `Linux文件夹` 只能使用英文字母、数字、点、下划线和连字符，且不能与已有页面重名。

## 本地预览

本地已可用 `pixi global install hugo` 安装Hugo。初始化主题后运行：

```bash
git submodule update --init --recursive
hugo server
```

本地生成静态HTML：

```bash
hugo --gc --minify --cleanDestinationDir --noTimes --noChmod --destination public
python3 scripts/generate_student_redirects.py --input data/course_students_urls.xlsx --output public
```

生成后可直接打开 `public/index.html` 检查静态网站；学员入口可直接打开 `public/<Linux文件夹>.html` 检查跳转页。不要用 `hugo serve` 的页面结果替代这个检查，因为 `hugo serve` 会临时注入本地预览脚本和本地地址。

如果本地预览后发现 `public/` 中出现 `localhost` 或 `livereload`，说明预览服务器写入了开发模式页面。此时应停止预览进程并重新运行上面的静态生成命令。

## 自动生成流程

GitHub Actions配置位于 `.github/workflows/build-html.yml`。每次推送Markdown、内容目录、模板、主题配置或工作流文件时，会自动执行以下步骤：

1. 检出仓库和Hugo Book主题子模块。
2. 通过 `pixi global install hugo` 安装Hugo。
3. 运行 `hugo --gc --minify --cleanDestinationDir --noTimes --noChmod --destination public` 生成HTML。
4. 运行 `python3 scripts/generate_student_redirects.py --input data/course_students_urls.xlsx --output public` 生成学员英文上机入口。
5. 如果 `public/` 有变化，自动提交 `Build Hugo HTML [skip ci]` 并推回 `main`。

这个流程负责生成并提交HTML。

## 中文写作规则

本站页面标题、菜单、说明文字、食宿后勤信息、国家基因库指南和维护说明应使用中文。英文仅用于必要的学术缩写、软件名称、命令名称、文件路径和链接地址，例如GWAS、QTL、TWAS、PRS、Hugo、GitHub、Markdown和HTML。
