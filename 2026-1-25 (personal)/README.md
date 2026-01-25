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
放到 C:\Users\Henry\Documents\ComfyUI\user\default\workflows
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