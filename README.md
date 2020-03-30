# ExcelMergeTool
基于Excel和VBA输出Json的配置系统，基于Python,Openpy和SVN的Excel冲突解决系统
开发语言版本：Python 3.6.8

开发插件：openpyxl，Installer

工具Exe地址：ExcelMergeTool/dist/ExcelMerge.exe

# 功能简介**：**

远端和本地冲突的时候，检查并处理对同一sheet中不同数据检查区的修改，增加与删除的变更。检查并处理新增的非备注Sheet

## 基础概念：

![](https://tcs-ga.teambition.net/storage/111q827e0bc8c70787e29fb645d8bcd5c2df?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXE4MjdlMGJjOGM3MDc4N2UyOWZiNjQ1ZDhiY2Q1YzJkZiJ9.JMATnEqbVE95jwdaLfWhJX_sEHvZ02lebmnSLMFDruY&download=blob.png "")

图

### **表头：**

即用于设置Json字段名的区域。宽由Sheet有效宽度决定，高为第一列中第一个数据为数字的单元格行号（减去1）决定。

如果表头出现一下冲突，则跳过当前的sheet的合并处理。

- 相对位置的批注有变更

- 字段名字的修改

- 合并单元格信息出现变化，包括新增字段，删除字段，重新拆分或者合并单元格。

![](https://tcs-ga.teambition.net/storage/111r6ebbdb23cd2584230ec93ce11ee737b9?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXI2ZWJiZGIyM2NkMjU4NDIzMGVjOTNjZTExZWU3MzdiOSJ9.8XwhMaIydULYfChUD7eQX0__ydM8yej2hXPe4xGEqVc&download=blob.png "")



### **Sheet有效宽度：**

由表头中最长且连续不为空的行决定，数值是该行最后一个不为空的单元格决定



### **数据检查区**：

一个sheet会分为若干的检查区

检查区的确立的规则：第一列单元格或合并单元格的上下限，与表头的有效宽度，确立的一个矩形区域。如图a

如果遇到下面的冲突，则跳过当前的sheet的合并处理：

冲突双方出现同一id下的数据检查区都出现变更（增删改）的状态。

第一个使用版本中，**公式**的相对位置的变更**会触发修改变更**。

例：远端修改了id为2的数据检查区中的一个数值，本地在id为2检查区下新增了一行数据。

### **常规表结构：**

![](https://tcs-ga.teambition.net/storage/111qf1a9cc03c819cf7ac0123ed3afe1c571?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXFmMWE5Y2MwM2M4MTljZjdhYzAxMjNlZDNhZmUxYzU3MSJ9.ttd44ANhGn4I7dn9BHVy3suceqDgDVA4wbYS9upx4a4&download=blob.png "")

### **一级合并单元格表结构：**

![](https://tcs-ga.teambition.net/storage/111qc70d10d224409dbb102a9d0d0677b2a9?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXFjNzBkMTBkMjI0NDA5ZGJiMTAyYTlkMGQwNjc3YjJhOSJ9.LbsTPnnzdMx_4RFjRVDl5KKDkk031YtdlcmQeNu6sKo&download=blob.png "")

### **二级合并单元格表结构：**

![](https://tcs-ga.teambition.net/storage/111q9b75a210269dad0a37b97a373674bd78?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXE5Yjc1YTIxMDI2OWRhZDBhMzdiOTdhMzczNjc0YmQ3OCJ9.V1bIzSk-DhmkWeHSYF7a8uDz21TdWp_wqiZgHPpxaec&download=blob.png "")



# 注意事项（务必阅读）：

### 正常关闭的操作以及异常关闭的恢复：

合并流程会创建3个临时文件，程序结束时需要恢复状态，删除3个文件。关闭程序的方式应当是在结束后，**只有在窗口内输入任意键**，程序才能自己恢复原始状态。

异常退出的后果，会导致下一次的合并操作无法正常进行，亦或是合并逻辑错误。

异常退出后需要采用手动方案来删除临时文件。即删除 *_合并工具日志文件.txt，*_copy_here1.xlsm，*_copy_here2.xlsm 三个文件

![](https://tcs-ga.teambition.net/storage/111r222f22502a3e5a1aba6a326e04a23c10?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXIyMjJmMjI1MDJhM2U1YTFhYmE2YTMyNmUwNGEyM2MxMCJ9.KMkg1_teItzye8UU2eE5teNrRD-Q2ePDTIbXLidcEHY&download=blob.png "")

![](https://tcs-ga.teambition.net/storage/111r7a6c9b30ff6e1eded57e3e474a5d8a4b?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXI3YTZjOWIzMGZmNmUxZWRlZDU3ZTNlNDc0YTVkOGE0YiJ9.SGgD1X33DhG10EB47giCb2HHXwuuMqrzh05NVCq2Aq8&download=blob.png "")

### 程序运行需要关闭相关文件

以Character.xlsm为例子，程序在运行时，**请务必关闭以下6个文件**，有可能导致程序异常退出，如果遇到异常退出的情况，请按照【正常关闭的操作以及异常关闭的恢复】操作处理解决

![](https://tcs-ga.teambition.net/storage/111reed0301254005f9916a4b41ff0516bd6?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXJlZWQwMzAxMjU0MDA1Zjk5MTZhNGI0MWZmMDUxNmJkNiJ9.D_Eehei0RIK38SpKPwt-FecQZymtSCWsmZBDLLo8Fjg&download=blob.png "")

### 检查区域之外的数据有可能会丢失

由于新增和删除的处理是新增和删除行，故有可能会误删有效区域外的信息。

合并处理，不出检查和判断有效区域外的内容如下图，故不建议在有效区域外填写解释性的文字。

![](https://tcs-ga.teambition.net/storage/111q5270a4b18c59e91c8903ff6cf5ede71f?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXE1MjcwYTRiMThjNTllOTFjODkwM2ZmNmNmNWVkZTcxZiJ9.8Gzc2atHbsuCdaUyJ7od3dyI6QGhUpw0ubYoQWLYY8g&download=blob.png "")



### **公式变化也会引起差异变更**

由于openpyxl的某些限制，暂时没有比较合适的公式差异解决方案，所以当遇到**公式变更时也认定其为常规变更**。后续视情况再决定是否提供支持。

如图 当遇到数据变更时会把对应的数据打印出来，若数据比较庞大，也可以在*_合并工具日志文件.txt中查看相关内容

*_合并工具日志文件.txt 的存在周期，同对应窗口的周期一致（前提时正常退出）

![](https://tcs-ga.teambition.net/storage/111r694b2d4ed74534d22d9741b6ad0a45d0?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXI2OTRiMmQ0ZWQ3NDUzNGQyMmQ5NzQxYjZhZDBhNDVkMCJ9.Zr9YB2pkfZadV_f0ZnRB5V16aYXkClx-lpXcqV8fM4A&download=blob.png "")



### 来自开发者的几句废话：

工具完成初期，可能会出现在测试中没有遇到的情况，初期还是希望大家在使用工具时，**务必检查输出结果是否同自己的修改一致。**当遇到非常规情况，或者问题时，需要将如图的三个文件（后缀为.r0000的文件为SVN的冲突文件）保存，程序调试需要这样的三个文件

![](https://tcs-ga.teambition.net/storage/111rd799b0e5c09f4f08ddfc117547a70d44?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXJkNzk5YjBlNWMwOWY0ZjA4ZGRmYzExNzU0N2E3MGQ0NCJ9.6z4K_JC752OXLDc8TrS8lu5UzuXMU7s5-MDMzRQ6R4s&download=blob.png "")

# 合并工具得配置

1.更新工具SVN地址或者工具Exe地址的内容

2.MergeToolApp.exe并复制其绝对路径

3/找到svn设置窗口，点击Advanced

![](https://tcs-ga.teambition.net/storage/111qd4778eb147d08cdc131b5028f9426a47?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXFkNDc3OGViMTQ3ZDA4Y2RjMTMxYjUwMjhmOTQyNmE0NyJ9.R88rLEo7kq0W4t3_ZDK-vLlv9Z4BfJyJRPFRY8Q0NtE&download=blob.png "")

![](https://tcs-ga.teambition.net/storage/111qfb706de5f5196640a55f45647a478130?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXFmYjcwNmRlNWY1MTk2NjQwYTU1ZjQ1NjQ3YTQ3ODEzMCJ9.dXRuoAAj7mlK_p3xUogJOMz87D7NhWiD4F6vBzNdho4&download=blob.png "")

4.弹框中没有.xlsm的选项就点击add，有得话就点击edit。

![](https://tcs-ga.teambition.net/storage/111qc137c73cc89eb1543c13ad4b67425bb7?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXFjMTM3YzczY2M4OWViMTU0M2MxM2FkNGI2NzQyNWJiNyJ9.JCzS8DKxiRIGBuYECmjscc6lt7RWCyMLkpMWLLyAzv8&download=blob.png "")

5.在后续的窗口中填入 刚刚复制的exe路径 %merged %theirs %mine %base

例如我填写的内容：

D:\SvnProject\Tools\ExcelMergeTool\dist/ExcelMerge.exe %merged %theirs %mine %base

![](https://tcs-ga.teambition.net/storage/111q453723dbce369e2f3f083ba035ac4750?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXE0NTM3MjNkYmNlMzY5ZTJmM2YwODNiYTAzNWFjNDc1MCJ9.OUalA1G9KyR7x38xEUs1gXU8Xm0U0rIH7FIZapKAGyE&download=blob.png "")

6.点击ok，点击ok，点击应用。

7.设置成功

# 合并工具的使用**：**

遇到xlsm的冲突时，右键对应选项，在弹出框内点击Edit Conflicts，弹出命令行窗口，即代表程序已经运行。

也可以双击该冲突

然后静静的等待，等程序跑完。

![](https://tcs-ga.teambition.net/storage/111qc768ba834e6cfb8a1e38493240ef7410?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXFjNzY4YmE4MzRlNmNmYjhhMWUzODQ5MzI0MGVmNzQxMCJ9.YuVZL4faJvqQueKpYWio4O9k5WUIetQehUdcvwZKuIU&download=blob.png "")

![](https://tcs-ga.teambition.net/storage/111q22e767e93081cf2f23ebf25208ea13c0?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXEyMmU3NjdlOTMwODFjZjJmMjNlYmYyNTIwOGVhMTNjMCJ9.0pD_FriWY1ydJnF4OEZRPZYz-GgamNyk8SDBm8qXp9s&download=blob.png "")

![](https://tcs-ga.teambition.net/storage/111q39bfa43569d0b46e518fe51f2f7e9f11?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTQ0NSwiaWF0IjoxNTg1NTYwNjQ1LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXEzOWJmYTQzNTY5ZDBiNDZlNTE4ZmU1MWYyZjdlOWYxMSJ9.PrgHPqBe0T1j1rTAHwAbUXrOuWmDfFGLfUlmG9ClqPY&download=blob.png "")



# 关于SVN:

Svn的文件冲突创建会有4个抽象概念即Base,Mine,Their,Merge

Svn发生冲突时，会创建两个文件，一个是本地svn的基础版本（Base）一个是远端更新下来的待处理版本（Their）然后加上本地的原始文件（Mine），三者进行比较差异，然后写入Mine。在当前版本SVN文件处理中其实Merge和Mine是一个文件。

# openpyxl的基础概念

## **workbook：**

 工作簿，一个excel文件包含多个sheet。

load_workbook(filename = path_xlsm, read_only=False, keep_vba=True,data_only=True)

read_only设置只读模式，openpyxl只从excel中解析数据，不加工。

keep_vba当有vba脚本的workbook对象调用save()接口时，若keep_vba为false，手动打开该excel文件，则会出现文件损坏的错误。

data_only数据模式，为true时cell的value为公式的结果，false时，cell的value为公式内容。同一个workbook不可以切换data_only的状态。

同一个excel可以加载多个workbook对象

## **sheet**：

工作表，一个workbook有多个，表名识别，如“sheet1”,“sheet2”等。

主要数据对象为一个cell的二维数组、mergecell的list

## **cell： **

单元格，存储数据对象

其bounds元组定义的顺序是：0-3 min_col, min_row, max_col, max_row

## **mergecell：**

合并单元格对象。一个mergecell包含多个cell,这多个cell中只有左上角的cel可l设置数据，其他cell设置数据时会read_only的错误

# 实现方案简介：

比较方案：通过字符串的比较

比较单位的确立：MergeCell的启发确立一个CBound对象，只记录key和数据检查区的4个顶点的坐标。

[【传送门】thoughts中的合并工具说明文档](https://thoughts.teambition.com/share/5e6aef3ba1fa16001aea3cbc#title=Excel%E5%90%88%E5%B9%B6%E5%B7%A5%E5%85%B7%E7%9A%84%E4%BD%BF%E7%94%A8%E4%BB%8B%E7%BB%8D)。

基础的类型判断：新增数据，修改数据，删除数据，新增Sheet。

方案就是以sheet的第一列的单元格或是合并单元格收集纵向信息，表头的有效宽度收集横向信息，以确定多个不同的数据检查区。

然后相同Sheet的检查区逐一比对，以获得有差异检查区的key和范围。Base与Mine比较，Base与Their比较。然后将得到的key和范围，读取想应得数据，然后写入Mine。

合并工具的的工作分为以下几个部分

1.      解析excel

2.      收集差异

3.      判断对应sheet是否可合并

4.      应用差异。包括新增sheet，新增数据，删除数据，修改数据

这里介绍两个基础得部分收集差异和应用差异：

# 收集差异：

## **修改：**

差异的收集采用了行字符串拼接比对的方案，牺牲差异结果得精度，尽可能短的降低耗时。

做法是遍历数据检查区的行单元格，然后拼接成一个字符串。如下图id为1行，拼接出来的数据应当是1_32001_53111_53111_1_1_0_40，其key为1存进字典。然后相同的key进行比较，字符串不相同的数据检查区就视为有修改。

## **删除与新增：**

这里利用了Python中的集合，利用差集来判断

Mine与Base中数据检查区的差集，就作为新增。Their与Base同理。

Base与Mine中数据检查区的差集，就作为删除。Base与Their同理。

![](https://tcs-ga.teambition.net/storage/111rcc7069840c04bebc9ba249aa0bee072a?Signature=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBcHBJRCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9hcHBJZCI6IjU5Mzc3MGZmODM5NjMyMDAyZTAzNThmMSIsIl9vcmdhbml6YXRpb25JZCI6IiIsImV4cCI6MTU4NjE2NTI0OSwiaWF0IjoxNTg1NTYwNDQ5LCJyZXNvdXJjZSI6Ii9zdG9yYWdlLzExMXJjYzcwNjk4NDBjMDRiZWJjOWJhMjQ5YWEwYmVlMDcyYSJ9.2NOu7TWi6zvat8b_ffGhP3ZRfb4RHxQOKYzuPiIA1CU&download=blob.png "")

# 应用差异:

差异写入Mine的时候，会出现以下情况：

差异的内容包括key,范围，范围内的数据。

## **1.       范围不匹配。**

先适配范围，根据差异的范围来确定Mine中对应区域是新增行还是删除行。

然后收集对应区域内所有的合并单元格，接着将收集的合并单元格全部拆分。

删除行，或者新增。

遍历sheet内有的合并单元格，找出在修改区域下方的合并单元格，然后根据删除或新增的行数对合并单元格进行偏移。

## 2.**       数据不同。**

到写入数据这一步时，同一个key的情况下，差异的范围与Mine的数据检查区的范围时相同的。只需通过遍历将差异中的数据写入Mine中即可