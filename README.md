<!-- main documents -->


<div align="center">


# UniAnimate: Taming Unified Video Diffusion Models for Consistent Human Image Animation

[Xiang Wang](https://scholar.google.com.hk/citations?user=cQbXvkcAAAAJ&hl=zh-CN&oi=sra)<sup>1</sup>, [Shiwei Zhang](https://scholar.google.com.hk/citations?user=ZO3OQ-8AAAAJ&hl=zh-CN)<sup>2</sup>, [CHangxin Gao](https://scholar.google.com.hk/citations?user=4tku-lwAAAAJ&hl=zh-CN)<sup>1</sup>, [Jiayu Wang](#)<sup>2</sup>, [Xiaoqiang Zhou](https://scholar.google.com.hk/citations?user=Z2BTkNIAAAAJ&hl=zh-CN&oi=ao)<sup>3</sup>, [Yingya Zhang](https://scholar.google.com.hk/citations?user=16RDSEUAAAAJ&hl=zh-CN)<sup>2</sup> , [Luxin Yan](#)<sup>1</sup> , [Nong Sang](https://scholar.google.com.hk/citations?user=ky_ZowEAAAAJ&hl=zh-CN)<sup>1</sup>  
<sup>1</sup>HUST &nbsp; <sup>2</sup>Alibaba Group &nbsp; <sup>3</sup>USTC


[🎨 Project Page](https://unianimate.github.io/)


<p align="middle">
  <img src='https://img.alicdn.com/imgextra/i4/O1CN01bW2Y491JkHAUK4W0i_!!6000000001066-2-tps-2352-1460.png' width='784'>

  Demo cases generated by the proposed UniAnimate
</p>


</div>

## 🔥 News 
- **[2024/06/13]** **🔥🔥🔥 <font color=red>We released the code and models for human image animation, enjoy it!</font>** 
- **[2024/06/13]** We have submitted the code to the company for approval, and **the code is expected to be released today or tomorrow**.
- **[2024/06/03]** We initialized this github repository and planed to release the paper. 


## TODO

- [x] Release the models and inference code, and pose alignment code.
- [x] Support generating both short and long videos.
- [ ] Release the models for longer video generation in one batch.
- [ ] Release models based on VideoLCM for faster video synthesis.
- [ ] Training the models on higher resolution videos.



## Introduction

<div align="center">
<p align="middle">
  <img src='https://img.alicdn.com/imgextra/i3/O1CN01VvncFJ1ueRudiMOZu_!!6000000006062-2-tps-2654-1042.png' width='784'>

  Overall framework of UniAnimate
</p>
</div>

Recent diffusion-based human image animation techniques have demonstrated impressive success in synthesizing videos that faithfully follow a given reference identity and a sequence of desired movement poses. Despite this, there are still two limitations: i) an extra reference model is required to align the identity image with the main video branch, which significantly increases the optimization burden and model parameters; ii) the generated video is usually short in time (e.g., 24 frames), hampering practical applications. To address these shortcomings, we present a UniAnimate framework to enable efficient and long-term human video generation. First, to reduce the optimization difficulty and ensure temporal coherence, we map the reference image along with the posture guidance and noise video into a common feature space by incorporating a unified video diffusion model. Second, we propose a unified noise input that supports random noised input as well as first frame conditioned input, which enhances the ability to generate long-term video. Finally, to further efficiently handle long sequences, we explore an alternative temporal modeling architecture based on state space model to replace the original computation-consuming temporal Transformer. Extensive experimental results indicate that UniAnimate achieves superior synthesis results over existing state-of-the-art counterparts in both quantitative and qualitative evaluations. Notably, UniAnimate can even generate highly consistent one-minute videos by iteratively employing the first frame conditioning strategy.


## Getting Started with UniAnimate


### (1) Installation

Installation the python dependencies:

```
git clone https://github.com/ali-vilab/UniAnimate.git
cd UniAnimate
conda create -n UniAnimate python=3.9
conda activate UniAnimate
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```
We also provide all the dependencies in `environment.yaml`

### (2) Download the pretrained checkpoints

Download models:
```
!pip install modelscope
from modelscope.hub.snapshot_download import snapshot_download
model_dir = snapshot_download('iic/unianimate', cache_dir='checkpoints/')
```
Then you might need the following command to move the checkpoints to the "checkpoints/" directory:
```
mv ./checkpoints/iic/unianimate/* ./checkpoints/
```

Finally, the model weights will be organized in `./checkpoints/` as follows:
```
./checkpoints/
|---- dw-ll_ucoco_384.onnx
|---- open_clip_pytorch_model.bin
|---- unianimate_16f_32f_non_ema_223000.pth 
|---- v2-1_512-ema-pruned.ckpt
└---- yolox_l.onnx
```

### (3) Pose alignment **(Important)**

Rescale the target pose sequence to match the pose of the reference image:
```
# reference image 1
python run_align_pose.py  --ref_name data/images/WOMEN-Blouses_Shirts-id_00004955-01_4_full.jpg --source_video_paths data/videos/source_video.mp4 --saved_pose_dir data/saved_pose/WOMEN-Blouses_Shirts-id_00004955-01_4_full 

# reference image 2
python run_align_pose.py  --ref_name data/images/musk.jpg --source_video_paths data/videos/source_video.mp4 --saved_pose_dir data/saved_pose/musk 

# reference image 3
python run_align_pose.py  --ref_name data/images/WOMEN-Blouses_Shirts-id_00005125-03_4_full.jpg --source_video_paths data/videos/source_video.mp4 --saved_pose_dir data/saved_pose/WOMEN-Blouses_Shirts-id_00005125-03_4_full

# reference image 4
python run_align_pose.py  --ref_name data/images/IMG_20240514_104337.jpg --source_video_paths data/videos/source_video.mp4 --saved_pose_dir data/saved_pose/IMG_20240514_104337
```
We have already provided the processed target pose for demo videos in ```data/saved_pose```, if you run our demo video example, this step can be skipped.

**<font color=red>&#10004; Some tips</font>**:

- > In pose alignment, the first frame in the target pose sequence is used to calculate the scale coefficient of the alignment. Therefore, if the first frame in the target pose sequence contains the entire face and pose (hand and foot), it can help obtain more accurate estimation and better video generation results.



### (4) Run the UniAnimate model to generate videos

#### (4.1) Generating video clips (32 frames with 768x512 resolution)

Execute the following command to generate video clips:
```
python inference.py --cfg configs/UniAnimate_infer.yaml 
```
After this, 32-frame video clips with 768x512 resolution will be generated:


<table>
<center>
  <tr>
    <td ><center>
      <image  height="260" src="assets/1.gif"></image>	
    </center></td>
    <td ><center>
      <image height="260" src="assets/2.gif"></image>	
    </center></td>
  </tr>
  <tr>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYV1hTb2g3Zlpmb1E/Vk9HZHZkdDBUQzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYYzNUUWRKR043c1FaZkVHSkpSMnpoeTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
  </tr>
</center>
</table>
</center>

<!-- <table>
<center>
<tr>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYYzNUUWRKR043c1FaZkVHSkpSMnpoeTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYV1hTb2g3Zlpmb1E/Vk9HZHZkdDBUQzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
</tr>
</table> -->


**<font color=red>&#10004; Some tips</font>**:

- > To run the model, ~26G GPU memory will be used. If your GPU is smaller than this, you can change the  `max_frames: 32` in `configs/UniAnimate_infer.yaml` to other values, e.g., 24, 16, and 8. Our model is compatible with all of them.


#### (4.2) Generating video clips (32 frames with 1216x768 resolution)

If you want to synthesize higher resolution results, you can change the `resolution: [512, 768]` in `configs/UniAnimate_infer.yaml` to `resolution: [768, 1216]`. And execute the following command to generate video clips:
```
python inference.py --cfg configs/UniAnimate_infer.yaml 
```
After this, 32-frame video clips with 1216x768 resolution will be generated:



<table>
<center>
  <tr>
    <td ><center>
      <image  height="260" src="assets/3.gif"></image>	
    </center></td>
    <td ><center>
      <image height="260" src="assets/4.gif"></image>	
    </center></td>
  </tr>
  <tr>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/NTFJUWJ1YXphUzU5b3dhZHJlQk1YZjA3emppMWNJbHhXSlN6WmZHc2FTYTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYYklMcGdIRFlDcXcwVEU5ZnR0VlBpRzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
  </tr>
</center>
</table>
</center>

<!-- <table>
<center>
<tr>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/NTFJUWJ1YXphUzU5b3dhZHJlQk1YZjA3emppMWNJbHhXSlN6WmZHc2FTYTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYYklMcGdIRFlDcXcwVEU5ZnR0VlBpRzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
</tr>
</table> -->


**<font color=red>&#10004; Some tips</font>**:

- > To run the model, ~36G GPU memory will be used.  Even though our model was trained on 512x768 resolution, we observed that direct inference on 768x1216 is usually allowed and produces satisfactory results. If this results in inconsistent apparence, you can try a different seed or adjust the resolution to 512x768.

- > Although our model was not trained on 48 or 64 frames, we found that the model generalizes well to synthesis of these lengths.



In the `configs/UniAnimate_infer.yaml` configuration file, you can specify the data, adjust the video length using `max_frames`, and validate your ideas with different Diffusion settings, and so on.



#### (4.3) Generating long videos



If you want to synthesize videos as long as the target pose sequence, you can execute the following command to generate long videos:
```
python inference.py --cfg configs/UniAnimate_infer_long.yaml
```
After this, long videos with 1216x768 resolution will be generated:



<table>
<center>
  <tr>
    <td ><center>
      <image  height="260" src="assets/5.gif"></image>	
    </center></td>
    <td ><center>
      <image height="260" src="assets/6.gif"></image>	
    </center></td>
  </tr>
  <tr>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYVmJKZUJSbDl6N1FXU01DYTlDRmJKTzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/VUdZTUE5MWtST3VtNEdFaVpGbHN1U25nNEorTEc2SzZROUNiUjNncW5ycTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
  </tr>
</center>
</table>
</center>

<!-- <table>
<center>
<tr>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYVmJKZUJSbDl6N1FXU01DYTlDRmJKTzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/VUdZTUE5MWtST3VtNEdFaVpGbHN1U25nNEorTEc2SzZROUNiUjNncW5ycTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
</tr>
</table> -->




<table>
<center>
  <tr>
    <td ><center>
      <image  height="260" src="assets/7.gif"></image>	
    </center></td>
    <td ><center>
      <image height="260" src="assets/8.gif"></image>	
    </center></td>
  </tr>
  <tr>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYV04xKzd3eWFPVGZCQjVTUWdtbTFuQzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
    <td ><center>
      <p>Click <a href="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYWGwxVkNMY1NXOHpWTVdNZDRxKzRuZTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ">HERE</a> to view the generated video.</p>
    </center></td>
  </tr>
</center>
</table>
</center>

<!-- <table>
<center>
<tr>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYV04xKzd3eWFPVGZCQjVTUWdtbTFuQzZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
    <td ><center>
        <video height="260" controls autoplay loop src="https://cloud.video.taobao.com/vod/play/cEdJVkF4TXRTOTd2bTQ4andjMENYWGwxVkNMY1NXOHpWTVdNZDRxKzRuZTZQZWw1SnpKVVVCTlh4OVFON0V5UUVMUDduY1RJak82VE1sdXdHTjNOaHc9PQ" muted="false"></video>
    </td>
</tr>
</table> -->

In the `configs/UniAnimate_infer_long.yaml` configuration file, `test_list_path` should in the format of `[frame_interval, reference image, driving pose sequence]`, where `frame_interval=1` means that all frames in the target pose sequence will be used to generate the video, and `frame_interval=2` means that one frame is sampled every two frames. `reference image` is the location where the reference image is saved, and `driving pose sequence` is the location where the driving pose sequence is saved.



**<font color=red>&#10004; Some tips</font>**:

- > If you find inconsistent appearance, you can change the resolution from 768x1216 to 512x768, or change the `context_overlap` from 8 to 16.
- > In the default setting of `configs/UniAnimate_infer_long.yaml`, the strategy of sliding window with temporal overlap is used. You can also generate a satisfactory video segment first, and then input the last frame of this segment into the model to generate the next segment to continue the video.



## Citation

If you find this codebase useful for your research, please cite the following paper:

```
@article{wang2024unianimate,
      title={UniAnimate: Taming Unified Video Diffusion Models for Consistent Human Image Animation},
      author={Wang, Xiang and Zhang, Shiwei and Gao, Changxin and Wang, Jiayu and Zhou, Xiaoqiang and Zhang, Yingya and Yan, Luxin and Sang, Nong},
      journal={arXiv preprint arXiv:2406.01188},
      year={2024}
}
```


## Disclaimer


This open-source model is intended for <strong>RESEARCH/NON-COMMERCIAL USE ONLY</strong>. 
We explicitly disclaim any responsibility for user-generated content. Users are solely liable for their actions while using the generative model. The project contributors have no legal affiliation with, nor accountability for, users' behaviors. It is imperative to use the generative model responsibly, adhering to both ethical and legal standards.