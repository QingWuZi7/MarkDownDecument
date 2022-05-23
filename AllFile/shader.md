[TOC]

# 技术
- 多级渐远纹理——提前用滤波处理得到更小的像素，形成图形金字塔。多出33%的空间。

# 数学
- 向量点乘，求投影`dot(a,b)=|a|*|b|*cos()`
- 向量叉乘，求法向量(绝对是平行四边形的面积)`|cross(a,b)|=|a|*|b|sin()`

# unity属性
- 纹理贴图属性WrapMode：
    - Repeat：重复，像影分身一样
    - Clamp：边界外的直接使用边界坐标上的纹素值，像最后一个像素拖尾了一样


# 语义

```js
Shader "MyShaderName"{
    Properties{
        //属性:显示名称，类型，初始化
        _Color("showName",Color) = (1,1,1,1)
    }
    SubShader{
        //针对显卡A的SubShader
        Pass{
            //设置渲染状态和标签       
            -------------------------------
            |CGPROGRAM                    |               
            |//该代码片段的编译指令,例如:      |      
            |#pragma vertex fun1          |-->告诉Unity,哪个函数包含了顶点着色器的代码
            |#pragma fragment name        |-->哪个函数包含了片元着色器的代码,name指函数名
            |                             |           
            |//Cg代码写在这里               |-->实现名为name的函数   
            |                             |       
            |   cg代码声明变量 结构体以供使用等|
            |	fixed4 _Color;			  |
            |struct a2v{				//a:Application v:vertex shader顶点着色器
            |	//a2v就是把数据从应用阶段传递到顶点着色器中
            |    //内置函数TRANSFORM_TEX宏来计算经过平铺和偏移后的纹理坐标
            |    outVer.uv = TRANSFORM_TEX(ver.texcoord, _RampTex); //(tex,name)    
            |}
            |struct v2f{				//f:fragment 传递到片元着色器            
            |} 
            |ENGCG                        |   
            -------------------------------
           
            //其他设置
        }
        //其他需要的Pass
    }
    SubShader{
        //针对显卡B的SubShader
    }
    
    //上述SubShader都失败后用于回调的Unity Shader
    Fallback "VertexLit"
}

```

- 使用哪种Unity Shader  
    - 明确需要在非常旧的设备中运行，使用固定函数着色器
    - 和各种光源打交道，使用表面着色器，但需要小心再移动平台的性能表现
    - 光照数目少，顶点/片元着色器
    - 最重要的是，如果有很多自定义的渲染效果，使用顶点/片元着色器。

<b>Properties</b>

> <b>float高精度类型</b>，32位。通常用于世界坐标下的位置，纹理uv，或涉及复杂函数的标量计算，如三角函数、幂运算等  
> <b>half中精度类型</b>，16位。通常用于本地坐标下的位置、方向向量、HDR颜色等  
> <b>fixed低精度类型</b>，11位，通常用于常规的颜色与贴图，以及低精度间的一些运算变量等。数值范围为[-2,+2],但fixed4可以理解为一个四维向量或矩阵(1行4列)  
>
> > PC的Shader中half/fixed会当成float处理。所以在一些移动设备中有效。  
> > 比较常用的一个规则是，除了位置和坐标用float以外，其余的全部用half。主要原因也是因为大部分的现代GPU只支持32位与16位，也就是说只支持float和half，不支持fixed。
>
> ```js
> Properties{  
>     _Tex("Tex",2D)  = "white"{} //主纹理是有Tiling和Offset两个属性，所以需要额外的cg声明
> }
> CGPROGAM
> sampler2D _Tex;
> float4 _Tex_ST;	//(x,y,z,w)缩放和位移，有时候无需此属性，或多个纹理可以共用一个纹理属性变量
> ```
>
> 


|      语义      |   含义   |     Cg变量类型      |
| :------------: | :------: | :-----------------: |
|      Int       |   整型   |                     |
|     Float      |  浮点型  |  float,half,fixed   |
| Range(min,max) |  随机数  |  float,half,fixed   |
|     Color      | 颜色属性 | float4,half4,fixed4 |
|     Vector     | 向量属性 | float4,half4,fixed4 |
|       2D       | 纹理属性 |      sampler2D      |
|       3D       |          |     samplerCube     |
|      Cube      |          |      sampler3D      |

<b>在Unity中,顶点数据从哪里获得?</b>
> Mesh Filter组件中获取,其中有大量的顶点信息.可以在Wireframe模式下观察到各个顶点的连线信息.

<b>顶点数据如何输入到顶点着色器中?</b>
> 顶点着色器中可使用以下语义获得顶点数据:

|   语义    |       含义       |              结构               |
| :-------: | :--------------: | :-----------------------------: |
| POSITION  |     顶点位置     |    一般为float3或float4类型     |
|  NORMAL   |     顶点法线     |        一般为float3类型         |
| TEXCOORD0 |     纹理坐标     | 一般为float2,float3或float4类型 |
| TEXCOORD1 |   二级纹理坐标   |                                 |
|    ...    |       ...        |                                 |
|  TANGENT  | (顶点的)切线向量 |        一般为float4类型         |
|   COLOR   |    逐顶点颜色    |    一般为float4类型,性能友好    |


> 片元着色器中可使用以下语义获得顶点数据:

|    语义     | 含义 | 结构 |
| :---------: | :--: | :--: |
| SV_POSITION |      |      |
|   COLOR0    |      |      |
|   Color1    |      |      |
| TEXCOORD0~7 |      |      |

> 输出到片元着色器的语义:

|           |      |
| :-------: | :--: |
| SV_Target |      |

UnityShader中的内置矩阵`mul(_Object2World,v.vertex).xyz`
|       矩阵名       |                 描述                 |                                                              |
| :----------------: | :----------------------------------: | :----------------------------------------------------------: |
|  UNITY_MATRIX_MVP  |        当前的模型观察投影矩阵        |         用于将顶点/方向矢量从模型空间变换到裁剪空间          |
|  UNITY_MATRIX_MV   |          当前的模型观察矩阵          |         用于将顶点/方向矢量从模型空间变换到观察空间          |
|   UNTIY_MATRIX_V   |            当前的观察矩阵            |         用于将顶点/方向矢量从世界空间变换到观察空间          |
|   UNITY_MATRIX_P   |            当前的投影矩阵            |         用于将顶点/方向矢量从观察空间变换到裁剪空间          |
|  UNTIY_MATRIX_VP   |            当前的观察矩阵            |         用于将顶点/方向矢量从世界空间变换到裁剪空间          |
| UNITY_MATRIX_T_MV  |      UNITY_MATRIX_MVP的转置矩阵      |                                                              |
| UNITY_MATRIX_IT_MV |     UNITY_MATRIX_MV的逆转置矩阵      | 用于将法线从模型空间变换到观察空间<br>也可用于得到UNITY_MATRIX_MV的逆矩阵 |
|   _Object2World    | 当前的模型矩阵(unity\_ObjectToWorld) |         用于将顶点/方向矢量从模型空间变换到世界空间          |
|   _World2Object    |        _Object2World的逆矩阵         |         用于将顶点/方向矢量从世界空间变换到模型空间          |

cg函数  
`saturate()` 将参数截取到[0,1]范围内


UnityCG.cginc常用函数
| 函数名                                       | 描述                                                         |
| :------------------------------------------- | :----------------------------------------------------------- |
| 摄像机方向(视角方向)                         |                                                              |
| float3 WorldSpaceViewDir(float4 v)           | 根据模型空间中的顶点坐标 得到 （世界空间）从这个点到摄像机的观察方向 |
| float3 UnityWorldSpaceViewDir(float4 v)      | 世界空间中的顶点坐标==》世界空间从这个点到摄像机的观察方向   |
| float3 ObjSpaceViewDir(float4 v)             | 模型空间中的顶点坐标==》模型空间从这个点到摄像机的观察方向   |
| 光源方向                                     |                                                              |
| float3 WorldSpaceLightDir(float4 v)          | 模型空间中的顶点坐标==》世界空间中从这个点到光源的方向       |
| float3 UnityWorldSpaceLightDir(float4 v)     | 世界空间中的顶点坐标==》世界空间中从这个点到光源的方向       |
| float3 ObjSpaceLightDir(float4 v)            | 模型空间中的顶点坐标==》模型空间中从这个点到光源的方向       |
| 方向转换                                     |                                                              |
| float3 UnityObjectToWorldNormal(float3 norm) | 把法线方向 模型空间==》世界空间                              |
| float3 UnityObjectToWorldDir(float3 dir)     | 把方向 模型空间=》世界空间                                   |
| float3 UnityWorldToObjectDir(float3 dir)     | 把方向 世界空间=》模型空间                                   |


# 光照
> <b>着色</b>：根据材质属性(如漫反射属性等)，光源信息(如光源方向、辐照度等)，使用一个等式去计算沿某个观察方向的出射度的过程。我们也把这个等式称为<b>光照模型</b>(Lighting Model)

基本光照模型  
1. 漫反射——兰伯特定律

```math
c_{diffuse}=(c_{light} \cdot m_{diffuse})max(0,\widehat{n} \cdot \widehat{l} )
\\
半兰伯特c_{diffuse}=(c_{light} \cdot m_{diffuse})（0.5(\widehat{n} \cdot \widehat{l})+0.5）
```
- `$c_{light}$`入射光线的颜色和强度
- `$m_{diffuse}$`材质的漫反射颜色
- `n`表面法线
- `l`指向光源的单位矢量
    - `saturate(x)_Cg`防止法线和光源方向点乘的结果范围为[0,1]，物体被从后面来的光源照亮）

2. 高光反射  
    Phong模型
```math
反射方向的计算：\widehat{r} = 2(\widehat{n}\cdot \widehat{l})\widehat{n}-\widehat{l}
\\
c_{specular} = (c_{light} \cdot m_{specular})max(0,\widehat{v}\cdot \widehat{r})^{m_{gloss}}      
```
- `v`视角方向
- `$m_{specular}$`材质的高光反射颜色
- `$m_{gloss}$`材质的光泽度
    - 需要防止v*r的结果为负数
    - `reflect(i,n)_Cg[i:入射,n:法线方向]`

Blinn-Phong模型
```math
\widehat{h} = \cfrac{\widehat{v}+\widehat{l}}{|\widehat{v}+\widehat{l}|}
\\
c_{specular} = (c_{light} \cdot m_{specular})max(0,\widehat{n}\cdot \widehat{h})^{m_{gloss}}  
```
# 基础纹理

==纹理坐标转换：==

在顶点着色器中，使用纹理的属性值_MainTex_ST来对顶点纹理坐标进行变换，得到最终的纹理坐标。

使用缩放属性`_MainTex_ST.xy`对顶点纹理坐标进行缩放，然后使用偏移属性`_MainTex_ST.zw`对结果进行偏移。

`o.uv = ver.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw`

或者直接用内置宏`o.uv = TRANSFORM_TEX(_ver.texcoord,_MainTex)`



## 单张映射
## 凹凸映射
- 目的：使用一张纹理来修改模型表面的发线，以便为模型提供更多细节。
- 凹凸映射的两种方法
    1. 高度映射：使用一张高度图实现。计算更加复杂，高度图通常和法线映射一起使用
    2. 法线映射：一张法线纹理存储表面的法线方向。
- 两种纹理
    1. 模型空间下的法线纹理：记录模型的绝对法线信息，生成简单，比较直观；纹理坐标的缝合处、尖锐边角突变(缝隙)较少。
    2. 切线空间下的法线纹理：自由度高，可重用在其他网格上；可进行uv动画，比如水和熔岩；可重用法线纹理；可压缩，进存储XY方向。
    
### 写光照模型的shader,
> 统一各个方向矢量所在的坐标空间（法线纹理存储的法线是切线空间下的方向）。  
> <b>1.在切线空间下进行光照计算，需要把光照方向、视角方向变换到切线空间下。</b>  
> <b>2.在世界空间下进行光照计算，需将采样得到的法线方向变换到世界空间，在和世界空间下的光照方向和视角方向进行计算。</b>

分析：前者在顶点着色器中完成变换，效率高。后者更复杂但通用性强，因为有时需要在世界空间下进行一些计算。



# 对透明物体的处理

深度缓冲(depth buffer & z-buffer)——解决可见性

> 根据深度缓冲的值来判断该片元距离摄像机的距离，片元深度值和缓冲的值对比，舍弃或者覆盖颜色缓冲的像素值，从而决定像素是否被渲染。
>
> 所以使用深度缓冲不需要担心**不透明**物体的渲染顺序，但通过透明度混合实现透明效果时，深度写入(ZWrite)是关闭的。



## UnityShader的渲染顺序

(使用SubShader的Queue标签)：

|    名称     | 描述                                                         |
| :---------: | :----------------------------------------------------------- |
| Background  | 最先渲染，通常定义那些绘制在背景上的物体                     |
|  Geometry   | 默认的渲染队列，大多数物体都是用此队列，不透明物体使用此队列 |
|  AlphaTest  | 需要透明度测试的物体使用此队列，从Geometry独立出来，更加高效 |
| Transparent | 在以上队列完成后再按从后往前顺序进行渲染，任何使用透明度混合(如关闭了深度写入的shader)物体应使用 |
|   Overlay   | 实现一些叠加效果，任何需要再最后渲染的的物体使用此队列       |



## 实现透明的两种方法：

- 透明度测试`Tags{"Queue" = "Alpha"}`

对片元的透明度进行阈值判断，从而来决定片元是舍弃还是使用。

不需要关闭深度写入，效果极端，要么无，要么有

- 透明度混合`Tags{"Queue" = "Transparent"}`

使用当前片元的透明度作为混合因子，与已经存储再颜色缓冲中的颜色值进行混合，得到新的颜色。过程中 深度缓冲是**只读**的，这导致无法继续使用深度缓冲和相机距离比较的方法，渲染物体，所以有透明度混合的物体，**一定要注意渲染顺序**，即使是半透明物体之间。

> 出现循环重叠的物体时？或者视角和重叠的物体不是每每垂直的 怎么办？

- 分割网格。为了减少错误的排序情况，尽可能让模型是凸面体，考虑将复杂的模型拆分成可以独立排序的多个子模型等（这样不至于结果太糟糕）

- 即使不使用分割网格的办法，可以让透明通道更加柔和，使穿插看起来不是很明显。
- 也可以开启了深度写入的半透明效果来近似模拟物体的半透明。

```js
SubShader{
    Tags{"Queue" = "Transparent"}
    Pass{
        ZWrite Off//关闭深度写入，若写在SubShader下，则所有Pass都会关闭。
    }
}
```

Tags{"IgnoreProjector" = "True"}

# 动画

## 纹理动画

- 序列帧动画





# Q&A
> Scene和Game窗口的shader效果时隐时现？  

可能是同时运行的shader过多。

> 凹凸法线纹理为什么是五颜六色的，而且蓝色的偏多？

对于模型的每个顶点，都要一个属于自己的切线空间，由xyz三个轴组成的向量可以映射成RGB像素，计算公式是`pixel = (normal+1)/2 `如果不是凹凸的，模型空间的xy向量和切线空间的xy向量是一样的，则这个点存储的法线方向是(0,0,1)，映射后存储到纹理中对应了RGB(0.5,0.5,1)浅蓝色。

> Cannot resolve symbol '_MainTex_ST'.没有声明\_MainTex_ST