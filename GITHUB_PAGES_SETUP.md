# GitHub Pages 配置指南

## 📚 项目说明

本项目已将 569 个 Markdown 文档转换为 HTML 静态网站，存放在 `docs/` 目录中。

## 🚀 GitHub Pages 部署步骤

### 步骤 1: 提交并推送代码

```bash
# 查看当前更改
git status

# 添加所有文件
git add .

# 提交更改
git commit -m "Add HTML documentation site in docs folder"

# 推送到 GitHub
git push -u origin website
```

### 步骤 2: 在 GitHub 上配置 Pages

1. 访问您的 GitHub 仓库页面
2. 点击 **Settings**（设置）
3. 在左侧菜单中找到 **Pages**
4. 在 **Source** 部分：
   - Branch: 选择 `website`
   - Folder: 选择 `/docs`
5. 点击 **Save**（保存）

### 步骤 3: 等待部署

- GitHub 会自动构建和部署网站
- 通常需要 1-3 分钟
- 部署完成后会显示网站 URL
- URL 格式通常为: `https://[username].github.io/[repo-name]/`

## 📊 网站内容

- **总文档**: 569 篇
- **主索引**: docs/index.html
- **文档分类**:
  - 📝 博客文章: 22 篇
  - 🤝 贡献指南: 10 篇
  - 🔍 源码解析: 128 篇
  - 📊 VChart FAQ: 333 篇
  - 📋 VTable FAQ: 76 篇

## 🎨 网站特性

- ✅ 纯静态 HTML，无需 Jekyll 构建
- ✅ GitHub 风格的美观样式
- ✅ 响应式设计，支持移动端
- ✅ 代码高亮和表格优化
- ✅ 精美的渐变背景索引页

## 🔧 本地测试

如果想在本地预览网站：

```bash
cd docs
python3 -m http.server 8000
# 访问 http://localhost:8000
```

## 📝 目录说明

- `docs/` - GitHub Pages 网站根目录
  - `index.html` - 网站首页
  - `visactor/` - 所有文档 HTML 文件
- `visactor/` - 原始 Markdown 文件（保留）
- `Gemfile` - Jekyll 依赖（可选，用于本地开发）
- `index.md` - Markdown 索引（可选）

## ⚠️ 注意事项

1. **docs 目录必须提交到 Git**
   - 已在 .gitignore 中配置不忽略 docs/
   - 确保 `git add docs/` 能成功添加

2. **分支选择**
   - 使用 `website` 分支部署
   - 保持 `main` 分支为原始文档

3. **URL 访问**
   - 首页: `https://[username].github.io/[repo]/`
   - 文档: `https://[username].github.io/[repo]/visactor/blog/cn/xxx.html`

## 🎉 完成

配置完成后，您的文档网站将自动部署并可以通过 GitHub Pages URL 访问！

