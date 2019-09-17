# verilog_2048
Use FPGA implement a game named 2048
# Introduction
  我們做的 Final Project 是 2048 。
這是一款單人遊戲，由 19 歲的義大利人 Gabriele Cirulli 於 2014 年 3 月開發。遊戲任務是在一個網格上滑動小方塊來進行組合，每一次 滑動會出現 2，如果數􏰀一樣，疊再一起便會相加，直到形成一個 帶有有數􏰀 2048 的方塊。
# Motivation
  身為肥宅，如我們總會在休閒時候手機不離手，手機內容不是巴哈 姆特，傳說對決，那不然就是漫畫。但是我們有一個好朋友，他卻 是一直玩著 2048，起初我們剛認識他時，他 2048 不離手，走火入 魔似的，所以我們經常取笑他，笑他說你這樣整天看著數􏰀，交不 到女朋友的。但是，我們做夢也夢不到的事發生了，他教到了女朋 友了，他成為我們之中唯一一個脫魯的人，而且至今，仍玩著 2048 我們因為這些事情，而對 2048 有著不一樣的看法，便選擇做 2048
 
# System specification
  Block diagram
 
  State transition
# Design
  1. Keyboard
  我們keyboard主要是接了 W,A,S,D,Enter,Space這些功能。 分別是向上，向左，向下，向右 ， 返回 ， 進入遊戲畫面這些 功能。而􏰁入一個按鍵功能，我們又把它做編碼的動作是為了讓 後面做判斷的動作來得快些。
  像是 W 輸入，便給予 6bit 的 keyboard ,6’b010000;這樣分別可以 分別出每個 bit 代表著不同功能的輸入，對於後面的 state transition 以及 Move 的 module 更好來去判斷。
  2. Onepulse
  參考之前 Lab 的作法，其功用是使輸入的信號變成一個 clk cycle 長度。
  3. Debounce
  參考之前 Lab 的作法，其功用是使輸入信號乾淨無雜訊。
  4. 7-segment
  如同之前 Lab，將分數當作 input 輸入，在 FPGA 板上輸出現在的 分數。 而當遊戲輸掉後，FPGA 板上會改為顯示 LOSS 􏰀樣，只 有在顯示 LOSS 用的 CLK(loss_clk)變為 1 時顯示，造成閃爍的效果。
  5. Vga_display
  由於遊戲有兩個畫面，一個是利用輸入圖片另一個是參考網路上 資料畫出的邊線與數􏰀。方法是利用 state 來判斷，起始是在 state 0 ,如果按下 space(由 keyboard 判斷)，進入 state 1。 接著在利用之前所用過的
  Vga_controller ,mem_addr_gen ,blk_mem_gen 來 input 圖片，並且 加入條件是，如果 state 是在 state 0 才會讓 vga_red,vga_green,vga_blue 等於 pixel，如果是在其餘的 state 則 讓 vga_red,vga_green,vga_blue 等於 color 畫出來的參數。
  6. State
  總共有 5 個 state。第一個 state 主要是來 check space 是否已經按 下，如果按下，則進入下一個 state。第二個 state 主要是來判斷 按的 keyboard 是上、下、左、又、返回，只要有判讀到就進入下 一個 state。第三個 state 主要是來給予每個格子該有的數􏰀，方 法是如果按上，則給予最下排其中一格新的 2 ，給予值後就再進 入下一個 state。第四個 state 主要是做判斷是否不能再 Move 了 (lose)，或者已經贏了(2048)，如果是輸了的話，就到第 5 個 state 贏的話就重新開始。如果都不是的話，則回到第二個 state 繼續 接收 keyboard 的 input。
  7. Move
  Move 的方法是如果按了上，則對每一行都執行向上相加的功 能，在 move 裡會先判斷是否有一樣數􏰀的在鄰格，如果有的話 相加，沒有的話就移動到最上排。對於向左，向下，向右也是一 樣，對每一行或列作判斷並執行。
  8. Generate
  在 Generate 裡有 4 個參數來分別判斷有幾個是空的=>需要給予幾 位的 random (雖然需要產生的 random 格子只需要 1 格，但是要 給予其餘的格子是 0)所以才會有 rand2,rand3,rand4，以 rand2 為 例子，意思是如果 4 個參數裡有 2 位是空的代表需要 rand2 來產 生新的變數。
  9. Color
  這個 module 的功能是以當前某格子的數􏰀(0,2,4, ... , 8192)為 input，輸出這個格子裡應該長的樣子，然後傳到 top module 裡面 用 vga module 給出的 x, y 位置來拿取相應位置的顏色。
  10.Clock Divider
  將 FPGA 板上原給定的 CLK 除頻變為五種不同的 clock，分別有 1. clk_game 是遊戲主要用的 clk 2. clk_ran 是 Random module 所使用 的 clk 3. clk_vga 是 VGA module 使用的 clk 4. clk_fpga 是 7-segment 需要用的 clk 5. clk_lose 是為了製造閃爍效果使用到的 clk 至於 speaker 的 clk 則是使用最原始的 clk。
  11.Speaker
  Speaker 參考之前 Lab speaker 的部分，我們實作了背景音樂的功 能，進入遊戲之後會開始撥放音樂，接著隨著分數提高，音樂的 速度會逐漸加快。當 state 進入已經沒有方向可以動時，也就是 輸掉遊戲之後，會改為撥放第二個配樂，增加遊戲的豐富度。

# Experimental results
  
