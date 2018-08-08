# 基于迁移学习的焊缝缺陷识别

## 1.介绍


weld_defect文件夹包含了焊缝缺陷识别的所有相关代码，图片数据等。我们使用了迁移学习的方法，重新训练的Google的inception_v3和mobilenet_1.0_224模型。通过接口文件将训练好的模型与图片接口联系起来，以实现对焊缝缺陷的自动识别。包含的代码有：重训练模型的代码，生成缺陷图片的代码，接口文件，图片接口文件还有一些有用的工具代码。

每一个子文件夹的内容大致如下:

* ``Orig_Img``:保存了企业给的原始焊缝缺陷图片以及这些原始图片经过预处理后的图片，比如从原始图片中抽取出来的焊缝图片，不同焊缝缺陷分类好的图片以及背景图片。

* ``Generate_Defect_Img``:里面是计算机生成焊缝缺陷图片的相关代码和图片，包含生成对抗网络(GANs)和随机数特征生成两种方法。

* ``retrain``：里面保存了迁移学习重新训练inception_v3和mobilenet_1.0_224的相关代码。

* ``retraining_img``：里面保存了包括：迁移学习再训练用到的图片(也就是数据集)和验证模型的图片。

* ``trained_model``：里面保存了迁移学习预训练模型经过再训练后得到模型。

* ``predict``:里面是检测焊接缺陷的主程序(Detece_Img_Test.py)和相关成员函数(放在Detect_Img.py中),可以认为是输入图片的接口。

* ``interface``:里面存放了主程序与模型的接口代码。

* ``Test_Img``:里面保存了待预测的图片（包括企业给的焊缝图片和自己生成的用于验证模型的图片）。

* ``Final_Img``:里面保存了预测好后可视化之后的图片，也就是最终图片。

* ``tools``:里面保存了一些有用的工具文件


## 2.运行环境

操作系统：Ubuntu 18.04<br>
我们使用了Python语言编写的这些代码。如果要成功运行，需要在安装tensorflow和openCV。



## 3.生成缺陷图片

由于企业给的图片太少，不足以作为迁移学习的数据集。因此我们需要自己生成缺陷图片。

生成缺陷图片的方法主要有两种：(1)生成对抗网络(GANs)，(2)随机数特征+openCV

具体生成方法见文件夹"Generate_Defect_Img"中的readme

## 4.使用迁移学习训练模型

首先，先将数据集整理好在一个文件夹中，在本问题中，我们将缺陷类型分为"BURNTHROUGH","CRACK","POROSITY"和"NORMAL"四类，每一类都生成了大约2000张图片。我将这些缺陷图片放到对应的文件夹中并放在服务器上的下列路径中：

```
/home/sjtu/Desktop/weld_defect/retraining_img/retraining_all_2000/BURNTHROUGH
/home/sjtu/Desktop/weld_defect/retraining_img/retraining_all_2000/CRACK
/home/sjtu/Desktop/weld_defect/retraining_img/retraining_all_2000/POROSITY
/home/sjtu/Desktop/weld_defect/retraining_img/retraining_all_2000/NORMAL
```

接下去打开终端,然后切换工作目录到retraining代码文件的路径(我放在/home/sjtu/Desktop/weld_defect/retrain/中，代码文件名为retrain.py)

```
cd /home/sjtu/Desktop/weld_defect/retrain
```

接下去就可以执行文件以训练模型了，执行文件的方法如下：

首先，如果是要训练inception_v3模型，那么在终端中输入以下代码即可：

```
python retrain.py --image_dir /home/sjtu/Desktop/weld_defect/retraining_img/retraining_all_2000
```

等待训练完成后，训练好的模型保存在 计算机/tmp/ 中，文件名为output_graph.pb和output_labels.txt。<br>
分了方便区别inception和mobilenet两个模型，将output_graph.pb重命名为output_graph_incep.pb<br>
将这两个文件移动到/home/sjtu/Desktop/weld_defect/trained_model/inception_v3中即可。

然后删除 计算机/tmp/ 中的相关文件，就可以继续训练mobilenet模型了。

训练mobilenet_1.0_224的方法类似，只需要在终端中输入下面代码即可:

```
python retrain.py --image_dir /home/sjtu/Desktop/weld_defect/retraining_img/retraining_all_2000 --architecture mobilenet_1.0_224
```
之后的操作与训练inception_v3类似。


## 5.预测焊缝缺陷

现在我们已经把模型准备好了，接下去就可以看看预测的效果怎么样了。

你可以一次测试一张图片，也可以一次测试多张图片，将你要测试的图片全部整理到一个文件夹中，文件夹的名字随意(只能包含英文，数字，下划线)，这个文件夹名字之后需要用到。

然后将这个文件夹放在/home/sjtu/Desktop/weld_defect/Test_Img中，这样的话Test_Img文件夹中就会有一个名字为'你取的文件夹名字'的文件夹。

然后，我们打开主程序，也就是图片的接口程序，这个程序放在/home/sjtu/Desktop/weld_defect/predict中，打开Detect_Image_Test.py。

将version变量中的值改为你的文件夹名，也就是说把第20行改为

```
version=['你取的文件夹名字']
```

然后运行Detect_Image_Test.py文件即可，预测时会输出每一张图片的信息。

预测好的图片会被保存在/home/sjtu/Desktop/weld_defect/Final_Img/你取的文件夹名字 中。


此外，你可以一次预测多个文件夹中的图片，比如你有一些'BURNTHROUGH'类型的图片和一些'CRACK'类型的图片需要预测，你可以把它们分别整理到

```
/home/sjtu/Desktop/weld_defect/Test_Img/BURNTHROUGH
/home/sjtu/Desktop/weld_defect/Test_Img/CRACK
```

然后改变Detect_Image_Test.py中的version为：

```
version=['BURNTHROUGH','CRACK']
```

然后运行即可。



## 6.一些有用的工具文件

Tools文件夹中包含了一些有用的工具文件，比如：

* ``Mixed_Up_Img.py``: 这个文件可以将指定文件夹中的图片全部打乱顺序并且重新命名。

* ``Simply_Resize.py``: 这个文件可以将指定文件夹中的所有图片从原始大小转为另外一个大小,比如原来都是128x128像素，可以全部转换为32x32像素。

* ``Simply_Rename.py``: 这个文件可以将指定文件夹中的所有图片重命名，新的名字为编号，顺序从1到n,n为图片数目。

这些文件都比较简单，自己重新写也可以。
