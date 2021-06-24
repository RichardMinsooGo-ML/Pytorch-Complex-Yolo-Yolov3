# Pytorch Complex Yolo - Yolov3


Complete but Unofficial PyTorch Implementation of [Complex-YOLO: Real-time 3D Object Detection on Point Clouds](https://arxiv.org/pdf/1803.06199.pdf) with YoloV3
Code was tested with following specs:
- Code was tested on Windows 10

## 1. Installation
First, clone or download this GitHub repository.
Install requirements and download from official darknet weights:
```
# yolov3
wget -P model_data https://pjreddie.com/media/files/yolov3.weights

# yolov3-tiny
wget -P model_data https://pjreddie.com/media/files/yolov3-tiny.weights

# yolov4
wget -P model_data https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v3_optimal/yolov4.weights

# yolov4-tiny
wget -P model_data https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v4_pre/yolov4-tiny.weights
```

Or you can download darknet weights from my google drive:

https://drive.google.com/drive/folders/1w4KNO2jIlkyzQgUkkcZ18zrC8A1Nqvwa?usp=sharing

## 2. Pretrained weights

You can download darknet weights from my google drive:

- [x] Complex Yolo v3:
https://drive.google.com/file/d/1jpaDXJcBLf4s3JBYrmapkTQ9OCeGmoQI/view?usp=sharing

- [x] Complex Yolo v3 - tiny:
https://drive.google.com/file/d/1Mukg7aZ3C5ZWFhkS5r1wmL1tgWXsTxS6/view?usp=sharing


## 3. Quick start

### 3.1. Download pretrained weights 

### 3.2. Dataset shall contains `detect_1` folders and contents, `sampledata` *.txt files.

```
${ROOT}
├── dataset/
│    ├── classes.names
│    └── kitti/
│          ├── classes_names.txt
│          ├── detect_1/    <-- for detection test
│          │    ├── calib/
│          │    ├── image_2/
│          │    └── velodyne/
│          ├── ImageSets/ 
│          │    ├── detect_1.txt
│          │    ├── detect_2.txt
│          │    ├── sample.txt
│          ├── sampledata/ 
│          │    ├── image_2/
│          │    ├── calib/
│          │    ├── label_2/
│          │    └── velodyne/

```


### Test [without downloading dataset] 

       $ python test_detection.py --split=sample --folder=sampledata  
       $ python test_both_side_detection.py --split=sample --folder=sampledata

### Demo Video 

![Detection_one_side](./asset/Detection_one_side.gif)


![detection_both_side](./asset/detection_both_side.gif)



## Data Preparation from KITTI

You can see `sampledata` folder in `dataset/kitti/sampledata` directory which can be used for testing this project without downloading KITTI dataset. However, if you want to train the model by yourself and check the mAP in validation set just follow the steps below.

#### Download the [3D KITTI detection dataset](http://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d) 
1. Camera calibration matrices of object data set (16 MB)
2. Training labels of object data set (5 MB)
3. Velodyne point clouds (29 GB)
4. Left color images of object data set (12 GB)

Now you have to manage dataset directory structure. Place your dataset into `data` folder. Please make sure that you have the dataset directory structure as follows. 


The `train/valid` split of training dataset as well as `sample` and `test` dataset ids are in `data/kitti/ImageSets` directory. From training set of 7481 images, 6000 images are used for training and remaining 1481 images are used for validation. The mAP results reported in this project are evaluated into this valid set with custom mAP evaluation script with 0.5 iou for each object class. 

#### Verify your download
    $ python check_dataset.py

## Train
    $ train.py [-h] [--epochs EPOCHS] [--batch_size BATCH_SIZE]
                [--gradient_accumulations GRADIENT_ACCUMULATIONS]
                [--model_def MODEL_DEF] 
                [--pretrained_weights PRETRAINED_WEIGHTS] 
                [--n_cpu N_CPU] [--img_size IMG_SIZE]
                [--evaluation_interval EVALUATION_INTERVAL]
                [--multiscale_training MULTISCALE_TRAINING]

--Training log example--

    ---- [Epoch 0/300, Batch 250/1441] ----  
    +------------+--------------+--------------+--------------+  
    | Metrics    | YOLO Layer 0 | YOLO Layer 1 | YOLO Layer 2 |  
    +------------+--------------+--------------+--------------+  
    | grid_size  | 17           | 34           | 68           |  
    | loss       | 6.952686     | 5.046788     | 4.256296     |  
    | x          | 0.054503     | 0.047048     | 0.060234     |  
    | y          | 0.110871     | 0.059848     | 0.081368     |
    | w          | 0.101059     | 0.056696     | 0.022349     |
    | h          | 0.294365     | 0.230845     | 0.076873     |
    | im         | 0.215230     | 0.218564     | 0.184226     |
    | re         | 1.049812     | 0.883522     | 0.783887     |
    | conf       | 4.682138     | 3.265709     | 2.941420     |
    | cls        | 0.444707     | 0.284557     | 0.105938     |
    | cls_acc    | 67.74%       | 83.87%       | 96.77%       |
    | recall50   | 0.000000     | 0.129032     | 0.322581     |
    | recall75   | 0.000000     | 0.032258     | 0.032258     |
    | precision  | 0.000000     | 0.285714     | 0.133333     |
    | conf_obj   | 0.058708     | 0.248192     | 0.347815     |
    | conf_noobj | 0.014188     | 0.007680     | 0.010709     |
    +------------+--------------+--------------+--------------+
    Total loss 16.255769729614258
    ---- ETA 0:18:27.490254

## Test
    $ python test_detection.py
    $ python test_both_side_detection.py

## Evaluation
    $ python eval_mAP.py 

mAP (min. 50 IoU)

| Model/Class             | Car     | Pedestrian | Cyclist | Average |
| ----------------------- |:--------|:-----------|:--------|:--------|
| Complex-YOLO-v3         | 97.89   |82.71       |90.12    |90.24    |
| Complex-Tiny-YOLO-v3    | 95.91   |49.29       |78.75    |74.65    |

#### Results 
<p align="center"><img src="assets/result1.jpg" width="1246"\></p>
<p align="center"><img src="assets/result2.jpg" width="1246"\></p>
<p align="center"><img src="assets/result3.jpg" width="1246"\></p>

## Credit

1. Complex-YOLO: https://arxiv.org/pdf/1803.06199.pdf

YoloV3 Implementation is borrowed from:
1. https://github.com/eriklindernoren/PyTorch-YOLOv3

Point Cloud Preprocessing is based on:  
1. https://github.com/skyhehe123/VoxelNet-pytorch
2. https://github.com/dongwoohhh/MV3D-Pytorch


## Folder structure

```
${ROOT}
├── detection.py
├── detection_both_side.py
├── eval_mAP.py
├── README.md 
├── train.py
├── checkpoints/ 
│    ├── Complex_yolo_yolo_v3.pth
│    ├── Complex_yolo_yolo_v3_tiny.pth
│    ├── yolov3.weights
│    └── yolov3-tiny.weights
├── config/ 
│    ├── complex_yolov3.cfg
│    ├── complex_yolov3_tiny.cfg
│    ├── complex_yolov4.cfg
│    ├── complex_yolov4_tiny.cfg
│    └── train_config.py
├── data_process/ 
│    ├── config.py
│    ├── kitti_aug_utils.py
│    ├── kitti_bev_utils.py
│    ├── kitti_dataset.py
│    ├── kitti_utils.py
│    └── kitti_yolo_dataset.py
├── dataset/
│    ├── classes.names
│    └── kitti/
│          ├── classes_names.txt
│          ├── detect_1/    <-- for detection test
│          │    ├── calib/
│          │    ├── image_2/
│          │    └── velodyne/
│          ├── detect_2/    <-- for detection test
│          │    ├── calib/
│          │    ├── image_2/
│          │    └── velodyne/
│          ├── ImageSets/ 
│          │    ├── detect_1.txt
│          │    ├── detect_2.txt
│          │    ├── sample.txt
│          │    ├── test.txt
│          │    ├── train.txt
│          │    ├── val.txt
│          │    └── valid.txt
│          ├── sampledata/ 
│          │    ├── image_2/
│          │    ├── calib/
│          │    ├── label_2/
│          │    └── velodyne/
│          ├── training/    <-- 7481 train data
│          │    ├── image_2/  <-- for visualization
│          │    ├── calib/
│          │    ├── label_2/
│          │    └── velodyne/
│          └── testing/     <-- 7580 test data
│                ├── image_2/  <-- for visualization
│                ├── calib/
│                └── velodyne/
├── logs/ 
├── models/ 
│    ├── models.py
│    └── yolo_layer.py
└── utils/ 
      ├── mayavi_viewer.py
      ├── train_utils.py
      └── utils.py

```
