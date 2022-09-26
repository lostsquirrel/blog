---
title: "Onenote 目录生成器(半残)"
date: 2022-09-26T15:38:20+08:00
tags: [onenote,addins,tool]
categories:
draft: false

---

## 安装

1. 下载 https://o.shangao.tech/manifest.xml 到本地
2. 在浏览器中打开 onenote 页面
3. 切换到 插入(Insert) 菜单，选择 Office 外挂程序(Office Add-ins)

{{<b2img "/onenote-install-01.png" "安装">}}

4. 选择 我的外挂程序(My Add-ins), 点击 上传外挂程序 (Upload My Add-in)

{{<b2img "/onenote-install-02.png" "上传">}}

5. 选择 第1步下载的 `manifest.xml`, 点击 上传 按钮

## 使用

1. 选择 开始(Home) 菜单， 点击 `Show Taskpane` 按钮

{{<b2img "/onenote-use-01.png" "菜单">}}

2. 右侧会弹出操作说明及操作按钮， 选中需要创建目录的 Onenote Outline, 点击工具中的 Generate 按钮
{{<b2img "/onenote-use-02.png" "生成">}}

3. 页面顶会生成一个新的包含目录的 Outline

{{<b2img "/onenote-use-03.png" "结果">}}

## 修正

1. 由于在调用 `page.addOutline(1,1, <a href="onenoet:xxx">)` 时， `href` 的内容会被替换，因此的连接前加前缀 `https://o.shangao.tech/`, 现在需要手动编辑去掉这年前缀,并替换为 `onenote:`

{{<b2img "/onenote-fix-01.png" "菜单">}}
{{<b2img "/onenote-fix-02.png" "菜单">}}
{{<b2img "/onenote-fix-03.png" "菜单">}}

## 更新

1. 在地址中添加 `onenoet:` , 修正时只需要移除前缀 `https://o.shangao.tech/`