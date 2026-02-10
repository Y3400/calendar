# CLAUDE.md
再次尝试
尝试新的提交和更新
此文件为 Claude Code (claude.ai/code) 在本仓库中工作时提供指导。

## 项目概述

这是一个单文件 Web 应用 - 支持中文的智能日历仪表板。设计为可本地保存的个人仪表板，可离线运行（天气数据除外）。

## 运行应用

直接在任何现代浏览器中打开 `calendar.html` 即可。无需构建过程、依赖或服务器。

## 架构

### 单文件结构
整个应用包含在 `calendar.html` 中，包含三个主要部分：
- **日历区域**（左侧）：月历视图，带导航功能
- **时间组件**（侧边栏）：实时时钟
- **天气组件**（侧边栏）：来自 wttr.in API 的天气数据
- **便签组件**（侧边栏）：每日记事，支持自动保存

### 组件架构

JavaScript 组织为三个组件对象（第 649-869 行）：

1. **TimeWidget** (`第 649-677 行`)
   - 使用 `setInterval` 每秒更新
   - 使用 `toLocaleTimeString` 和 `toLocaleDateString`，语言环境为 'zh-CN'

2. **WeatherWidget** (`第 680-754 行`)
   - 使用 wttr.in API (`https://wttr.in/Puyang,China?format=j1`)
   - 每 30 分钟自动更新
   - 通过 `getWeatherIcon()` 将天气代码映射为 emoji 图标

3. **NotesWidget** (`第 757-869 行`)
   - 使用 localStorage 存储便签，键名为 `calendar_notes`
   - 基于日期的便签组织，键名格式如 `2026-2-8`
   - 输入后 1 秒延迟自动保存
   - 监听自定义 `dateChanged` 事件

### 事件系统

日历导航会触发自定义 `dateChanged` 事件（`第 631 行`），在显示月份更改时通知其他组件。目前 NotesWidget 会监听此事件，但 `getSelectedDate()` 方法（`第 799-806 行`）无论日历导航如何都始终返回今天的日期 - 这是一个已知限制。

### 样式系统

- **CSS 变量** (`第 17-32 行`)：在 `:root` 中定义的主题颜色
- **玻璃拟态**：使用 `backdrop-filter: blur(24px) saturate(180%)`
- **渐变动画**：背景渐变在 15 秒内以 400% 尺寸流动
- **响应式**：断点为 950px（堆叠布局）和 600px（移动端调整）

### 数据存储

- **仅使用 localStorage**：专门用于便签持久化
- **天气 API**：外部 wttr.in 服务（有速率限制，无需 API 密钥）

## 主要限制

1. 天气位置硬编码为 "Puyang,China"（濮阳市）
2. 便签组件无论日历导航如何始终显示今天的日期
3. 没有针对 localStorage 配额超出的错误恢复机制
4. 没有测试套件或自动化测试
