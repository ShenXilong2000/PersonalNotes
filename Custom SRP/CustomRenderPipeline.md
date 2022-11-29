
# 一个新的渲染流水线
## 1.1 新建项目
创建一个新的3D项目用Unity 2019.2.6或者更低的版本(注意：需要UnityHub2.45版本或更低版本，否则在此版本的unity无法创建3D项目)。不要选择任何的渲染流水线模板，因为要自己创建一个。打开项目后， 到package manager移除所有不需要的包，在教学中只要使用Unity UI package来画UI，可以保留这个包。
我们要工作在linear color space(线性颜色空间)，但Unity 2019.2还在用gamma space（伽马空间）作为默认值。在Edit/Project Settings/Player中，点击Other Settings切换Color Space到Linear。

<center style="font-size:14px;color:#C0C0C0;">

![设置颜色空间为Linear](Custom SRP\CustomRenderPipeline\color-space.png "设置颜色空间为Linear")
设置颜色空间为Linear

</center>

用一些物体填充场景，使用一些standard, unlit opaque and transparent材质，其中Unlit/Transparent shader需要以下贴图。

<center style="font-size:14px;color:#C0C0C0;background-color: black">

![黑色背景下的UV球形透明贴图](Custom SRP\CustomRenderPipeline\sphere-alpha-map.png "黑色背景下的UV球形透明贴图")
</center>

<center style="font-size:14px;color:#C0C0C0;">
黑色背景下的UV球形透明贴图
</center>


## 1.2 流水线Asset
现在Unity用的是默认的渲染流水线。为了用自定义渲染流水线来替换它，我们必须先为它创建一个资产类型。我们直接使用跟Unity普通渲染流水线一样的文件结构。在Assets目录下创建Custom RP文件夹，再在其目录下创建Runtime子文件夹，在此文件夹下新建一个CustomRenderPipelineAsset 脚本。

<center style="font-size:14px;color:#C0C0C0;">

![文件夹结构](Custom SRP\CustomRenderPipeline\folder-structure.png "文件夹结构")
文件夹结构

</center>

此脚本需从UnityEngine.Rendering继承RenderPipelineAss