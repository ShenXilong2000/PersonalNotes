
# 一个新的渲染流水线
## 1.1 新建项目
创建一个新的3D项目用Unity 2019.2.6或者更低的版本。不要选择任何的渲染流水线模板，因为要自己创建一个。打开项目后， 到package manager移除所有不需要的包，在教学中只要使用Unity UI package来画UI，可以保留这个包。
我们要工作在linear color space(线性颜色空间)，但Unity 2019.2还在用gamma space（伽马空间）作为默认值。Go to the player settings via Edit / Project Settings and then Player, then switch Color Space under the Other Settings section to Linear.  

<center style="font-size:14px;color:#C0C0C0;">

![设置颜色空间为Linear](CustomRenderPipeline\color-space.png "设置颜色空间为Linear")
设置颜色空间为Linear

</center>

用一些物体填充场景，
## 1.2 流水线Asset
新建一个CustomRenderPipelineAsset 脚本，从UnityEngine.Rendering继承RenderPipelineAss