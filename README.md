# GaitDet
本项目进行行人检测和人形二值分割，为下一步的步态识别提供高质量的人形特征进行行人再识别

## 运行环境
OS:Ubuntu16.04 
GPU:Titan X 
CUDA:9.0 
cudnn:7.0 
TensorRT:5.0.2.6
## 任务介绍
| 任务 | 算法 | 数据集 | 输入图片规格 | 测试效果 | 速度 |
| ------ | ------ | ------ | ------ | ------ | ------ |
| 行人检测 | yolov3 | caltech, pascal voc | 416x416 RGB | mAP:71.43% | 62fps |
| 人形分割 | Deeplabv3+ | cihp | 512x512 RGB | mIoU:91.14% | 24fps |
## 算法提速
1. 使用CUDA npp库对图像进行缩放、归一化、通道转换等处理
2. 利用cuda编程，在GPU上将Deeplabv3+输出的probablity map映射为RGB分割图
3. 使用多线程以及信号量机制实现同步，使各个任务在各个线程中进行    

|  | yolov3 | Deeplabv3+ | 总时间 |
| ------ | ------ | ------ | ------ |
| 单线程 | 16ms | 43ms | 64ms |
| 三线程 | 25ms | 55ms | 59ms |
## GIE模型文件下载地址
链接：https://pan.baidu.com/s/1kgQaINyk9UZ5CI3CjzZY-A  提取码：6wrd
## 运行步骤
$ git clone https://github.com/SteveSZF/GaitDet.git  
$ cd GaitDet  
将下载的两个GIE模型文件分别放到deeplab和yolov3文件中  
$ cd deeplab  
$ python generate_onnx.py --model ./deeplabv3_plus.pth.tar  
$ ../onnx2tensorrt/onnx2tensorrt.bin deeplab.onnx deeplab.trt  
$ cd ../yolov3  
$ python yolov3_to_onnx.py --weight yolov3_person_det.weight --cfg yolov3-custom-deploy.cfg --onnx ./yolov3.onnx  
$ ../onnx2tensorrt/onnx2tensorrt.bin yolov3.onnx yolov3.trt  
$ ./gait.bin /home/Videos/test.mp4 ../deeplabv3_plus/deeplab.trt  ../new_yolov3_v2/yolov3.trt  
## Todo
1. 实现行人跟踪
2. 实现人体姿态估计
