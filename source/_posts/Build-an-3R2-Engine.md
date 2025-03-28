---
title: Build an 3R2 Engine
tags: [三渲二, 计算几何, 记录,图形学,三维模型]
date: 2023-11-28 08:00:00
updated:
categories: 记录
keywords: 三渲二, 光栅化成象, 渲染管线, Texture
description: 通过修改光栅化成象的渲染材质实现三渲二效果
top_img: '/image/rp/3r2.png'
comments: true
cover: '/image/rp/3r2.png'
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
aplayer:
highlight_shrink:
aside: true
ai: true
swiper_index: 1
top_group_index: 1
background: "#fff"
---

通过修改光栅化成象的渲染材质实现三渲二效果
<!-- more -->
这周实现了一下比较火的三渲二，本来只是想做着玩玩，但没想到效果意外的还行，首先摆一下[项目地址](https://github.com/Joviisaus/3R2.git)

## 动机

起因是复习考研专业课的时候学到了管道设计模式，又想起来之前 Games101 在光栅化成相里，有介绍过一个叫渲染管线的东西。课虽然刷完了但是源码还没有好好分析，于是对那段源码分析了一下。

### 浅浅谈一下对渲染管线的理解

渲染的工作流程几乎是完全线性的，空间上并存时间上继起，也就是说前一模块的输出做下一模块的输入，很符合管道与过滤器架构的风格，所以称作渲染管线。这样带来的问题是不好在模块之间进行并行计算，多帧复用可以一定程度上缓解这样的问题，也就是说这个模块处理这一帧的内容，而前一个模块处理下一帧的内容，以提高计算速率

### 具体的实现方法

主要的类为 rasterizer 也就是光栅化，相比起管道架构，在执行过程中更像是使用仓库系统的思路传递信息。每个刚发方法修改一段属性，而并非直接传输数据，耦合性会更高一点，具体流程如下：

```
rst::rasterizer r(700, 700); //光栅结构初始化
r.set_texture(Texture(obj_path + texture_path));//设置材质模型
r.set_vertex_shader(vertex_shader);//设置点渲染方式
r.set_fragment_shader(active_shader);//设置渲染模型
r.clear(rst::Buffers::Color | rst::Buffers::Depth);//清空距离缓存
r.set_model(get_model_matrix(angle));//设置模型
r.set_view(get_view_matrix(eye_pos));//设置摄像机
r.set_projection(get_projection_matrix(45.0, 1, 0.1, 50));//设置视角

r.draw(TriangleList);//输出
cv::Mat image(700, 700, CV_32FC3, r.frame_buffer().data());
image.convertTo(image, CV_8UC3, 1.0f);
cv::cvtColor(image, image, cv::COLOR_RGB2BGR);

cv::imwrite(filename, image);
```

### Texture 模型渲染结果

![Texture](/image/rp/texture.png)

## 三渲二具体实现

在基于 Texture 的纹理贴图实现二维风格化图片输出

### Texture 纹理贴图方法

纹理贴图是在 Phong 模型的基础上实现的，简单概述一下原理就是将点的成像分为自发光，自然光，高光三个因素的和，即 $$ result\_{color} = ambient + diffuse + specular$$

自发光为常数，为方便检验，即使是牛牛我们也认为它是可以发出微弱的光芒的，即 RGB(10,10,10)

自然光主要表现为漫反射，在迭代三角形时，目光与三角面法向量的夹角还有外界光与三角面法向量的夹角差别不大时(可用 $$\\cos \\theta $$ 量化分析），发出相对稳定的光

高光主要表现为镜面反射，在迭代三角形时，目光与三角面法向量的夹角还有外界光与三角面法向量的夹角几乎一致时(可用 $$\\cos^n \\theta $$ 量化分析），发出耀眼的白光

通过规定三个光的参数，即可实现不同材质属性，Texture 材质的发光方式可通过下列定义表示

```
Eigen::Vector3f texture_fragment_shader(const fragment_shader_payload& payload)
{
    Eigen::Vector3f return_color = {0, 0, 0};
    if (payload.texture)
    {
        // TODO: Get the texture value at the texture coordinates of the current fragment
        return_color = payload.texture->getColor(payload.tex_coords.x(), payload.tex_coords.y());;
    }
    Eigen::Vector3f texture_color;
    texture_color << return_color.x(), return_color.y(), return_color.z();

    Eigen::Vector3f ka = Eigen::Vector3f(0.005, 0.005, 0.005);
    Eigen::Vector3f kd = texture_color / 255.f;
    Eigen::Vector3f ks = Eigen::Vector3f(0.7937, 0.7937, 0.7937);

    auto l1 = light{{20, 20, 20}, {500, 500, 500}};
    auto l2 = light{{-20, 20, 0}, {500, 500, 500}};

    std::vector<light> lights = {l1, l2};
    Eigen::Vector3f amb_light_intensity{10, 10, 10};
    Eigen::Vector3f eye_pos{0, 0, 10};

    float p = 150;

    Eigen::Vector3f color = texture_color;
    Eigen::Vector3f point = payload.view_pos;
    Eigen::Vector3f normal = payload.normal;

    Eigen::Vector3f result_color = {0, 0, 0};

    for (auto& light : lights)
    {
        auto light_direction = light.position - point;
        auto view_direction = eye_pos-point;
        auto centre_normal = light_direction+view_direction;
        auto d = light_direction.squaredNorm();
        auto ambient = ka.cwiseProduct(amb_light_intensity);
        auto diffuse = kd.cwiseProduct((light.intensity / d) * std::max(0.0f, normal.normalized().dot(light_direction.normalized())));
        auto specular = ks.cwiseProduct((light.intensity / d) * std::pow(std::max(0.0f, normal.normalized().dot(centre_normal.normalized())), p));
        result_color += (ambient + diffuse + specular);

    }

    return result_color * 255.f;
}
```

## 扁平化

去除自发光项和高光项，同时去除自然光在 Phong 模型下的参数变动，即可实现模型扁平化效果。

```
Eigen::Vector3f Three_R_Two(const fragment_shader_payload& payload)
{
    if(payload.isedge)
    {
        return Eigen::Vector3f(225.f,0,0);
    }
    Eigen::Vector3f result_color = {0, 0, 0};
    Eigen::Vector3f color = payload.color; 
    Eigen::Vector3f point = payload.view_pos;
    Eigen::Vector3f normal = payload.normal;
    Eigen::Vector3f return_color = {0, 0, 0};

    if (payload.texture)
    {
        return_color = payload.texture->getColor(payload.tex_coords.x(), payload.tex_coords.y());;
    }
    Eigen::Vector3f texture_color;
    texture_color << return_color.x(), return_color.y(), return_color.z();

    result_color = texture_color/255.f;

    return result_color * 255.f;
}
```

![3R2](/image/rp/3r2.png)

### 描边

二维图像在色彩扁平的基础上，往往还需要轮廓描绘来实现物体的色块区分。幸运的是，光栅化成象中有利用深度缓存属性来实现物体遮挡的效果，在描边过程里可以复用这一属性。

逻辑有点类似卷积，当某像素点的领域内具有差别较大的深度缓存时，将该点颜色设置为黑色。

```
    for(int x = 0 ; x < width ; x++)
    {
        for(int y = 0; y < height ; y++)
        {
            if(abs(depth_buf[get_index(x,y)]-depth_buf[get_index(x-1,y)]) > 0.01 || abs(depth_buf[get_index(x,y)]-depth_buf[get_index(x,y-1)]) > 0.01)
            {
                Eigen::Vector3f color = {0,0,0};
                Eigen::Vector2i point = Eigen::Vector2i(x, y);
                set_pixel(point,color);
            }
        }
    }
```
![40](/image/rp/40.png)
![90](/image/rp/90.png)
![180](/image/rp/180.png)
![135](/image/rp/135.png)
