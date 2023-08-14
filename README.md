# MoFaNeRF:Morphable Facial Neural Radiance Field

[comment]: <> "### [Project Page]&#40;https://yiyuzhuang.github.io/mofanerf/&#41; | [Video]&#40;https://neverstopzyy.github.io/mofanerf/video/supplement_video_7_audio_1.mp4&#41; | [Paper]&#40;https://arxiv.org/abs/2112.02308&#41;  "
[![report](https://img.shields.io/badge/project_page-OK-green)](https://yiyuzhuang.github.io/mofanerf/)
[![report](https://img.shields.io/badge/arXiv_report-OK-green)](https://arxiv.org/abs/2112.02308)
[![report](https://img.shields.io/badge/testing_code-OK-green)](https://github.com/zhuhao-nju/mofanerf)
[![report](https://img.shields.io/badge/training_code-ok-green)](https://github.com/zhuhao-nju/mofanerf)
[![report](https://img.shields.io/badge/dataset-ok-green)](https://facescape.nju.edu.cn/Page_Data)

[comment]: <> "| [Data]&#40;https://zjueducn-my.sharepoint.com/:f:/g/personal/pengsida_zju_edu_cn/Eo9zn4x_xcZKmYHZNjzel7gBdWf_d4m-pISHhPWB-GZBYw?e=Hf4mz7&#41;"

<img src="https://i.imgur.com/PNRhZAN.png" width="1024">

**Any questions or discussions are welcomed!**


Catalog
-----
* [Install](#install)
* [Test our model](#test-our-model)
  * [Fitting](#fitting)
    + [1. Prepare your data first](#1-prepare-your-data-first)
    + [2. Fit to the processed image](#2-fit-to-the-processed-image)
    + [3. Render images of novel views](#3-render-images-of-novel-views)
    + [4. Refine the rendered results](#4-refine-the-rendered-results)
* [Train your own model]()

Install
------
* **Environment configuration**
```
pip install -r requirements.txt # tested on Python 3.7
```
<box style="text-indent:1.0em; font-size: 13px">
<details>
  <summary> Dependencies (click to expand) </summary>

  - PyTorch 1.9.0
  - dlib 19.22.1
  - matplotlib
  - numpy
  - imageio
  - imageio-ffmpeg
</details>
</box>
<br>

* **Pre-trained models**

Download the pretrained models:
```
./download_pretrained_models.sh
```

Test our model
------
- ### Fitting
<img src="https://neverstopzyy.github.io/mofanerf/images/fig_gen_fit.png" width=1024>

#### 1. Prepare your data first
We preprocess wild images using the following three steps: alignment, segmentation and relighting,
and we pack these steps into `wildData_preprocess.py`. After the preprocessing,
one aligned image and one estimated camera pose you will get.

This demo show the way to batch processing of the wild images.
```
 python ./tools/wildData_preprocess.py --filePath ./data/fit/
```
#### 2. Fit to the processed image
This demo show how to fit our model to target image.
```
python run_fit.py --filePath ./data/fit/segRelRes/1.png
```
* Reminder: If your GPU has memory of only 10GB, we recommend that you modify parameters "netchunk","chunk" from 196608 to 16384, "N_rand" from 1024 to 64 in `./configs/exp_mofanerf.txt`.
#### 3. Render images of novel views
This demo show how to render novel views with the fitted parameters, and just simply add one parameter "--renderType rendering".
```
python run_fit.py --filePath ./data/fit/segRelRes/1.png --renderType rendering  
```
#### 4. Refine the rendered results
This demo show how to use our refinement module to enrich details of the rendered results.

The refined results will be saved in folder `refine`  in the same parent directory as the target folder.
```
python run_refine.py --name facescape --nerf_folder ./data/fit/fitting/segRelRes_1/render/
```
- ### Face Rigging and Editing
#### 1. Render modulation results
This demo simply show how to rig and edit the fitting results.
```
python run_fit.py --filePath ./data/fit/segRelRes/1.png --renderType rendering_modulation
```
#### 2. Refine the rendered results

```
python run_refine.py --name facescape --nerf_folder ./data/fit/fitting/segRelRes_1/render/
```

Train your own model
------

#### 1. Train the coarse part of MoFaNeRF 

```
 python run_train.py --config ./configs/exp_mofanerf.txt
 ```

#### 2. There are three steps to train our refineNet:

2.1. Render the training data with MoFaNeRF:

```
python render_refine_trainSet.py  --config ./configs/exp_mofanerf.txt
```

2.2. Prepare paired ground truth images of rendered results:

Try to modify the folder paths in  *prepare_refineNet_trainSet.py* to achieve it.

2.3. Run the training code for refineNet:
```
python run_train_refineNet.py --name facescape --dataroot *dataPath* --nerf_folder train --gt_folder GT
```

**Tips**: It takes a long time to run *render_refine_trainSet.py*, so you can try to distribute the task across several machines. A simple way to do this is to split the identity list with the parameters "begin_person" and "end_person", thus enabling multiple identities to be rendered at the same time.

Bibtex
---
If you find this project helpful to your research, please consider citing:

```
@InProceedings{zhuang2022mofanerf,
  title={MoFaNeRF: Morphable Facial Neural Radiance Field},
  author={Zhuang, Yiyu and Zhu, Hao and Sun, Xusen and Cao, Xun},
  booktitle={European Conference on Computer Vision},
  year={2022}
}
```
