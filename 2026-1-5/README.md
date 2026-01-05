# 26/01/05
1. 編寫簡單的 linux sh腳本(類似於windows的bat)
` -> linux_sh_outcome.png, linux_sh_script.png`

2. 將之前寫decode腳本時判斷檔案類型的程式提取出來，但在判斷 bat, cpp, py, json, html時無法精準判斷(因為他們的魔術標頭本質上是一樣的)，只能使用 mime=False 的描述來更進一步的判斷(結果的第二行) 
` -> filetype_judge.png`

3. 使用 ghidra 來進行逆向拆解，將 crackme.c 編譯過後刪掉，新建專案->press I 選擇crackme->雙擊crackme開啟 codebrowser->分析
` -> ghidra_outcome.png, ncat_outcome2.png`

5. 可以看到中間的是組合語言，右側的decompiler是ghidra嘗試將組合語言轉回c語言虛擬碼
` -> issue1.png`