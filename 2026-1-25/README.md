# 圖片角度轉換

## 參考 

https://github.com/jtydhr88/ComfyUI-qwenmultiangle

## 安裝與使用

```bash
# 新增專用節點
cd ComfyUI/custom_nodes
git clone https://github.com/jtydhr88/ComfyUI-qwenmultiangle.git
然後重啟comfyUI
```
```bash
# 新增工作流
下載 https://huggingface.co/fal/Qwen-Image-Edit-2511-Multiple-Angles-LoRA/tree/main 裡的 comfyui-workflow-multiple-angles.json
放到 ComfyUI\user\default\workflows
```
```bash
# 適配模型下載
在載入工作流之後，由comfyui自動下載需要的模型，不要自己去網上找，大概率會是不匹配的
```
```bash
# 加入視覺化角度命令節點，
add node->image->multiangle
將IMAGE接入節點的input，將節點的output接上正向命令的prompt
```

## 文件結構
```
📂 ComfyUI/
├─── 📂 models/
│     └── 📂 diffusion_models/
│     │    └── qwen_image_edit_2511_bf16.safetensors
│     └── 📂 loras/
│     │    └── qwen-image-edit-2511-multiple-angles-lora.safetensors
│     │    └── Qwen-Image-Edit-2511-Lightning-4steps-V1.0-bf16.safetensors
│     └── 📂 vae/
│     │    └── qwen_image_vae.safetensors
│     └── 📂 text_encoders/
│          └── qwen_2.5_vl_7b_fp8_scaled.safetensors
```

## 結論

角度變換靈活，但諸如紋理、解頗學，手指等等的還是會偶爾出現一些怪異如果照片的主體不清晰或者某些手、四肢被遮擋

## 備註

1. 切記不要從網路上下載模型，因為有許多版本或者相似名稱的模型(例如2509和2511是不同的，也有分bf16和fp8)，先下載工作流後，由ComfyUI自動偵測缺少哪些模型並下載就行，而不是先準備模型

2. 這個lora是由qwen在huggingface上發表的，他和傳統的stable diffusion不同，它是基於qwen-image-edit-2511這個模型，所以無稐是lora或者diffusion-model接無法在invokeAI這種幾乎只支持stable-diffusion的平台上使用





# 圖片生成3D模型

## 參考:
```
https://docs.comfy.org/tutorials/3d/hunyuan3D-2
```
## workflow:
```
3d_hunyuan3d_multiview_to_model.json<br>
來自comfyUI內建開源workflow
```
## checkpoint:
```
hunyuan3d-dit-v2-mv_fp16.safetensors<br>
自動補齊、或手動下載 https://huggingface.co/Comfy-Org/hunyuan3D_2.0_repackaged/resolve/main/split_files/hunyuan3d-dit-v2-mv_fp16.safetensors
```
## 文件結構
```
📂 ComfyUI/
├── 📂 models/
│   └── 📂 checkpoints/
│          └── hunyuan3d-dit-v2-mv_fp16.safetensors
```

# 圖像生成影片 (短)

## 參考: 
```
https://www.youtube.com/watch?v=tHVcjUNY6hk
https://docs.comfy.org/tutorials/video/wan/wan2_2
```
## workflow:
```
video_wan2_2_14B_i2v.json
來自 https://docs.comfy.org/tutorials/video/wan/wan2_2
```
## GGUF:
```
wan2.2_i2v_high_noise_14B_Q4_K_S.gguf
wan2.2_i2v_low_noise_14B_Q4_K_S.gguf
來自 https://huggingface.co/bullerwins/Wan2.2-I2V-A14B-GGUF/tree/main
```
## lora:
```
lightx2v_I2V_14B_480p_cfg_step_distill_rank32_bf16.safetensors
lightx2v_I2V_14B_480p_cfg_step_distill_rank64_bf16.safetensors
(兩個擇一)
來自 https://huggingface.co/Kijai/WanVideo_comfy/tree/main/Lightx2v
```
## text_encoder:
```
umt5_xxl_fp8_e4m3fn_scaled.safetensors
來自 https://docs.comfy.org/tutorials/video/wan/wan2_2
```
## vae:
```
wan_2.1_vae.safetensors
來自 https://docs.comfy.org/tutorials/video/wan/wan2_2
```
## sage attention2.2: (加速影片生成用的，可有可無)
```bash
# 安裝triton
ComfyUI\.venv\Scripts\python.exe -m pip install -U triton-windows==3.3.1.post19
# 解除安裝pytorch2.9.1，安裝2.8.0cu128
# pytorch指令參考 https://pytorch.org/get-started/previous-versions/
ComfyUI\.venv\Scripts\python.exe -m pip uninstall torch torchaudio torchvision
pip install torch==2.8.0 torchvision==0.23.0 torchaudio==2.8.0 --index-url https://download.pytorch.org/whl/cu128
# 安裝sageattention
# 從 https://github.com/wildminder/AI-windows-whl?tab=readme-ov-file 下載pytorch2.8.0、python3.12、cuda12.8的版本，放在ComfyUI資料夾內
ComfyUI\.venv\Scripts\python.exe -m pip install .\sageattention-2.2.0+cu128torch2.8.0-cp312-cp312-win_amd64.whl
# 如需要安裝cuda toolkit 12.8，下載好後要將12.8版本的系統變數路徑放到高於其他toolkit版本的
```
```
藉由載入 workflow 來補齊缺失的節點
https://github.com/kijai/ComfyUI-WanVideoWrapper/tree/main/example_workflows
```
## 文件結構
```
📂 ComfyUI/
├─── 📂 models/
│     └── 📂 unet/
│     │    └── wan2.2_i2v_high_noise_14B_Q4_K_S.gguf
│     │    └── wan2.2_i2v_low_noise_14B_Q4_K_S.gguf
│     └── 📂 loras/
│     │    └── lightx2v_I2V_14B_480p_cfg_step_distill_rank64_bf16.safetensors
│     └── 📂 vae/
│     │    └── wan_2.1_vae.safetensors
│     └── 📂 text_encoders/
│          └── umt5_xxl_fp8_e4m3fn_scaled.safetensors
```
