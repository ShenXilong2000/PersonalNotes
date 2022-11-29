
# 一个新的渲染流水线

## 1.1 新建项目
创建一个新的3D项目用Unity 2019.2.6或者更低的版本(注意：需要UnityHub2.45版本或更低版本，否则在此版本的unity无法创建3D项目)。不要选择任何的渲染流水线模板，因为要自己创建一个。打开项目后， 到package manager移除所有不需要的包，在教学中只要使用Unity UI package来画UI，可以保留这个包。
我们要工作在linear color space(线性颜色空间)，但Unity 2019.2还在用gamma space（伽马空间）作为默认值。在Edit/Project Settings/Player中，点击Other Settings切换Color Space到Linear。

<p align="center" >
<img  align="center" src="CustomRenderPipeline\color-space.png" style="background-color: black">
</p>

<p align="center" style="font-size:14px;color:#C0C0C0;">
设置颜色空间为Linear
</p>

用一些物体填充场景，使用一些standard, unlit opaque and transparent材质，其中Unlit/Transparent shader需要以下贴图。

<p align="center" >
<img  align="center" src="CustomRenderPipeline\sphere-alpha-map.png" style="background-color: black">
</p>

<p align="center" style="font-size:14px;color:#C0C0C0;">
黑色背景下的UV球形透明贴图
</p>

我放了一些不透明的cube在测试场景中，红色的材质使用Standard shader， 绿色的和黄色材质使用Unlit/Color shader。再加入蓝色Spheres使用Standard shader更改Rendering Mode为Transparent， 白色Spheres使用Unlit/Transparent shader， 这两个都要使用上面的球形透明贴图。

<p align="center" >
<img  align="center" src="CustomRenderPipeline\scene.png" style="background-color: black">
</p>

<p align="center" style="font-size:14px;color:#C0C0C0;">
测试场景
</p>

## 1.2 流水线Asset
现在Unity用的是默认的渲染流水线。为了用自定义渲染流水线来替换它，我们必须先为它创建一个资产类型。我们直接使用跟Unity普通渲染流水线一样的文件结构。在Assets目录下创建Custom RP文件夹，再在其目录下创建Runtime子文件夹，在此文件夹下新建一个CustomRenderPipelineAsset 脚本。

<p align="center" >
<img  align="center" src="CustomRenderPipeline\folder-structure.png" style="background-color: black">
</p>

<p align="center" style="font-size:14px;color:#C0C0C0;">
文件夹结构
</p>

此脚本需从UnityEngine.Rendering继承RenderPipelineAsset。

``` c#
using UnityEngine;
using UnityEngine.Rendering;

public class CustomRenderPipelineAsset : RenderPipelineAsset {}
```

这个渲染流水线asset的主要目的是为Unity提供一种获取负责渲染的流水线实例的方法。这个asset只是一个存储设置的地方（原文提到是a handle？？不懂）。我们现在还不需要任何设置，我们所要做的就是给Unity一个去获取我们的流水线实例的方法。那个被返回RenderPipeline实例的CreatePipeline抽象方法重写。但是我们还没有定义一个自定义渲染流水线，所以返回null。
这个CreatePipeline抽象方法被protected修饰，这说明只有RenderPipelineAsset以及继承与它的类可以访问它定义它。（此段有些不懂）

``` c#
protected override RenderPipeline CreatePipeline () {
    return null;
}
```

现在我们需要给我们的项目添加一个asset，为了这样可行，我们需要给CustomRenderPipelineAsset添加CreateAssetMenu注解。

``` c#
[CreateAssetMenu]
public class CustomRenderPipelineAsset : RenderPipelineAsset { … }
```

放一个入口在Asset/Create menu，整洁一点把它放在Rendering子菜单。通过在注解中设置menuName属性为Rendering/Custom Render Pipeline。此属性可以直接设置在注解类型之后，用圆括号括起来。

``` c#
[CreateAssetMenu(menuName = "Rendering/Custom Render Pipeline")]
public class CustomRenderPipelineAsset : RenderPipelineAsset { … }
```

使用新的菜单项去添加asset到项目里，到Graphics project settings选择它在Scriptable Render Pipeline Settings下。

<p align="center" >
<img  align="center" src="CustomRenderPipeline\srp-settings.png" style="background-color: black">
</p>

<p align="center" style="font-size:14px;color:#C0C0C0;">
选择自定义渲染流水线
</p>

替换掉默认的渲染流水线改了很多东西，首先很多在信息面板中提到过选项从graphics settings消失了，其次，我们在没有提供有效替换的情况下禁用了默认RP，因此不会再呈现任何内容。游戏窗口、场景窗口和材料预览不再有效。如果你打开框架调试器——通过窗口/分析/框架调试器——并启用它，你会看到游戏窗口中确实没有绘制任何东西。

## 1.3 渲染流水线实例