---
title: excel合并表格
date: 2018-10-05 20:15:17
categories: 杂项教程
tags: [excel]
---

写在前面，朋友前几天找到我江湖救急，问我怎么合并多张表格，虽然是计算机专业毕业的我但是真的没做过几张表格啊，最后找了好多网站的资料介绍，找到下面可行的方法，回馈给需要的朋友。

在一个excel文件中如果存在几张附表，就可以使用下面的VBA代码合并附表到一个主表中。

方法是先新建一个主表，右键最下面的主表，然后选择查看代码，贴入下面的代码。然后保存运行即可发现已经合并成功。

---
方案一：
```vbscript
Sub main()

For Each sh In Sheets

If sh.Name <> "总表" Then

i = sh.Range("I65536").End(3).Row

k = Range("A65536").End(3).Row

sh.Range("A1:I50" & i).Copy Range("A" & k + 1)

End If

Next

End Sub
```
方案二：
```
Sub main()
Application.ScreenUpdating = False

For j = 1 To Sheets.Count

If Sheets(j).Name <> ActiveSheet.Name Then

X = Range("A65536").End(xlUp).Row + 1

Sheets(j).UsedRange.Copy Cells(X, 1)

End If

Next

Range("B1").Select

Application.ScreenUpdating = True

MsgBox "当前工作簿下的全部工作表已经合并完毕！", vbInformation, "提示"

End Sub
```
