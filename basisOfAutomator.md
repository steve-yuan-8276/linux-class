---
title: 'Mac笔记： Automator都能干些啥（入门）'
date: 2018-03-06 17:31:54
categories: mac 
tags: [mac, automator] 
---

**Automator** 是Mac OS 的特色应用，相当于本地版的IFTTT，尤其是批量自动化操作，这个很有诱惑力。

### 基本操作

#### 工作界面

![](https://farm5.staticflickr.com/4773/25814359477_79eeb3c529_o.png)

**说明：**

1. 左侧有一个“资源库”栏，按应用程序或类别排序分类;

2. 显示可用于所选应用程序或类别的action;

3. 信息框，提供有关所选action的说明；

4. 文件夹选项，显示监控哪个文件夹或者操作；

5. 工作流程面板，可将左侧的action拖过来创建流程。

#### 基本操作步骤

1. 选择新建 `workflow`

2. 选择相应流程进行编辑操作。

3. 按住**option**，选择`save as`，保存到桌面。以后需要使用到时候，直接把文件或者文件夹拖拽到这个app就OK了。

<!--more-->

注意：此时保存到是application，如果希望保存为服务的话，还需要进行转换：

1. automator菜单选项中选择`file`，下拉菜单中点击 `convert to`，选择 `service`。

2. 在`service receives selected `后面选择 `file/file folder`，在in 后面选择 `finder`。

3. 保存即可。

![](https://farm5.staticflickr.com/4752/26779160578_9f200889ca_o.png)

### 工作流

#### 批量缩放图片：ZoomPicsTo600PX

工作的关系，需要经常更新微信公众号，用这个把图片一键缩放到600px，效率瞬间提升。

##### 实现方法：

###### 基础功能实现：
![](https://farm5.staticflickr.com/4603/39940645984_ca00108be4_o.png)

###### 功能优化：
![](https://farm5.staticflickr.com/4665/26800424048_a568a5818f_o.png)


#### 一键保存网页图片

![](https://farm5.staticflickr.com/4746/39774459005_1b4e82b026_o.png)

**注意：**

1. 建议保存为服务service，用safari打开网页，右键选择即可保存。 

2. 此方法对某些使用网站无效。

####  图片批量转化为PDF
 
 ![](https://farm5.staticflickr.com/4665/25801314147_f31bd87dce_o.png)
 
### 文件夹操作（folder action）
 
#### 自动缩放图片

**第一步：**新建 folder action

**第二步：**设置流程

![](https://farm5.staticflickr.com/4800/25812595577_555086bb5c_o.png)

**第三步：**设置文件夹，在 services 中选择 folder actions setup，如图操作

![](https://farm5.staticflickr.com/4796/39973720734_85a5227034_o.png)

#### 下载音乐文件自动归档

设置步骤跟前面类似，这里只截图最关键的部分。

![](https://farm5.staticflickr.com/4774/39788855335_654523eaca_o.png)

与之类似，图片、视频其他类型的文件都可以按照这个思路处理。

最后，更多automator的使用技巧，可以在[macosxautomation.com](https://macosxautomation.com/automator/index.html) 查找。






