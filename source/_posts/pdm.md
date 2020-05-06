---
layout: post
title:  "从数据库中生成pdm"
date:   2019-01-29
categories:
 - PowerDesigner
tags:
 - pdm
color: rgb(230,230,250)
cover: '../assets/blogimg/2018-10-24.png'
---
# 从 mysql 中导出 pdm 步骤记录

- mysql 版本：5.7
- PowerDesigner 版本：15.2

## 生成PDM

首选导出建表语句，然后打开PowerDesigner， 选择文件-> 反向工程 -> database

![](/images/pdm/create.png)

设置模型名称，点击确定。点击使用脚本文件，选择对应的建表语句点击确定即可生成 pdm 文件

![](/images/pdm/createpdm.jpg)

## 调整模型

在 PDM 空白处右键----->显示首选项，选择 table--->Advanced, 在弹出界面中选择 Columns, 点击 List columns对应的放大镜图标, 通过上下箭头调整列显示顺序。

![](/images/pdm/colunms.jpg)

对应以下顺序，这时候的Name是和Code列一致的，都是显示列名，Name列要替换为注释

![](/images/pdm/colunms1.jpg)

## 替换Name列为中文注释

点击工具---> Execute Commands----> Edit/Run Script,执行以下脚本即可

![](/images/pdm/script.png)

```
    Option   Explicit   
    ValidationMode   =   True   
    InteractiveMode   =   im_Batch
    Dim blankStr
    blankStr   =   Space(1)
    Dim   mdl   '   the   current   model  
      
    '   get   the   current   active   model   
    Set   mdl   =   ActiveModel   
    If   (mdl   Is   Nothing)   Then   
          MsgBox   "There   is   no   current   Model "   
    ElseIf   Not   mdl.IsKindOf(PdPDM.cls_Model)   Then   
          MsgBox   "The   current   model   is   not   an   Physical   Data   model. "   
    Else   
          ProcessFolder   mdl   
    End   If  
      
    Private   sub   ProcessFolder(folder)   
    On Error Resume Next  
          Dim   Tab   'running     table   
          for   each   Tab   in   folder.tables   
                if   not   tab.isShortcut   then   
                      tab.name   =   tab.comment  
                      Dim   col   '   running   column   
                      for   each   col   in   tab.columns   
                      if col.comment = "" or replace(col.comment," ", "")="" Then
                            col.name = blankStr
                            blankStr = blankStr & Space(1)
                      else  
                            col.name = col.comment   
                      end if  
                      next   
                end   if   
          next  
      
          Dim   view   'running   view   
          for   each   view   in   folder.Views   
                if   not   view.isShortcut   then   
                      view.name   =   view.comment   
                end   if   
          next  
      
          '   go   into   the   sub-packages   
          Dim   f   '   running   folder   
          For   Each   f   In   folder.Packages   
                if   not   f.IsShortcut   then   
                      ProcessFolder   f   
                end   if   
          Next   
    end   sub  
```

## 重新渲染PDM
点击符号--->自动布局，选择合适的布局模型。

![](/images/pdm/render.jpg)








