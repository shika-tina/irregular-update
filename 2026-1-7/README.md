# 更改c語言編譯過後的檔案，進行逆向拆解
有使用到以exeditor(一個十六進位修改器), ghidra以及gdb(兩個都是可以檢視組合語言的工具，ghidra同樣也可以修改內容)

簡單編寫一個c腳本，名為crackme.c，內容大致上為如果使用者輸入"SuperSecret2026"，那麼就通過身份驗證，如果錯誤，就會顯示密碼錯誤，接下來將編譯過後的檔案留下來進行拆解，原本的c腳本丟掉

1. <span style="color: red;">ghidra</span>
    1. 安裝 `sudo apt install ghidra` -> `sudo apt install ghidra`

    2. 打開ghidra，在資料夾新增專案 -> 按 I -> 新增編譯過後的crackme -> 雙擊crackme打開小綠龍圖示

    3. 找到main函式，可以看到 `CALL strcmp`，意思是比較並將結果進行回傳到 rax 暫存器裡，如果為0 那麼相同，下一行 `TEST EAX, EAX`，意思是比較暫存器裡的數字，接著會在下方看到 `JNZ lab_001011cb`，JNZ 的意思是 Jump if Not Zero, 再找到前面的 "Bytes" 欄位，會看到類似 74 0a 或 75 0a 的十六進位數值，74 是 `JZ` (Jump if Zero) 的機器碼。75 是 `JNZ` (Jump if Not Zero) 的機器碼。並記下位址 001011cb。

    4. 點擊 JNZ 那一行，按下shift ctrl G 開啟修改器，將原本的 JNZ 改成 JZ，讓判斷條件變成反而只要輸入不是"SuperSecret2026"那麼就會通過驗證，接著保存、退出

    5. 結果 : 可以看到原本解不開猜不透的密碼(crackme)，只需要隨便敲個字串就能輕鬆通過身份驗證(crackme_ghidra)，當然你也可以不修改任何東西，在一開始就能看到密碼是什麼(左邊的 Decompile 視窗)

2. <span style="color: red;"> gdb </span> <br>
    gdb 是 kali 內建的工具，`gdb ./crackme`打開gdb
    ```
    layout asm -> 顯示出所有執行的命令
    disas main -> 定位main函式位置
    break main -> 在main函式下斷點
    break *0x5555555551b6 -> 在這個步驟下斷點
    r  ->  run執行
    n  ->  next下一步
    x/s $rsi  ->查看rsi存放的字串
    x/s $rdi  ->查看rdi存放的字串
    x/s $rax  ->查看rax存放的字串
    rdi, rsi, rax皆是暫存器，rdi存放使用者輸入，rsi存放程式中的字串，rax存放回傳值
    ```

