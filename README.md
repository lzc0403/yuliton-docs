# Yuliton API 文档网站

基于 MkDocs 和 Material 主题构建的 Yuliton API 文档网站，支持 Vercel 部署。

## 功能特性

-   **Material 主题**：现代化的文档主题，支持暗色模式
-   **全文搜索**：内置搜索功能，支持中英文
-   **代码高亮**：支持多种编程语言的代码高亮
-   **响应式设计**：完美适配桌面和移动设备
-   **快速部署**：一键部署到 Vercel

## 本地开发

### 前提条件

- Python 3.8+
- pip

### 安装依赖

```bash
pip install mkdocs mkdocs-material
```

### 启动本地服务器

```bash
mkdocs serve
```

访问 http://127.0.0.1:8000 查看文档。

### 构建静态网站

```bash
mkdocs build
```

生成的静态文件位于 `site` 目录。

## Vercel 部署

### 步骤 1：初始化 Git 仓库

```bash
git init
git add .
git commit -m "Initial commit"
```

### 步骤 2：推送到 GitHub

```bash
git remote add origin https://github.com/yourusername/yuliton-docs.git
git push -u origin main
```

### 步骤 3：连接 Vercel

1. 登录 [Vercel](https://vercel.com)
2. 点击 "New Project"
3. 选择 GitHub 仓库
4. Vercel 会自动检测 MkDocs 项目并配置构建
5. 点击 "Deploy"

### 步骤 4：自定义域名（可选）

1. 在 Vercel 项目设置中添加自定义域名
2. 配置 DNS 记录指向 Vercel

## 项目结构

```
yuliton-docs/
├── mkdocs.yml          # MkDocs 配置文件
├── vercel.json         # Vercel 部署配置
├── README.md           # 项目说明
└── docs/               # 文档目录
    ├── index.md        # 首页
    └── yuliton-api.md  # API 文档
```

## 自定义配置

### 修改主题颜色

编辑 `mkdocs.yml` 中的 `palette` 部分：

```yaml
theme:
  palette:
    - scheme: default
      primary: deep purple  # 主色调
      accent: amber         # 强调色
```

### 添加新页面

1. 在 `docs` 目录创建新的 `.md` 文件
2. 在 `mkdocs.yml` 的 `nav` 部分添加导航项

### 启用分析

在 `mkdocs.yml` 中添加 Google Analytics：

```yaml
plugins:
  - google-analytics:
      property: G-XXXXXXXXXX
```

## 常见问题

### Q: 构建失败怎么办？

A: 检查 Python 版本和依赖是否正确安装：

```bash
python --version
pip list | grep mkdocs
```

### Q: 如何修改文档内容？

A: 编辑 `docs` 目录下的 `.md` 文件，保存后本地服务器会自动重载。

### Q: 如何添加图片？

A: 在 `docs` 目录创建 `images` 文件夹，将图片放入其中，然后在 Markdown 中引用：

```markdown
![图片描述](images/example.png)
```

## 相关链接

- [MkDocs 官方文档](https://www.mkdocs.org/)
- [Material 主题文档](https://squidfunk.github.io/mkdocs-material/)
- [Vercel 部署文档](https://vercel.com/docs)

## 许可证

MIT License