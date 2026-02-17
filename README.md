# 保密壁纸生成器

一个简易的娱乐性保密警告壁纸生成工具，用于创建带有自定义保密信息的桌面壁纸。

## 功能特性

- 🎨 **自定义背景**：支持设置背景颜色和壁纸比例
- 📝 **中心文字**：自定义文字内容、颜色、字体、大小、位置和对齐方式
- 🖼️ **标志设置**：支持使用预设标志或上传自定义标志
- 🔍 **落款设置**：添加自定义落款文字，支持设置字体、颜色和位置
- 💾 **多格式下载**：支持 PNG 和 JPG 格式下载
- 📱 **响应式设计**：适配桌面端和移动端
- 🔧 **配置管理**：支持导出和导入配置
- 🎯 **实时预览**：配置变更实时反映在预览效果中

## 技术栈

- **前端框架**：Vue 3 + TypeScript
- **构建工具**：Vite
- **样式**：原生 CSS + 响应式设计
- **Canvas API**：用于壁纸渲染

## 快速开始

### 安装依赖

```bash
npm install
```

### 开发模式

```bash
npm run dev
```

项目将在本地启动，默认地址为 `http://localhost:5173`。

### 构建项目

```bash
npm run build
```

构建后的文件将生成在 `dist` 目录中。

### 预览构建结果

```bash
npm run preview
```


## 项目结构

```
wallpaper-generator/
├── dist/             # 构建输出目录
├── public/           # 静态资源
├── src/              # 源代码
│   ├── components/    # 组件
│   │   ├── ControlPanel.vue  # 控制面板
│   │   ├── PreviewSection.vue  # 预览区域
│   │   └── Tabs.vue  # 标签栏
│   ├── App.vue        # 主应用组件
│   ├── main.ts        # 应用入口
│   └── style.css      # 全局样式
├── index.html        # HTML 模板
├── package.json      # 项目配置
├── tsconfig.json     # TypeScript 配置
├── vite.config.ts    # Vite 配置
└── README.md         # 项目文档
```

## 贡献指南

欢迎贡献代码！请按照以下步骤：

1. Fork 项目
2. 创建功能分支
3. 提交更改
4. 推送分支
5. 开启 Pull Request

## 许可证

MIT License

## 致谢

- Vue 3 - 优秀的前端框架
- TypeScript - 类型安全的 JavaScript 超集
- Vite - 快速的构建工具

---

**注意**：本工具生成的壁纸仅用于内部保密提醒，请勿用于非法用途。
