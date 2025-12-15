---
title:  动态菜单显示问题排查与修复指南
published: 2023-08-01
description: 排查并修复动态菜单在导航界面不显示的问题
tags: [Example, Video]
category: Examples
draft: false
---
# 动态菜单显示问题排查与修复指南



## 问题描述
通过Magic-API接口生成菜单功能创建的动态菜单（ID: 3021 "查询配送方案"）在导航界面不显示。

## 问题分析

### 1. 数据库层面问题
- ✅ 菜单记录已存在（menu_id=3021）
- ❌ 缺少角色菜单关联（sys_role_menu表中无记录）
- ❌ 父菜单可能不存在或不可见（parent_id=3000）

### 2. 前端层面问题
- 可能存在权限缓存问题
- 路由动态加载问题
- 组件路径配置问题

## 修复步骤

### 第一步：执行数据库修复脚本
运行 `fix_dynamic_menu_display.sql` 脚本：
```bash
mysql -u用户名 -p密码 数据库名 < fix_dynamic_menu_display.sql
```

### 第二步：前端缓存清理
1. **清除浏览器缓存**
   - 按 F12 打开开发者工具
   - 右键刷新按钮，选择"清空缓存并硬性重新加载"

2. **清除LocalStorage/SessionStorage**
   ```javascript
   // 在浏览器控制台执行
   localStorage.clear();
   sessionStorage.clear();
   ```

3. **重新登录**
   - 退出当前账户
   - 重新登录以获取最新权限信息

### 第三步：检查前端路由配置
确保以下文件正确配置：

1. **检查权限获取** (`src/permission.js:27`)
   ```javascript
   store.dispatch('GenerateRoutes').then(accessRoutes => {
     router.addRoutes(accessRoutes) // 动态添加可访问路由表
     next({ ...to, replace: true })
   })
   ```

2. **检查菜单API** (`src/api/menu.js:4-8`)
   ```javascript
   export const getRouters = () => {
     return request({
       url: '/getRouters',
       method: 'get'
     })
   }
   ```

3. **检查权限Store** (`src/store/modules/permission.js:36-48`)
   ```javascript
   getRouters().then(res => {
     const sdata = JSON.parse(JSON.stringify(res.data))
     const rdata = JSON.parse(JSON.stringify(res.data))
     const sidebarRoutes = filterAsyncRouter(sdata)
     const rewriteRoutes = filterAsyncRouter(rdata, false, true)
     // ... 设置路由
   })
   ```

### 第四步：验证组件路径
确认组件文件存在：
- ✅ `src/views/tool/magic-api/interface-test.vue` 已存在
- ✅ 组件路径配置正确：`tool/magic-api/interface-test`

### 第五步：检查权限标识
确认权限标识格式正确：
- 菜单权限：`magicapi:spscm:cdplist:list`
- 父菜单权限：`tool:magic-api:list`

## 调试方法

### 1. 检查API响应
在浏览器开发者工具中查看 `/getRouters` 接口响应：
```javascript
// 在控制台执行
fetch('/getRouters', {
  headers: {
    'Authorization': 'Bearer ' + localStorage.getItem('token')
  }
}).then(res => res.json()).then(console.log)
```

### 2. 检查Vuex状态
```javascript
// 在Vue DevTools或控制台中查看
console.log(this.$store.getters.sidebarRouters)
console.log(this.$store.getters.routes)
```

### 3. 检查用户权限
```javascript
// 检查当前用户权限
console.log(this.$store.getters.permissions)
console.log(this.$store.getters.roles)
```

## 常见解决方案

1. **立即生效方案**：
   - 重启后端服务
   - 清除前端缓存
   - 重新登录

2. **临时访问方案**：
   - 直接访问URL：`/#/magic-api-test/2e797790f1f24f5488e05594c9cc7a6c?path=%2FCDPList%2Flist`

3. **权限检查**：
   - 确认当前用户所属角色有该菜单权限
   - 检查菜单状态是否为启用（status='0'）
   - 检查菜单可见性（visible='0'）

## 预防措施

1. **自动权限分配**：
   修改 `generateMenu` 方法，在生成菜单时自动分配给当前用户角色。

2. **缓存刷新机制**：
   在菜单生成成功后，自动刷新前端路由缓存。

3. **权限验证**：
   在生成菜单前检查父菜单权限。 