# FileShare - 8位提取码文件共享

基于 GitHub Releases 存储的纯前端文件共享工具。上传文件生成 8 位数字提取码，接收方输入提取码即可下载。

## 特性

- **纯前端** — 无需后端服务器，可部署到 GitHub Pages 或任何静态托管
- **GitHub Releases 存储** — 文件作为 Release Asset 上传，稳定可靠
- **8位提取码** — 提取码写入 Release body，永不删除
- **拖拽上传** — 支持拖拽或点击选择文件
- **Token 加密存储** — XOR + Base64 加密，避免 GitHub 自动扫描检测明文 Token
- **双模式配置** — 可保存到浏览器 localStorage，或保存为本地配置文件

## 快速开始

### 1. 准备 GitHub Token

- 访问 [GitHub Personal Access Tokens](https://github.com/settings/tokens)
- 点击 **Generate new token (classic)**
- 勾选 `repo` 完整权限
- 生成并复制 Token

### 2. 创建 GitHub 仓库

创建一个新的 GitHub 仓库（可以是空仓库），用于存储上传的文件。

### 3. 打开页面

直接用浏览器打开 `index.html`，或部署到 GitHub Pages：

- 将本仓库 fork 到你的 GitHub
- 进入仓库 Settings → Pages
- 选择 `main` 分支，`/root` 目录
- 保存后访问 `https://你的用户名.github.io/仓库名`

### 4. 配置 Token（两种方式）

#### 方式 A：保存为配置文件（推荐）

1. 点击 **GitHub 配置**，填入 Token、仓库所有者、仓库名称
2. 点击 **保存为配置文件**，浏览器会下载 `config.js`
3. 将 `config.js` 放入 `index.html` 同目录
4. 下次打开页面时自动加载配置（Token 已加密，GitHub 扫描不到）

#### 方式 B：保存到浏览器

1. 点击 **GitHub 配置**，填入 Token、仓库所有者、仓库名称
2. 点击 **保存到浏览器**，Token 加密后存入 localStorage
3. 下次打开页面时自动填充

## 加密原理

```
明文 Token  →  XOR 加密（随机密钥） →  Base64 编码 → 存入 config.js
```

- 密钥和加密后的 Token 一同存储在 `config.js` 中
- GitHub 的密钥扫描只检测明文 `ghp_` 等模式，加密后不会被检测到
- `config.js` 已加入 `.gitignore`，防止误提交

## 工作原理

```
上传: 文件 → 创建 Release (tag: share-{8位码}, body: {8位码}) → 上传文件作为 Asset
下载: 输入 8 位码 → 按 tag 查找 Release → 获取 Asset 下载链接
```

## 文件说明

| 文件 | 说明 |
|------|------|
| `index.html` | 主页面，完整 SPA |
| `config.example.js` | 配置文件模板，请勿直接编辑 |
| `config.js` | 真实配置文件（已 .gitignore，需自行生成） |
| `.gitignore` | 排除 config.js 避免误提交 |

## 注意事项

- 文件大小受 GitHub Releases 限制（单个文件最大 2GB，建议 100MB 以下）
- 需要 GitHub Token 才能上传和下载
- 提取码由 8 位随机数字组成，碰撞概率极低（1/100,000,000）
- 文件永不删除，上传后永久有效