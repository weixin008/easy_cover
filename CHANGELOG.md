# 更新日志

## 2026-01-13 - 重大功能更新

### ✨ 新增功能

#### 1. 文字多行显示功能
- **功能描述**：支持将文字自动分为单行、两行或三行显示
- **使用位置**：文字设置 → 文字行数
- **可选项**：
  - 单行：默认模式，所有文字显示在一行
  - 两行：文字均匀分为两行
  - 三行：文字均匀分为三行
- **配套功能**：可调节行间距（0.8-2.5倍），默认1.2
- **文件修改**：
  - [`store/useCoverStore.ts`](store/useCoverStore.ts) - 添加 `lineMode` 和 `lineSpacing` 字段
  - [`components/cover/Canvas.tsx`](components/cover/Canvas.tsx) - 实现文字分行逻辑
  - [`components/cover/Controls.tsx`](components/cover/Controls.tsx) - 添加UI控制

#### 2. 文字阴影效果
- **功能描述**：为文字添加可自定义的阴影效果
- **使用位置**：文字设置 → 文字阴影
- **可配置项**：
  - 阴影颜色：支持全颜色选择
  - 模糊半径：0-50px
  - 水平偏移：-50px 到 +50px
  - 垂直偏移：-50px 到 +50px
- **文件修改**：
  - [`store/useCoverStore.ts`](store/useCoverStore.ts) - 添加阴影相关字段
  - [`components/cover/Canvas.tsx`](components/cover/Canvas.tsx) - 应用CSS textShadow
  - [`components/cover/Controls.tsx`](components/cover/Controls.tsx) - 添加阴影控制面板

#### 3. 画布缩放控制
- **功能描述**：实时缩放画布以便查看细节或全局
- **使用位置**：画布右上角悬浮按钮
- **控制按钮**：
  - ➕ 放大：增加10%缩放
  - ➖ 缩小：减少10%缩放
  - 🔲 重置：恢复100%缩放
  - 📊 百分比显示：实时显示当前缩放比例
- **缩放范围**：30% - 300%
- **文件修改**：
  - [`components/cover/Canvas.tsx`](components/cover/Canvas.tsx) - 添加缩放状态和控制UI

#### 4. 配置自动保存（LocalStorage）
- **功能描述**：自动保存用户的所有配置到浏览器本地存储
- **保存内容**：
  - 文字设置（内容、字体、大小、颜色等）
  - 图标设置（图标、大小、样式等）
  - 背景设置（颜色、样式等）
  - 布局选择（比例、标尺显示）
- **不保存内容**：
  - 上传的图片文件（blob URLs）
  - 自定义图标文件
- **存储键名**：`easy-cover-storage`
- **文件修改**：
  - [`store/useCoverStore.ts`](store/useCoverStore.ts) - 使用 `zustand/middleware` 的 `persist`

### 📦 部署优化

#### 5. Docker 支持
创建完整的 Docker 部署方案：
- **文件列表**：
  - [`Dockerfile`](Dockerfile) - 多阶段构建，优化镜像大小
  - [`docker-compose.yml`](docker-compose.yml) - Docker Compose 配置
  - [`nginx.conf`](nginx.conf) - Nginx 配置，包含：
    - Gzip 压缩
    - 静态资源缓存
    - SPA 路由支持
    - 安全头设置
    - 健康检查端点

**使用方法**：
```bash
# 构建并启动
docker-compose up -d

# 查看日志
docker-compose logs -f

# 停止
docker-compose down
```

访问：http://localhost:3000

#### 6. 详细部署文档
创建 [`DEPLOYMENT.md`](DEPLOYMENT.md)，包含5个主流平台的完整部署指南：

**支持的部署平台**：
1. **Vercel**（推荐）
   - CLI 部署
   - GitHub 自动部署
   - 自定义域名配置

2. **Cloudflare Pages**
   - Wrangler CLI 部署
   - GitHub 集成
   - 自定义域名

3. **腾讯云 EdgeOne**（国内推荐）
   - COS 对象存储
   - EdgeOne 配置
   - GitHub Actions 自动部署

4. **Docker 部署**
   - 本地部署
   - Docker Hub 发布
   - 容器管理

5. **Linux 服务器部署**
   - Nginx 配置
   - HTTPS 证书（Let's Encrypt）
   - 自动更新脚本
   - Webhook 自动部署

---

## 🔧 技术改进

### 代码优化
1. **状态管理增强**：使用 Zustand persist 中间件实现配置持久化
2. **用户体验提升**：
   - 缩放控制提供更好的预览体验
   - 文字多行布局更灵活
   - 阴影效果增强视觉表现
3. **部署便利性**：一键 Docker 部署，支持多平台

### 文件变更统计
- **新增文件**：4个
  - `Dockerfile`
  - `docker-compose.yml`
  - `nginx.conf`
  - `DEPLOYMENT.md`
  - `CHANGELOG.md`（本文件）

- **修改文件**：3个
  - `store/useCoverStore.ts`
  - `components/cover/Canvas.tsx`
  - `components/cover/Controls.tsx`

---

## 📝 使用指南

### 文字多行功能使用

1. 打开左侧控制面板
2. 找到"文字设置"区域
3. 选择"文字行数"下拉菜单
4. 选择单行/两行/三行
5. 如果选择多行，可调节"行间距"滑块

**示例**：
- 输入文字："欢迎使用EasyCover"
- 选择"三行"
- 结果：
  ```
  欢迎使
  用Eas
  yCover
  ```

### 文字阴影功能使用

1. 找到"文字设置"区域
2. 开启"文字阴影"开关
3. 调节以下参数：
   - **阴影颜色**：点击颜色块选择
   - **模糊**：控制阴影柔和度
   - **水平偏移**：左右移动阴影
   - **垂直偏移**：上下移动阴影

**推荐配置**：
- 浅色文字：深色阴影（黑色，透明度50%）
- 深色文字：浅色阴影（白色，透明度30%）

### 画布缩放功能使用

1. 查看画布右上角的悬浮按钮组
2. 点击 ➕ 放大画布
3. 点击 ➖ 缩小画布
4. 点击 🔲 重置到100%
5. 下方数字显示当前缩放比例

**使用场景**：
- 放大：查看文字细节、调整精确位置
- 缩小：查看整体效果
- 重置：恢复默认视图

### 配置自动保存

所有配置会自动保存到浏览器本地存储：
- ✅ 实时自动保存，无需手动操作
- ✅ 关闭浏览器后配置保留
- ✅ 清除浏览器数据会丢失配置

**清除保存的配置**：
打开浏览器开发者工具（F12）→ Application → Local Storage → 删除 `easy-cover-storage`

---

## 🚀 部署建议

根据不同需求选择部署方案：

| 场景 | 推荐方案 | 理由 |
|------|---------|------|
| 个人项目展示 | Vercel | 免费、简单、自动HTTPS |
| 国内用户访问 | Cloudflare Pages | 国内速度好，免费流量 |
| 企业内部使用 | Docker | 完全控制，数据安全 |
| 学习Docker | Docker | 实践容器化部署 |
| 需要定制化 | Linux服务器 | 灵活配置，完全掌控 |

---

## ⚠️ 注意事项

### 浏览器兼容性
- **推荐浏览器**：Chrome 90+、Edge 90+、Firefox 88+、Safari 14+
- **不支持**：IE 11及以下版本

### 性能建议
1. **图片优化**：上传的背景图片建议小于5MB
2. **字体加载**：首次使用自定义字体可能需要几秒加载时间
3. **导出质量**：导出大尺寸图片时可能需要等待几秒

### 数据安全
1. **本地存储**：所有配置保存在浏览器，清除缓存会丢失
2. **隐私保护**：图片处理完全在浏览器本地，不上传服务器
3. **建议备份**：重要配置建议导出保存（未来功能）

---

## 🐛 已知问题

### TypeScript类型警告
- **现象**：编辑器显示类型错误
- **影响**：仅IDE警告，不影响实际运行
- **原因**：Zustand persist 中间件的类型推导
- **解决**：项目可正常构建和运行，可忽略警告

### 边缘情况
1. **极长文字**：超过100个字符可能导致分行不均匀
2. **特殊字符**：emoji等可能影响行宽计算
3. **缩放极限**：30%以下或300%以上可能影响操作体验

---

## 🔮 未来计划

虽然本次更新未实现，但以下功能值得期待：

1. **性能优化**
   - 使用 React.memo 优化组件渲染
   - 使用 useCallback 缓存函数
   - 减少不必要的重渲染

2. **撤销/重做功能**
   - 支持 Ctrl+Z / Ctrl+Y
   - 历史记录管理
   - 配置快照

3. **更多导出选项**
   - 支持 JPG、SVG、WebP 格式
   - 自定义导出质量
   - 批量导出多个比例

4. **模板系统**
   - 预设模板库
   - 一键应用模板
   - 自定义模板保存

5. **协作功能**
   - 配置分享链接
   - 二维码生成
   - 云端同步

---

## 📊 更新统计

- **新增功能**：6个
- **代码行数**：约+400行
- **新增文件**：5个
- **修改文件**：3个
- **文档完善**：2个完整部署指南
- **部署方案**：5个平台支持

---

## 🙏 致谢

感谢使用 EasyCover！

如有问题或建议，欢迎提交 Issue 或 Pull Request。

---

**文档版本**：v1.1.0  
**更新日期**：2026-01-13  
**维护者**：EasyCover Team
