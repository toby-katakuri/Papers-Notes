# 轻量化神经网络 移动端，终端边缘计算
## 压缩以训练好的模型
- 知识蒸馏
- 权重量化
- 剪枝（权重剪枝和通道剪枝）
- 注意力迁移
## 直接训练轻量化网络
- SqueezeNet
- MobileNet v1,v2,v3
- Mnasnet
- ShuffleNet v1,v2
- Xception
- Efficient Net
- Efficient Det
## 加速卷积运算
- im2col+GEMM
- Winograd
- 低秩分解
## 硬件部署
- TensorRT
- Jetson
- Tensorflow-slim
- Tensorflow-lite
- Openvino
- FPGA 集成电路

# 算法库
## OpenMMLab 模型压缩工具箱MMRazor:
- Prining 剪枝
- KD 知识蒸馏
- NAS 神经架构搜索
- Quantization 量化

[代码仓库](https://github.com/open-mmlab/mmrazor)

## OpenMMLab 模型压缩工具箱MMDeploy:
将**MMCls, MMDet, MMSeg, MMEdit, MMOCR, MMPose**部署到**ONNX, Openvino, TensorRT, OpenPPL, ncnn, PPLNN**上

[代码仓库](https://github.com/open-mmlab/mmdeploy)

[其他仓库：包括12个SOTA知识蒸馏算法的pytorch复现](https://github.com/HobbitLong/RepDistiller)

# Distilling the Knowledge in a Neural Network 论文
## Abstract
## Introduction
许多昆虫的幼虫形态最适合从环境中汲取能量和养分，而成虫形态则完全不同，最适合满足旅行和繁殖等截然不同的要求。

==training== must extract structure from very large, highly redundant datasets but it does not need to operate in real time and it can use a huge amount of computation.
Deployment to a large number of users, however, has much more stringent requirements on latency
and computational resources.
 
