# Hififace for painting

Adapt from [hififace](https://github.com/mindslab-ai/hififace) and [MSG-Net](https://github.com/zhanghang1989/PyTorch-Multi-Style-Transfer)

## Installation
```bash
git clone https://github.com/Peternal/hififace_for_painting.git
cd hififace_for_painting
git clone https://github.com/sicxu/Deep3DFaceRecon_pytorch && git clone https://github.com/NVlabs/nvdiffrast && git clone https://github.com/deepinsight/insightface.git
cp -r insightface/recognition/arcface_torch/ Deep3DFaceRecon_pytorch/models/
cp -r insightface/recognition/arcface_torch/ ./model/
rm -rf insightface
cp -rf 3DMM/* Deep3DFaceRecon_pytorch
mv Deep3DFaceRecon_pytorch model/
rm -rf 3DMM
cd model
wget -O 21styles.model https://www.dropbox.com/s/2iz8orqqubrfrpo/21styles.model?dl=1
cd ..
cd nvdiffrast
pip install .
cd ..
pip install requirements.txt
```
Follow [Deep3DFaceRecon_pytorch](https://github.com/sicxu/Deep3DFaceRecon_pytorch) to construct directory Deep3DFaceRecon_pytorch.\
Download [ms1mv3_arcface_r100_fp16_backbone.pth](https://1drv.ms/u/s!AswpsDO2toNKq0lWY69vN58GR6mw?e=p9Ov5d) and store in hififace_for_painting folder.\
Download [hififace_opensouce_299999.ckpt](https://drive.google.com/file/d/1tZitaNRDaIDK1MPOaQJJn5CivnEIKMnB/view?usp=sharing) and store in hififace_for_painting folder.\
Download our [training_datasets](https://drive.google.com/file/d/1hPqQppICS6t3PF2ftTeRdkJFXxIeE4N9/view?usp=sharing) and extract it to hififace_for_painting folder.


## Hififace Training
You can modify the hyperparaneters in [config/model.yaml](config/model.yaml) and [config/trainer.yaml](config/trainer.yaml).\
Please register [wandb](https://wandb.ai/site) and enter your project and entity name in [config/trainer.yaml](config/trainer.yaml).

### Run
```bash
python hififace_trainer.py --model_config config/model.yaml --train_config config/trainer.yaml -n hififace
```

## Face Swapping to Painting
### Swapping face
Swap the face in `source_image_path` to `target_image_path`.
```bash
python hififace_inference.py --gpus 0 --model_config config/model.yaml --model_checkpoint_path hififace_opensouce_299999.ckpt \
--output_image_path assets/result/swap_result.png \
--source_image_path assets/inference_samples/03_target.png \
--target_image_path assets/AF_dataset/Pablo_Picasso/111.png
```

### Style Transfer
Transfer picture to `--style-imag` style.
```bash
python MSG_main.py eval --content-image assets/inference_samples/03_target.png\ 
--style-image assets/21styles/picasso_selfport1907.jpg --model models/21styles.model\ 
--content-size 256 --output-image assets/transfer_result.png
```
Pre-trained style stored in `assets/21styles` \
More pre-trained (on our dataset) to be added later

