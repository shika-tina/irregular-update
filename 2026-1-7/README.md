# 更改c語言編譯過後的檔案，進行逆向拆解
有使用到以exeditor(一個十六進位修改器), ghidra以及gdb(兩個都是可以檢視組合語言的工具，ghidra同樣也可以修改內容)

簡單編寫一個c腳本，
```
#include <stdio.h>
#include <string.h>

int main() {
    char password[20];
    printf("請輸入授權密碼以解鎖進階功能: ");
    scanf("%19s", password);

    // 這是要破解的目標：硬編碼的密碼
    if (strcmp(password, "SuperSecret2026") == 0) {
        printf("驗證成功！存取權限已授予。\n");
    } else {
        printf("密碼錯誤，存取遭拒。\n");
    }
    return 0;
}
```
名為crackme.c，如果使用者輸入"SuperSecret2026"，那麼就通過身份驗證，如果錯誤，就會顯示密碼錯誤 <br>
將編譯過後的crackme留下來進行拆解，原本的crackme.c丟掉

1. <span style="color: red;"> GHIDRA </span>
    1. 安裝 `sudo apt update` -> `sudo apt install ghidra`

    2. 打開ghidra，在資料夾新增專案 -> 按 I -> 新增編譯過後的crackme -> 雙擊crackme打開十六進位檢視器(會出現小綠龍圖示)

    3. 找到main函式，可以看到 `CALL strcmp`，意思是比較並將結果進行回傳到 rax 暫存器裡，下一行 `TEST EAX, EAX`，意思是比較暫存器裡的數字，如果為0 那麼相同，接著在下方 `JNZ lab_001011cb`，JNZ 的意思是 Jump if Not Zero, 找到前面的 "Bytes" 欄位，會看到類似 74 0a 或 75 0a 的十六進位數值，74 是 `JZ` (Jump if Zero) 的機器碼。75 是 `JNZ` (Jump if Not Zero) 的機器碼。

    4. 點擊 JNZ 那一行，按下shift ctrl G 開啟修改器，將原本的 JNZ 改成 JZ，讓判斷條件變成反而只要輸入不是"SuperSecret2026"那麼就會通過驗證，接著保存、退出

    #### 結果 : 可以看到原本解不開猜不透的密碼(crackme)，只需要隨便敲個字串就能輕鬆通過身份驗證(crackme_ghidra)，當然你也可以不修改任何東西，在一開始就能看到密碼是什麼(左邊的 Decompile 視窗)

2. <span style="color: red;"> GDB </span> <br>
    gdb 是 kali 內建的工具，`gdb ./crackme`打開gdb
    ```
    layout asm   ->顯示出所有執行的命令
    disas main   ->定位main函式位置
    break main   ->在main函式下斷點
    break *0x5555555551b6   ->在這個步驟下斷點
    r   ->run執行
    n   ->next下一步
    x/s $rsi   ->查看rsi存放的字串
    x/s $rdi   ->查看rdi存放的字串
    x/s $rax   ->查看rax存放的字串
    set $rax = 0  ->將rax儲存的值設為0，這將會使記憶體認為 strcmp 的回傳值為0
    ```
    rdi, rsi, rax皆是暫存器，rdi存放使用者輸入，rsi存放程式中的字串，rax存放回傳值

    #### 結果: 在最後我們可以看到，同樣雖然密碼輸入不正確，但藉由在執行的過程中改變暫存器的值(strcmp回傳值)來"騙過"判斷(TEST EAX, EAX)

3. <span style="color: red;"> HEXEDITOR </span> <br>
    這是一個可以以十六進制更改檔案裡面內容的工具，前面提到 JNZ (不為零則跳轉) 的十六進位是 75，如果我們將它改成 JZ (為零則跳轉, 十六進位是 74)，或者將它改成 NOP (No Operation, 十六進位是 0x90)，這樣反而密碼錯誤會驗證成功

    1. 關於查找file offset的知識

        1. 記憶體位址 (Virtual Address)：在 Ghidra 看到的 001011b8 是程式被載入記憶體後的「預期位址」。 <br>
        2. 檔案偏移 (File Offset)：hexeditor 打開的是硬碟上的原始檔案，位址是從 0 開始算的。 <br>
        3. 位址隨機化 (ASLR)：現代 Linux 程式通常是 PIE (Position Independent Executable)，它們在檔案中的位址與在 Ghidra 顯示的虛擬位址完全不同。

        這會導致在 ghidra 中看到的 001011b8 還有 gdb 中run一遍後看到的 0x5555555551b8 這些座標並不被hexeditor認得，但我們可以透過在打開gdb還沒run過的時候，查看我們要修改的指令的在檔案中的檔案偏移(file offset)

        * 例如：位址是 001011b8，但 File Offset 可能是 0x11b8 或 0x1b8。

    2. 接著找到file offset後，ctrl T 查找目標指令的file offset，可以看到75 11的，可以改成 74(JZ)或是 90 90(NOP), 儲存並退出

    #### 結果: 執行後可以發現兩個更改的方法都適用，無論輸入的是什麼都會顯示身份驗證成功

4.  額外的紀錄
    1. 使用hexeditor時，不要使用十六進位查找(search for hex)，功能非常不穩定