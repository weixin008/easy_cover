# EasyCover 部署指南

本文档提供了 EasyCover 项目在各个平台的详细部署步骤。

## 目录

- [准备工作](#准备工作)
- [方案一：Vercel 部署（推荐）](#方案一vercel-部署推荐)
- [方案二：Cloudflare Pages 部署](#方案二cloudflare-pages-部署)
- [方案三：腾讯云 EdgeOne 部署](#方案三腾讯云-edgeone-部署)
- [方案四：Docker 部署](#方案四docker-部署)
- [方案五：Linux 服务器部署](#方案五linux-服务器部署)
- [常见问题](#常见问题)

---

## 准备工作

在开始部署前，确保你的项目可以正常构建：

```bash
# 安装依赖
npm install

# 本地测试
npm run dev

# 构建项目
npm run build
```

构建成功后，会在 `out` 目录生成静态文件。

---

## 方案一：Vercel 部署（推荐）

**优势**：
- ✅ 完全免费
- ✅ 自动 CI/CD
- ✅ 全球 CDN 加速
- ✅ 自动 HTTPS
- ✅ 完美支持 Next.js

### 方法 1：通过 Vercel CLI（命令行）

**步骤 1：安装 Vercel CLI**
```bash
npm install -g vercel
```

**步骤 2：登录 Vercel**
```bash
vercel login
```

**步骤 3：部署**
```bash
# 测试部署
vercel

# 生产部署
vercel --prod
```

部署完成后，Vercel 会提供一个域名，例如 `https://easy-cover.vercel.app`

### 方法 2：通过 GitHub 自动部署

**步骤 1：推送代码到 GitHub**
```bash
git add .
git commit -m "Initial commit"
git push origin main
```

**步骤 2：在 Vercel 导入项目**
1. 访问 https://vercel.com
2. 点击 "New Project"
3. 导入你的 GitHub 仓库
4. Vercel 会自动检测 Next.js 项目
5. 点击 "Deploy"

**步骤 3：自动部署配置**
- 每次推送到 `main` 分支会自动部署到生产环境
- PR 会自动创建预览环境

### 自定义域名（可选）

1. 进入项目设置
2. 选择 "Domains"
3. 添加你的域名
4. 按照提示配置 DNS 记录（CNAME 或 A 记录）

---

## 方案二：Cloudflare Pages 部署

**优势**：
- ✅ 完全免费
- ✅ 无限带宽
- ✅ 全球 CDN
- ✅ 自动 HTTPS
- ✅ 支持自定义域名

### 方法 1：通过 Wrangler CLI

**步骤 1：安装 Wrangler**
```bash
npm install -g wrangler
```

**步骤 2：登录 Cloudflare**
```bash
wrangler login
```

**步骤 3：构建项目**
```bash
npm run build
```

**步骤 4：部署**
```bash
# 首次部署
wrangler pages deploy out --project-name=easy-cover

# 后续更新
wrangler pages deploy out
```

### 方法 2：通过 GitHub 自动部署

**步骤 1：推送代码到 GitHub**
```bash
git add .
git commit -m "Deploy to Cloudflare Pages"
git push origin main
```

**步骤 2：在 Cloudflare Pages 创建项目**
1. 登录 Cloudflare Dashboard
2. 选择 "Pages"
3. 点击 "Create a project"
4. 连接 GitHub 仓库
5. 配置构建设置：
   - **构建命令**：`npm run build`
   - **构建输出目录**：`out`
   - **Node 版本**：18 或更高
6. 点击 "Save and Deploy"

**步骤 3：配置环境变量（如需要）**
在 Cloudflare Pages 设置中添加环境变量。

### 自定义域名

1. 在 Cloudflare Pages 项目中选择 "Custom domains"
2. 添加域名（如果域名已在 Cloudflare，会自动配置）
3. 等待 DNS 传播（通常几分钟）

---

## 方案三：腾讯云 EdgeOne 部署

**优势**：
- ✅ 国内访问速度快
- ✅ 免费版提供 10GB 流量/月
- ✅ 支持自定义域名
- ✅ 适合国内用户

### 步骤 1：构建项目

```bash
npm run build
```

### 步骤 2：注册腾讯云 EdgeOne

1. 访问 https://cloud.tencent.com/product/teo
2. 注册/登录腾讯云账号
3. 开通 EdgeOne 服务（选择免费版）

### 步骤 3：创建对象存储（COS）

**方法 A：通过控制台**
1. 登录 [COS 控制台](https://console.cloud.tencent.com/cos)
2. 创建存储桶
   - **名称**：`easy-cover-{随机数字}`
   - **地域**：选择离用户最近的区域（如广州）
   - **访问权限**：公有读私有写
3. 开启静态网站功能
   - 进入存储桶 > 基础配置 > 静态网站
   - 索引文档：`index.html`
   - 错误文档：`404.html`（可选）

**方法 B：使用 COSCMD 工具**
```bash
# 安装 COSCMD
pip install coscmd

# 配置（替换为你的实际信息）
coscmd config -a <SecretId> -s <SecretKey> -b <BucketName> -r <Region>

# 示例
coscmd config -a AKIDxxxxxx -s xxxxxx -b easy-cover-1234567890 -r ap-guangzhou
```

### 步骤 4：上传文件

**方法 A：通过控制台上传**
1. 进入存储桶
2. 点击"上传文件"
3. 选择 `out` 目录下的所有文件并上传

**方法 B：使用 COSCMD**
```bash
# 上传 out 目录的所有文件到根目录
coscmd upload -r ./out/ /

# 验证上传
coscmd list
```

### 步骤 5：配置 EdgeOne

1. 返回 [EdgeOne 控制台](https://console.cloud.tencent.com/edgeone)
2. 点击"新建站点"
3. 输入域名信息
   - 如果有域名：输入你的域名
   - 如果没有：可使用 EdgeOne 提供的测试域名
4. 选择"免费版"套餐
5. 配置源站
   - 源站类型：选择"对象存储"
   - 选择刚才创建的 COS 存储桶
6. 配置缓存规则（推荐）
   - 静态资源（js、css、图片）：缓存时间 1 年
   - HTML 文件：缓存时间 5 分钟或不缓存

### 步骤 6：配置 DNS（如有自定义域名）

1. 在 EdgeOne 控制台获取 CNAME 记录值
2. 到域名注册商处添加 CNAME 记录
   ```
   记录类型：CNAME
   主机记录：@ 或 www
   记录值：从 EdgeOne 获取的 CNAME 值
   ```
3. 等待 DNS 生效（通常 10 分钟内）

### 步骤 7：测试访问

访问你的域名或 EdgeOne 提供的测试域名，确认部署成功。

### EdgeOne 自动部署（GitHub Actions）

创建 `.github/workflows/deploy-edgeone.yml`：

```yaml
name: Deploy to EdgeOne

on:
  push:
    branches: [ main ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Build
      run: npm run build
      
    - name: Install COSCMD
      run: pip install coscmd
      
    - name: Configure COSCMD
      run: |
        coscmd config -a ${{ secrets.TENCENT_CLOUD_SECRET_ID }} \
                     -s ${{ secrets.TENCENT_CLOUD_SECRET_KEY }} \
                     -b ${{ secrets.COS_BUCKET }} \
                     -r ${{ secrets.COS_REGION }}
      
    - name: Upload to COS
      run: coscmd upload -r ./out/ / --delete
      
    - name: Purge EdgeOne Cache
      run: |
        # 可选：调用 EdgeOne API 清除缓存
        echo "Deployment completed"
```

在 GitHub 仓库设置中添加 Secrets：
- `TENCENT_CLOUD_SECRET_ID`
- `TENCENT_CLOUD_SECRET_KEY`
- `COS_BUCKET`（格式：bucket-name-appid）
- `COS_REGION`（如：ap-guangzhou）

---

## 方案四：Docker 部署

**优势**：
- ✅ 环境一致性
- ✅ 易于迁移
- ✅ 支持所有平台
- ✅ 完全控制

### 前置要求

- Docker（版本 20.10+）
- Docker Compose（版本 2.0+）

### 方法 1：使用 Docker Compose（推荐）

**步骤 1：确保文件齐全**

项目应包含：
- [`Dockerfile`](Dockerfile)
- [`docker-compose.yml`](docker-compose.yml)
- [`nginx.conf`](nginx.conf)

**步骤 2：构建并启动**
```bash
# 构建并启动容器
docker-compose up -d

# 查看日志
docker-compose logs -f

# 停止容器
docker-compose down

# 重新构建
docker-compose up -d --build
```

**步骤 3：访问应用**
在浏览器访问 `http://localhost:3000`

### 方法 2：使用 Docker 命令

**步骤 1：构建镜像**
```bash
docker build -t easy-cover:latest .
```

**步骤 2：运行容器**
```bash
docker run -d \
  --name easy-cover \
  -p 3000:80 \
  --restart unless-stopped \
  easy-cover:latest
```

**步骤 3：管理容器**
```bash
# 查看日志
docker logs -f easy-cover

# 停止容器
docker stop easy-cover

# 启动容器
docker start easy-cover

# 删除容器
docker rm -f easy-cover
```

### 方法 3：推送到 Docker Hub

**步骤 1：登录 Docker Hub**
```bash
docker login
```

**步骤 2：标记镜像**
```bash
docker tag easy-cover:latest yourusername/easy-cover:latest
```

**步骤 3：推送镜像**
```bash
docker push yourusername/easy-cover:latest
```

**步骤 4：在其他机器拉取并运行**
```bash
docker pull yourusername/easy-cover:latest
docker run -d -p 3000:80 --name easy-cover yourusername/easy-cover:latest
```

### 更新应用

```bash
# 停止并删除旧容器
docker-compose down

# 拉取最新代码
git pull

# 重新构建并启动
docker-compose up -d --build
```

### Docker 环境变量配置

修改 [`docker-compose.yml`](docker-compose.yml) 添加环境变量：

```yaml
services:
  easy-cover:
    environment:
      - NODE_ENV=production
      - CUSTOM_VAR=value
```

---

## 方案五：Linux 服务器部署

**适用场景**：
- 自有服务器
- VPS（阿里云、腾讯云、AWS 等）
- 需要完全控制

### 前置要求

- Linux 服务器（Ubuntu 20.04+ / CentOS 7+ / Debian 10+）
- Node.js 18+
- Nginx 或 Apache

### 5.1 使用 Nginx（推荐）

**步骤 1：安装 Node.js**

**Ubuntu/Debian：**
```bash
# 添加 NodeSource 仓库
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

# 安装 Node.js
sudo apt-get install -y nodejs

# 验证安装
node --version
npm --version
```

**CentOS/RHEL：**
```bash
# 添加 NodeSource 仓库
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -

# 安装 Node.js
sudo yum install -y nodejs

# 验证安装
node --version
npm --version
```

**步骤 2：克隆项目并构建**
```bash
# 创建部署目录
sudo mkdir -p /var/www
cd /var/www

# 克隆项目
sudo git clone https://github.com/yourusername/easy_cover.git
cd easy_cover

# 安装依赖
sudo npm install

# 构建项目
sudo npm run build

# 验证构建
ls -la out/
```

**步骤 3：安装 Nginx**

**Ubuntu/Debian：**
```bash
sudo apt-get update
sudo apt-get install -y nginx
```

**CentOS/RHEL：**
```bash
sudo yum install -y nginx
```

**步骤 4：配置 Nginx**
```bash
# 创建站点配置文件
sudo nano /etc/nginx/sites-available/easy-cover
```

添加以下内容：

```nginx
server {
    listen 80;
    server_name your-domain.com;  # 替换为你的域名或 IP
    
    root /var/www/easy_cover/out;
    index index.html;
    
    # Gzip 压缩
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/json application/javascript application/xml+rss;
    
    # 缓存静态资源
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # SPA 路由支持
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 安全头
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

**步骤 5：启用站点**

**Ubuntu/Debian：**
```bash
# 创建软链接
sudo ln -s /etc/nginx/sites-available/easy-cover /etc/nginx/sites-enabled/

# 测试配置
sudo nginx -t

# 重启 Nginx
sudo systemctl restart nginx
```

**CentOS/RHEL：**
```bash
# 测试配置
sudo nginx -t

# 重启 Nginx
sudo systemctl restart nginx

# 设置开机自启
sudo systemctl enable nginx
```

**步骤 6：配置防火墙**

**Ubuntu（UFW）：**
```bash
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

**CentOS（Firewalld）：**
```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

**步骤 7：配置 HTTPS（推荐）**

使用 Let's Encrypt 免费 SSL 证书：

```bash
# 安装 Certbot
# Ubuntu/Debian
sudo apt-get install -y certbot python3-certbot-nginx

# CentOS
sudo yum install -y certbot python3-certbot-nginx

# 获取证书并自动配置
sudo certbot --nginx -d your-domain.com

# 测试自动续期
sudo certbot renew --dry-run
```

Certbot 会自动修改 Nginx 配置文件，启用 HTTPS。

### 5.2 使用系统服务管理更新

创建自动更新脚本：

**创建脚本：**
```bash
sudo nano /usr/local/bin/update-easy-cover.sh
```

**脚本内容：**
```bash
#!/bin/bash
cd /var/www/easy_cover
git pull
npm install
npm run build
sudo systemctl reload nginx
echo "Update completed at $(date)"
```

**设置权限：**
```bash
sudo chmod +x /usr/local/bin/update-easy-cover.sh
```

**手动更新：**
```bash
sudo /usr/local/bin/update-easy-cover.sh
```

### 5.3 设置自动部署（GitHub Webhook）

**步骤 1：安装 webhook 工具**
```bash
# 安装 Go
wget https://golang.org/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin

# 安装 webhook
go install github.com/adnanh/webhook@latest
```

**步骤 2：创建 webhook 配置**
```bash
sudo mkdir -p /etc/webhook
sudo nano /etc/webhook/hooks.json
```

```json
[
  {
    "id": "easy-cover-deploy",
    "execute-command": "/usr/local/bin/update-easy-cover.sh",
    "command-working-directory": "/var/www/easy_cover",
    "response-message": "Deployment started",
    "trigger-rule": {
      "match": {
        "type": "payload-hash-sha256",
        "secret": "your-webhook-secret",
        "parameter": {
          "source": "header",
          "name": "X-Hub-Signature-256"
        }
      }
    }
  }
]
```

**步骤 3：启动 webhook 服务**
```bash
# 创建 systemd 服务
sudo nano /etc/systemd/system/webhook.service
```

```ini
[Unit]
Description=Webhook Server
After=network.target

[Service]
Type=simple
User=root
ExecStart=/root/go/bin/webhook -hooks /etc/webhook/hooks.json -verbose -port 9000
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```bash
# 启动服务
sudo systemctl daemon-reload
sudo systemctl start webhook
sudo systemctl enable webhook

# 查看状态
sudo systemctl status webhook
```

**步骤 4：在 GitHub 配置 Webhook**
1. 进入 GitHub 仓库设置
2. 选择 "Webhooks" > "Add webhook"
3. Payload URL: `http://your-server-ip:9000/hooks/easy-cover-deploy`
4. Content type: `application/json`
5. Secret: `your-webhook-secret`（与配置文件一致）
6. 选择 "Just the push event"
7. 保存

现在每次推送代码到 GitHub，服务器会自动更新！

---

## 常见问题

### Q1：构建失败怎么办？

**检查 Node.js 版本：**
```bash
node --version  # 确保 >= 18.0.0
```

**清除缓存重新构建：**
```bash
rm -rf node_modules package-lock.json out .next
npm install
npm run build
```

### Q2：部署后页面空白？

**检查构建输出：**
```bash
ls -la out/
```

确保 `out` 目录包含 `index.html` 和其他静态文件。

**检查浏览器控制台**，查看是否有 JavaScript 错误。

### Q3：Docker 容器无法访问？

**检查容器状态：**
```bash
docker ps -a
```

**查看容器日志：**
```bash
docker logs easy-cover
```

**检查端口映射：**
```bash
docker port easy-cover
```

### Q4：Nginx 404 错误？

**检查文件路径：**
```bash
ls -la /var/www/easy_cover/out/
```

**检查 Nginx 配置：**
```bash
sudo nginx -t
```

**查看 Nginx 错误日志：**
```bash
sudo tail -f /var/log/nginx/error.log
```

### Q5：HTTPS 证书问题？

**续期证书：**
```bash
sudo certbot renew
```

**检查证书状态：**
```bash
sudo certbot certificates
```

### Q6：如何减少流量消耗？

1. **启用 Gzip 压缩**（Nginx 配置已包含）
2. **设置合理的缓存策略**
3. **优化图片大小**
4. **使用 CDN**

### Q7：EdgeOne 流量超限怎么办？

免费版每月 10GB 流量，超出需付费。建议：
1. 监控流量使用
2. 启用更长的缓存时间
3. 升级到付费版
4. 切换到其他平台（Vercel、Cloudflare 无流量限制）

---

## 性能优化建议

### 1. 启用 Brotli 压缩（Nginx）

```nginx
# 安装 Brotli 模块并配置
brotli on;
brotli_comp_level 6;
brotli_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

### 2. 启用 HTTP/2

```nginx
server {
    listen 443 ssl http2;
    # ...
}
```

### 3. 配置 CDN

将静态资源推送到 CDN：
- 阿里云 CDN
- 腾讯云 CDN
- Cloudflare CDN
- AWS CloudFront

### 4. 监控性能

使用监控工具：
- Prometheus + Grafana
- 腾讯云监控
- 阿里云监控
- Uptime Robot

---

## 总结

| 平台 | 难度 | 费用 | 速度 | 推荐度 |
|------|------|------|------|--------|
| Vercel | ⭐ | 免费 | 🚀🚀🚀 | ⭐⭐⭐⭐⭐ |
| Cloudflare Pages | ⭐ | 免费 | 🚀🚀🚀 | ⭐⭐⭐⭐⭐ |
| EdgeOne | ⭐⭐ | 免费版 10GB/月 | 🚀🚀 (国内快) | ⭐⭐⭐⭐ |
| Docker | ⭐⭐⭐ | 服务器成本 | 🚀🚀 | ⭐⭐⭐⭐ |
| Linux 服务器 | ⭐⭐⭐⭐ | 服务器成本 | 🚀🚀 | ⭐⭐⭐ |

**推荐选择：**
- **国外用户**：Vercel 或 Cloudflare Pages
- **国内用户**：EdgeOne 或 Vercel（配置 CDN）
- **企业用户**：Docker + 自有服务器
- **学习目的**：任选一种

---

## 获取帮助

如果遇到问题：
1. 查看本文档的常见问题部分
2. 提交 GitHub Issue
3. 查看官方文档：
   - [Next.js 部署文档](https://nextjs.org/docs/deployment)
   - [Vercel 文档](https://vercel.com/docs)
   - [Cloudflare Pages 文档](https://developers.cloudflare.com/pages/)
   - [Docker 文档](https://docs.docker.com/)

祝部署顺利！🎉
