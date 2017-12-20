---
title: DirectX9.0 notes 1.3 预备知识
date: 2017-11-22 21:55:41
tags:
- DirectX
- Games

---

### 深度缓存(depth buffer)

> 定义: 一个含有特定像素的深度信息而不含图像数据的表面。
  用于计算每个像素而定深度值并进行深度测试: 让处于同一位置的不同像素进行竞争，选出应写入该位置的像素。距离摄像机最近的像素获胜。
  深度缓存格式决定深度测试精度, 精度度 16位<24位<32位, 一般选择24位满足效果

> * D3DFMT_D32         32位深度缓存
> * D3DFMT_D23S8       24位深度缓存, 其中8位保留供模板缓存(stencil buffer)[1]使用
> * D3DFMT_D24X8       24位深度缓存
> * D3DFMT_D16         16位深度缓存

### 顶点运算(Vertex Processing)

> * 软件顶点运算(software vertex process)
> * 硬件顶点运算(harfware ...)

软件顶点运算 ** 总是会被支持 ** , 硬件只有得到图形卡的支持才可使用。

优先考虑硬件顶点运算方式，理由：
> 1. 使用的硬件可能有加速功能
> 2. 可以不占用CPU， CPU可被解放出来进行其他运算

### 设备性能(Device Capabilities)

DeCaps 表示"Device Capabilities"
D3DCAP9::DevCaps的D3DDEVCAPS_HWTYRANSFORMANDLIGHT 可用来表示设备是否支持变换和光照的硬件计算。
多查阅D3DCAP9相关的SDK文档, 继续研究一下Directt3D所提供能够设备性能的完整列表。

























[1] : 18章论述
