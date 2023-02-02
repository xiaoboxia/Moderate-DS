# Moderate Coreset: A Universal Method of Data Selection For Real-world Data-efficient Deep Learning

This repo contains code for selecting Coreset and training on selected coreset using the methods provided by our paper Moderate Coreset: A Universal Method of Data Selection For Real-world Data-efficient Deep Learning (ICLR 2023). 



## Getting started

### Download CIFAR-100

Download [CIFAR-100](https://www.cs.toronto.edu/~kriz/cifar-100-python.tar.gz) to `data` and unzip.

### Download Tiny ImageNet

Download [Tiny ImageNet](http://cs231n.stanford.edu/tiny-imagenet-200.zip) to `data` and unzip.

Run `convert-tiny.sh` to re-organize validation set. Script should take ~2 min to complete.



## Experiment on Ideal Datasets

**Train a base model on original dataset.**

On CIFAR-100:

```
python train.py --dataset CIFAR100 --data data 
```

On Tiny ImageNet:

```
python train.py --dataset tiny --data data/tiny-imagenet-200 --epoch 90 --batch_size 256
```

By default, this will save model checkpoint to `ckpt/${dataset}/resnet50/`.

**Get image index to be pruned.**

On CIFAR-100:

```
python selection.py --dataset CIFAR100 --ckpt ckpt/CIFAR100/resnet50/best.pth --rate 0.2
```

On Tiny ImageNet:

```
python selection.py --dataset tiny --data data/tiny-imagenet-200 --ckpt ckpt/tiny/resnet50/best.pth --rate 0.2 
```

Change the `rate` argument to the selection ratio you desire.  By default, ids will be saved to `index/${dataset}.bin`

**Train on selected coreset.**

On CIFAR-100:

```
python train.py --dataset CIFAR100 --data data --prune index/CIFAR100.bin
```

On Tiny ImageNet:

```
python train.py --dataset tiny --data data/tiny-imagenet-200 --prune index/tiny.bin --batch_size 256  --epoch 90
```



## Experiment on Complex Scenes

The code used for perturbing datasets is stored in `robust` folder. Paths in the scripts are hard coded, please do not change the path configs.

```bash
cd robust
```

#### Corrupt CIFAR-100

The script will corrupt part of the training image and save corrupted trainset into a pickle file. 

Per-corrupt ratio can be altered in Line 19. You can alter the directory to save corrupted data in Line 16, but it is not recommended.

```bash
python corrupt-cifar.py
```

#### Corrupt Tiny ImageNet

The script will corrupt images **in-place**. Per-corrupt ratio can be altered in Line 18.

```bash
cp -r ../data/tiny-imagenet-200 ../data/tiny-imagenet-200-corrupt
python corrupt-tiny.py
```

#### Attack CIFAR-100

The script will perform either PGD attack or GSA attack on CIFAR-100.  You can change attack types in Line 18. 

```
python attack-cifar.py
```

#### Attack Tiny ImageNet

The script will perform either PGD attack or GSA attack on Tiny ImageNet. You can change attack types in Line 18. 

```
python attack-tiny.py
```

#### Add Label Noise to CIFAR-100

The script will add random noise to a part of the trainset. You can change mislabeled data ratio at Line 7. Labels are saved at `data/noisy/cifar.npy`

```bash
python noisy-cifar.py
```

#### Add Label Noise to Tiny ImageNet

Mislabeled data ratio can be changed at Line 9.

Images are saved at `data/noisy/tiny_img.npy`，labels are saved at `data/noisy/tiny_target.npy`

```
python noisy-tiny.py
```

#### Data Selection on Perturbed Dataset

Performing data selection on perturbed dataset is almost the same as performing on ideal dataset. Simply add one of `C` `A` `N` to dataset name, where `C` is for corrupted, `A` is for attacked, `N` is for noisy label. Here are some examples for training on perturbed dataset.

```
# Train on corrupted CIFAR-100
python train.py --dataset CIFAR100C --data data 

# Train on attacked CIFAR-100
python train.py --dataset CIFAR100A --data data 

# Train on mislabeled CIFAR-100
python train.py --dataset CIFAR100N --data data 

# Train on corrupted Tiny ImageNet
python train.py --dataset tinyC --data data/tiny-imagenet-200-corrupt --epoch 90 --batch_size 256

# Train on attacked Tiny ImageNet
python train.py --dataset tinyA --data data/tiny-imagenet-200 --epoch 90 --batch_size 256

# Train on mislabeled Tiny ImageNet
python train.py --dataset tinyN --data data/tiny-imagenet-200 --epoch 90 --batch_size 256
```



## Reference

If you find our code useful for your research, please cite our paper.

```
@inproceedings{xia2023moderate
  title={Moderate Coreset: A Universal Method of Data Selection for Real-world Data-efficient Deep Learning},
  author={Xia, Xiaobo and Liu, Jiale and Yu, Jun and Shen, Xu and Han, Bo and Liu, Tongliang},
  booktitle={ICLR},
  year={2023}
}
```
