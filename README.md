This is an implementation of YOLOv2 in Keras with Tensorflow backend. 

The two original papers are https://arxiv.org/abs/1506.02640 (YOLOv1) and https://arxiv.org/abs/1612.08242 (YOLOv2).

# Some example applications:

## Raccon detection
<a href="https://www.youtube.com/watch?v=aibuvj2-zxA" rel="some text"><p align="center">![Foo](https://j.gifs.com/E9O21Y.gif)</p></a>

Dataset from shttps://github.com/datitran/raccoon_dataset.

## Self-driving Car
<a href="https://www.youtube.com/watch?v=oYCaILZxEWM" rel="some text"><p align="center">![Foo](https://j.gifs.com/oY3zDB.gif)</p></a>

Trained on VOC2007 and did detection on a random dashcam video.

# Usage for python code
## Data preparation
Download COCO detection data from http://cocodataset.org/#download

+ http://images.cocodataset.org/zips/train2014.zip => training images

+ http://images.cocodataset.org/zips/val2014.zip => validation images

+ http://images.cocodataset.org/annotations/annotations_trainval2014.zip => training and validation annotations
    
Run this script to convert COCO annotation format to VOC format
    https://gist.github.com/chicham/6ed3842d0d2014987186#file-coco2pascal-py

At the end of this process, we should have handles to the 4 folders:

+ train_image_folder <= the folder that contains the train images.

+ train_annot_folder <= the folder that contains the train annotations in VOC format.

+ valid_image_folder <= the folder that contains the validation images.

+ valid_annot_folder <= the folder that contains the validation annotations in VOC format.
    
There is a one-to-one correspondence by file name between images and annotations. For custom dataset, it should be organized in this manner too. Otherwise, the training set will be automatically splitted into training set and validation set using the ratio of 0.8.

## Edit the configuration file
The configuration file is a json file, which looks like this:

```json
{
    "model" : {
        "architecture":         "Tiny Yolo",
        "input_size":           416,
        "anchors":              [0.57273, 0.677385, 1.87446, 2.06253, 3.33843, 5.47434, 7.88282, 3.52778, 9.77052, 9.16828],
        "max_box_per_image":    10,        
        "labels":               ["raccoon"]
    },

    "train": {
        "train_image_folder":   "/home/andy/data/raccoon_dataset/images/",
        "train_annot_folder":   "/home/andy/data/raccoon_dataset/anns/",      
          
        "train_times":          10,
        "pretrained_weights":   "",
        "batch_size":           16,
        "learning_rate":        1e-4,
        "nb_epoch":             50,
        "warmup_batches":       100,

        "object_scale":         5.0 ,
        "no_object_scale":      1.0,
        "coord_scale":          1.0,
        "class_scale":          1.0
    },

    "valid": {
        "valid_image_folder":   "",
        "valid_annot_folder":   "",

        "valid_times":          1
    }
}
```

The model section defines the type of the model to construct as well as other parameters of the model such as the input image size and the list of anchors. Two achitectures are supported at the moment: "Tiny Yolo" and "Full Yolo". 

Download pretrained weights of tiny yolo: https://1drv.ms/u/s!ApLdDEW3ut5fa5Z9jibkqUGG-CA

Download pretrained weights of full yolo: https://1drv.ms/u/s!ApLdDEW3ut5fbAMIhQAO1A26n2A

## Start the training process

`python train.py -c config.json`

By the end of this process, the code will produce the best model. Training stops when the loss on the validation stops being improved in 3 consecutive epoches.

## Perform detection using trained weights on an image by running
`python predict.py -c config.json -w /path/to/best_weights.h5 -i /path/to/image`

It carries out detection on a image and write the image with detected bounding boxes to the same folder.

# Usage jupyter notebook

Refer to the notebook (https://github.com/experiencor/basic-yolo-keras/blob/master/Yolo%20Step-by-Step.ipynb) for a complete step-through implementation of YOLOv2 from scratch (training, testing, and scoring).

# Evaluation of the current implementation:

| Train        | Test          | mAP (with this implementation) | mAP (on released weights) |
| -------------|:--------------|:------------------------:|:-------------------------:|
| COCO train   | COCO val      | 28.6 |    42.1 |
