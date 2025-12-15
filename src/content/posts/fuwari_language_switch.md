---
title:  Fuwari 语言切换机制详解
published: 2025-10-20
description: 详细解析 Fuwari 模板的语言切换机制，包括原理、配置方式和实际使用方法。
tags: [Fuwari, 模版语言切换方法]
category: Fuwari
draft: false
---

# Fuwari 语言切换机制详解

## 语言切换原理

# Fuwari 语言切换机制解析

## 1. 语言配置方式

语言切换主要通过 src/config.ts 文件中的 siteConfig.lang 属性进行配置：

```typescript
export const siteConfig: SiteConfig = {
  lang: "zh-CN", // 语言代码，例如 'en', 'zh_CN', 'ja' 等
  // ... 其他配置
};
```

## 2. 多语言实现原理

### 语言文件组织
- 所有语言文件位于 src/i18n/languages/ 目录下
- 每种语言都有独立的 TypeScript 文件（如 zh_CN.ts , zh_TW.ts , en.ts 等）
- 每个文件导出一个翻译对象，包含该语言的所有文本翻译

### 语言映射系统
在 src/i18n/translation.ts 中定义了语言映射：

```typescript
const map: { [key: string]: Translation } = {
  zh_cn: zh_CN,
  zh_tw: zh_TW,
  en: en,
  ja: ja,
  // ... 其他语言映射
};
```

### 语言获取函数

```typescript
export function i18n(key: I18nKey): string {
  const lang = siteConfig.lang || "en"; // 从配置中获取语言设置
  return getTranslation(lang)[key]; // 获取对应语言的翻译文本
}
```

## 3. 实际使用方式

在组件中通过 i18n() 函数调用翻译文本：

```typescript
{i18n(I18nKey.tags)}
```

## 4. 切换语言的方法

要切换网站显示语言，只需修改 src/config.ts 文件中的 lang 值：

```typescript
// 显示简体中文
lang: "zh-CN"

// 显示繁体中文
lang: "zh-TW"

// 显示英文
lang: "en"

// 显示日文
lang: "ja"
```

## 5. 支持的语言列表

目前模板支持以下语言：
- 英语 (en)
- 简体中文 (zh-CN)
- 繁体中文 (zh-TW)
- 日语 (ja)
- 韩语 (ko)
- 泰语 (th)
- 越南语 (vi)
- 印度尼西亚语 (id)
- 土耳其语 (tr)
- 西班牙语 (es)

## 6. 自定义语言

如果您需要添加新的语言支持：
1. 在 src/i18n/languages/ 目录下创建新的语言文件
2. 参照现有文件格式定义所有翻译键值对
3. 在 src/i18n/translation.ts 中添加新语言的映射
4. 在 src/config.ts 中设置 lang 为新语言代码