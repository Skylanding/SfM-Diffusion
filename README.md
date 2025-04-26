# SfM-Diffusion
 The implement code for SfM-Diffusion code

 #### Overview

<p align="center">
<img src='imgs/overview.png' width=800/> 
</p>

## ✏️ 📄 Citation

If you find our work useful in your research please consider citing our paper:

```
@article{li2025sfmdiffusion,
  title={SfMDiffusion: self-supervised monocular depth estimation in endoscopy based on diffusion models},
  author={Li, Yu and Chang, Da and Luo, Die and Huang, Jin and Dong, Lan and Wang, Du and Mei, Liye and Lei, Cheng},
  journal={International Journal of Computer Assisted Radiology and Surgery},
  pages={1--9},
  year={2025},
  publisher={Springer}
}
```

## ⚙️ Setup

We ran our experiments with PyTorch 2.0, CUDA 11.8, Python 3.8 and Ubuntu 20.04.



## 🖼️ Prediction for a single image or a folder of images

You can predict scaled disparity for a single image or a folder of images with:

```shell
CUDA_VISIBLE_DEVICES=0 python test_single.py --model_path <your_model_path> --image_path <your_image_or_folder_path>
```



## 💾 Datasets

You can download the [Endovis or SCARED dataset](https://endovissub2019-scared.grand-challenge.org), the [EndoSLAM dataset](https://data.mendeley.com/datasets/cd2rtzm23r/1), the [SERV-CT dataset](https://www.ucl.ac.uk/interventional-surgical-sciences/serv-ct), and the [Hamlyn dataset](http://hamlyn.doc.ic.ac.uk/vision/) and preprocess them before training.


**Endovis split**

The train/test/validation split for Endovis dataset used in our works is defined in the `splits/endovis` folder. 

**Endovis data preprocessing**

We use the ffmpeg format process to convert the RGB.mp4 into images.png:

```shell
find . -name "*.mp4" -print0 | xargs -0 -I {} sh -c 'output_dir=$(dirname "$1"); ffmpeg -i "$1" "$output_dir/%10d.png"' _ {}
```
please note that we only use the left frames in our experiments and please refer to extract_left_frames.py. For dataset 8 and 9, we rephrase keyframes 0-4 as keyframes 1-5.

**Data structure**

The directory of dataset structure is shown as follows:

```
/path/to/endovis_data/
  dataset1/
    keyframe1/
      image_02/
        data/
          0000000001.png
```



## ⏳ Endovis training


**End-to-end:**

```shell
CUDA_VISIBLE_DEVICES=0 python train_end_to_end.py --data_path <your_data_path> --log_dir <path_to_save_model (depth, pose, appearance flow, optical flow)>
```



## 📊 Endovis evaluation

To prepare the ground truth depth maps run:
```shell
CUDA_VISIBLE_DEVICES=0 python export_gt_depth.py --data_path endovis_data --split endovis
```
...assuming that you have placed the endovis dataset in the default location of `./endovis_data/`.

The following example command evaluates the epoch 19 weights of a model named `mono_model`:
```shell
CUDA_VISIBLE_DEVICES=0 python evaluate_depth.py --data_path <your_data_path> --load_weights_folder ~/mono_model/mdp/models/weights_19 --eval_mono
```
