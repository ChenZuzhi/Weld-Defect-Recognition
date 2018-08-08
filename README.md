# 基于迁移学习的焊缝缺陷识别

## 1.介绍
<br>
weld_defect文件夹包含了焊缝缺陷识别的所有相关代码，图片数据等。我们使用了迁移学习的方法，重新训练的Google的inception_v3和mobilenet_1.0_224模型。通过接口文件将训练好的模型与图片接口联系起来，以实现对焊缝缺陷的自动识别。包含的代码有：重训练模型的代码，生成缺陷图片的代码，接口文件，图片接口文件还有一些有用的工具代码。<br>
<br>
每一个子文件夹的内容大致如下:<br>
* Orig_Img:保存了企业给的原始焊缝缺陷图片以及这些原始图片经过预处理后的图片，比如从原始图片中抽取出来的焊缝图片，不同焊缝缺陷分类好的图片以及背景图片。
* Generate_Defect_Img:里面是计算机生成焊缝缺陷图片的相关代码和图片，包含生成对抗网络(GANs)和随机数特征生成两种方法。<br>
* retrain：里面保存了迁移学习重新训练inception_v3和mobilenet_1.0_224的相关代码。<br>
* retraining_img：里面保存了包括：迁移学习再训练用到的图片(也就是数据集)和验证模型的图片。<br>
* trained_model：里面保存了迁移学习预训练模型经过再训练后得到模型。<br>
* predict:里面是检测焊接缺陷的主程序(Detece_Img_Test.py)和相关成员函数(放在Detect_Img.py中),可以认为是输入图片的接口。<br>
* interface:里面存放了主程序与模型的接口代码。<br>
* Test_Img:里面保存了待预测的图片（包括企业给的焊缝图片和自己生成的用于验证模型的图片）。<br>
* Final_Img:里面保存了预测好后可视化之后的图片，也就是最终图片。<br>
* tools:里面保存了一些有用的工具文件<br>
<br>
<br>

## 2.运行环境
<br>
操作系统：Ubuntu 18.04<br>
我们使用了Python语言编写的这些代码。如果要成功运行，需要在安装tensorflow和openCV。<br>
<br>
<br>

## 3.生成缺陷图片
<br>
由于企业给的图片太少，不足以作为迁移学习的数据集。因此我们需要自己生成缺陷图片。<br>
生成缺陷图片的方法主要有两种：(1)生成对抗网络(GANs)，(2)随机数特征+openCV<br>

<br>
<br>
