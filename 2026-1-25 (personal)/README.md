# 圖片角度轉換

參考 https://github.com/jtydhr88/ComfyUI-qwenmultiangle

## 安裝與使用

```bash
# 新增專用節點
cd ComfyUI/custom_nodes
git clone https://github.com/jtydhr88/ComfyUI-qwenmultiangle.git
# 然後重啟comfyUI
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

## 結論

角度變換靈活，但諸如紋理、解頗學，手指等等的還是會偶爾出現一些怪異如果照片的主體不清晰或者某些手、四肢被遮擋

## 備註

1. 切記不要從網路上下載模型，因為有許多版本或者相似名稱的模型(例如2509和2511是不同的，也有分bf16和fp8)，下載工作流後，由ComfyUI自動偵測缺少哪些模型並下載就行

2. 這個lora是由qwen在huggingface上發表的，他和傳統的stable diffusion不同，它是基於qwen-image-edit-2511這個模型，所以無稐是lora或者diffusion-model接無法在invokeAI這種幾乎只支持stable-diffusion的平台上使用