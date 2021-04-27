# ranzcr-clip-catheter-line-classification
ranzcr-clip-catheter-line-classification silver solution

# TLDR
We had a simple approach . here is the basic diagram

![image](https://user-images.githubusercontent.com/22366914/116205078-f7785900-a76f-11eb-81f8-682f29d0b7b3.png)

First of All I would like to thank @ammarali32 and @ttahara for the starting points and notebooks. 
@ttahara your model in CV scored 0.9767 LB 0.972 (which is our best single) and Staged training proposed by @hengck23 and kernel provided by @yasufuminakama and followed by finetuning with multiple datasets

## CV strategy
we all had had almost different CV split but same algorithm. as proposed by @underwearfitting

# Models
We basically used 3 backbones with 4 heads( but only 2 of them were experimented) into submission:

### Backbones
* Resnet200d
* EfficientNetB7
* Resnet50d
### Head
* Multi head Attention
* GeM
* Simple Global Pooling
* AdaptiveConcatPooling

# Our Strategy
We Started with the idea proposed by @hengck23 and @ttahara as starting.

We train the model as 3 stages as @hengck23. When we trained Resnet200d with GeM with soft label(which is created by stage 3 model) on our competition set itself ( we thought it as same as knowledge distillation). and pretrain the model. this model showed us a CV: 0.97 and LB: wasn't tested. We found this soft labelling helps a lot. And we continue to do this on NIH , PadChest , VinBigData external dataset.

* CV: 0.971 / PublicLB: 0.970 / PrivateLB: 0.971 for 3 staged model
* CV: 0.977 / PublicLB: 0.970 / PrivateLB: 0.972 for Multi Head Attention

We first soft labelled only NIH and pretrained and finetuned and then PadChest pretrained and finetuned. We trained every staged model into 5 folds and only 1 fold for Multi Stage. We also used @ammarali32 's public high scoring weights and pretrained and finetuned to give into the ensemble.

# Ensembling
We used simple averaging

# Other Points
## Augmentation
```
RandomResizedCrop(CFG.img_size, CFG.img_size, scale=(0.9, 1), p=1), 
HorizontalFlip(p=0.5), ShiftScaleRotate(p=0.5),
HueSaturationValue(hue_shift_limit=10, sat_shift_limit=10, val_shift_limit=10, p=0.7),
RandomBrightnessContrast(brightness_limit=(-0.2,0.2), contrast_limit=(-0.2, 0.2), p=0.7),
CLAHE(clip_limit=(1,4), p=0.5),
OneOf([ 
OpticalDistortion(distort_limit=1.0), GridDistortion(num_steps=5, distort_limit=1.),
ElasticTransform(alpha=3), ], p=0.2),
OneOf([ GaussNoise(var_limit=[10, 50]),
GaussianBlur(),
MotionBlur(),
MedianBlur(), ], p=0.2),
Resize(CFG.img_size, CFG.img_size), OneOf([ JpegCompression(), Downscale(scale_min=0.1, scale_max=0.15), ], p=0.2), IAAPiecewiseAffine()
```

## Logging: 
Neptune.ai

# Things we didn't had time to do
* Segmentation based learning ( that made us apart from other top candidates)
* Retrieval based learning
* GeM Pooling and AdaptiveConcatPool ( we prepared didnt experimented )

# Things didn't worked
* Multi Staged Ensembling ( because it was hurting too much )
* Dynamic Temperature for pseudo labelling

Thanks to @philippsinger for his writeup where I got how to write a solution writeup (since this is my First writeup)
Thanks to All we learned a lot Team HotWater
