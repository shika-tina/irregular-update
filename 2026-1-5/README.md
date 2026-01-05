# 26/01/05
1. 編寫簡單的 linux sh腳本(類似於windows的bat)
` 參見 linux_sh_outcome.png, linux_sh_script.png`

2. 將之前寫decode腳本時判斷檔案類型的程式提取出來，但在判斷 bat, cpp, py, json, html時無法精準判斷(因為他們的魔術標頭本質上是一樣的)，只能使用 mime=False 的描述來更進一步的判斷(結果的第二行) 
` 參見 filetype_judge.png`

3. 使用ghidra來進行逆向拆解，將crackme.c編譯過後刪掉， 新建專案->press I 選擇crackme->雙擊crackme開啟codebrowser->分析 ，可以看到中間的是組合語言，右側的decompiler是ghidra嘗試將組合語言轉回c語言虛擬碼

` 參見 ghidra_outcome.png`

4. 使用ncat進行雙向溝通，`ncat -lnvp 12345`(接收方監聽12345端口)，`ncat -v [接收端ip] 12345`(發送方連接接收方12345端口)，連接虛擬機端口時很順利，但反過來就遇到time out的問題，調適了很久主機上的防毒軟體(tcp排除+解除ip封鎖)才順利實現兩邊都可以雙向溝通

` 參見 ncat_outcome1.png ncat_outcome2.png`

` 參見 issue1.png`