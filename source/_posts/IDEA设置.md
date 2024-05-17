---
title: IDEA设置
tags: [IDEA,设置]
categories: [IDEA]
cover: /images/jt/blog/1343747.png
banner: /images/jt/blog/1343747.png
poster:
  topic: IDEA中的个性设置
  headline: IDEA设置
date: 2024-05-17 10:30:43
description:
---

# IDEA中打开md文档设置

1. 打开设置界面：
   在IntelliJ IDEA中，点击菜单栏上的 File > Settings（Windows/Linux用户）或 IntelliJ IDEA > Preferences（Mac用户）。

2. 导航至外部工具设置：
   在设置窗口中，依次选择 Tools > External Tools。

3. 添加新的外部工具：
   点击右侧的加号 + 添加一个新的工具。

4. 配置Typora：

- Name: 输入一个便于识别的名称，例如 Open with Typora。

- Program: 选择Typora的可执行文件路径。通常在安装Typora后，可以在其安装目录下找到，例如 C:\Program Files\Typora\Typora.exe（Windows路径示例）或 /Applications/Typora.app/Contents/MacOS/Typora（Mac路径示例）。

- Arguments: 输入 $FilePath$，这会将当前选中的.md文件路径作为参数传递给Typora。

- Working directory: 保留为 $ProjectFileDir$，这样Typora会在当前项目的目录下打开文件。

5. 应用并确定：
     配置完成后，点击 OK 或 Apply 按钮保存设置。

6. 使用配置好的外部工具
     在项目中右键点击任何.md文件，然后在弹出的菜单中选择 External Tools > Open with Typora（或你之前设定的名称），即可用Typora打开该文件。
