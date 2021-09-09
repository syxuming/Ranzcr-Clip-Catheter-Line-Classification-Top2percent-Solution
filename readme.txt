5 STAGE 步骤

以下参数5个stage统一
optimizer="Adam"
scheduler="CosineAnnealingLR"

STAGE1:  
对有导管标注的图片进行高亮标注，并且训练，这一步训练到准确率为1或者十分接近1，得到模型1。
loss : BCE 
epoch：6
img_size:640
lr=1e-4

STAGE2:  
将stage1的输出模型作为teacher模型，得到模型2。
自定义loss
1.由teacher对有颜色标注导管的图片进行features指导。 
2.用BCE对全部图片和groudtruth训练。
epoch：14
img_size:640
lr=1e-3

STAGE3:
将stage2的输出模型作为pretrain起点，在官方训练数据集上进行finetune，得到模型3.
生成5fold的5个模型。
loss : BCE
epoch：18
img_size:640
lr=1e-4


STAGE4:
首先通过5fold的5个模型，对外部数据集进行softlabel，生成csv文件。
将stage3的输出模型作为pretrain起点，在外部数据集（约13万张图片）上训练，标签就是生成的softlabel.csv，得到模型4.
loss : BCE
epoch：22
img_size:640
lr=1e-4

STAGE5:
将stage4的输出模型作为pretrain起点, 并且扩大图片尺寸到720，在官方训练数据集上进行finetune，得到模型5.
loss : BCE
epoch：5
img_size:720
lr=5e-6
