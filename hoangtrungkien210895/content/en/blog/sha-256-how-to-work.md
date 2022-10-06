---
author: "Hoàng Kiên"
title: "Thuật toán băm là gì ?  Cách thức hoạt động của thuật toán  "
date: 2022-10-03
description: " Thuật toán băm là gì ? Cách thức hoạt động của thuật toán  "
tags: ["sha256", "how to work","Algorithm"]
thumbnail: /hash-la-gi-640.png
---

<h3>Mở đầu:</h3>

 Lời đầu : Có bao giờ bạn tự hỏi mọi thứ được vận hành như thế nào chưa ? Chắc ngày nhỏ chúng ta đã từng đặt ra vô số câu hỏi như thế rồi lại bỏ ngỏ vì chúng thực sự quá phức tạp . Bất kể những điều bạn nhìn thấy dù nó có đẹp đẽ hay xấu xí đến mức nào thì vốn dĩ chúng đều có nguyên nhân của nó ? Có câu nói nổi tiếng như này : trăm vạn hạt mưa rơi không hạt nào rơi nhầm chỗ, những người ta gặp không người nào là ngẫu nhiên. Nếu bạn đã bước vào bài viết này coi như chúng ta có duyên đi.  Hãy thử cá với mình bạn bỏ thời gian đọc hết bài viết của mình , mình sẽ chứng minh chỉ cần đủ kiên trì bạn sẽ hiểu được mọi thứ .

 


---


Ý tưởng  thuật toán: thuật toán băm sử dụng đầu vào là một thông tin bất kì hình ảnh , từ ngữ , âm thanh được biểu diễn qua các dãy bit 0 hoặc 1 . Dựa vào input đầu vào chúng ta sẽ thông qua các phép dịch bit và toán tử logic để thay đổi nội dung của dãy bit thành một dãy bit khác theo 1 công thức cố định . Dãy bit ta nhận được sau khi biến đổi thì ta gọi đó là mã hash (mã băm).

Khi ta có 1 đầu vào dữ liệu ta sẽ băm nó ra thành 1 mã băm đại diện cho đầu vào đó. ta tạm gọi đó là chữ ký của dữ liệu. Người ta sử dụng việc so sánh dữ liệu đó với chữ ký của dữ liệu để đảm bảo cho việc dữ liệu không bị thay đổi .

Vậy tại sao người ta lại phải mất công nghĩ ra những việc nhàm chán đó? 

Giờ mình đưa ra kịch bản như này để cho bạn thấy công dụng của nó nhé.  mình và bạn nhắn tin với nhau -> khi một tin nhắn đi đến đích -> bạn nhận được thông điệp nội dung tin nhắn . Câu hỏi đặt ra làm sao bạn biết tin nhắn đó người gửi chính là mình và ngược lại? bạn cần một thứ gì đó để định danh thông tin trên mạng phải không nào ? 
Giờ là lúc dùng đến nó rồi : Mã băm

Chúng mình quy ước với nhau mỗi lần mình gửi tin nhắn cho bạn (vd tin nhắn là "hey you" ) mình sẽ gửi kèm 1 mã băm( định danh đó là mình).Bằng cách băm thông tin mình gửi với một chuỗi ký tự bí mật giữa mình và bạn biết 

Vd: Mình khi gửi tin nhắn "hey you" thì mình sẽ gửi thêm 1 "mã băm A do kiên băm" được băm với đầu vào là : "hey you"+"secret lòng tin" .Trong đó "secret lòng tin" chính là chuỗi ký tự bí mật mình và bạn biết . Tin nhắn mình gửi sẽ có dạng thế này "hey you" và "mã băm A do kiên băm"

Bạn nhận thông tin và mã băm đó . Bạn có "secret lòng tin" chính là chuỗi ký bí mật dùng chung . Bạn dùng tin nhắn "hey you" mình gửi + "secret lòng tin" Và tiếp tục băm ra thành "mã băm A do bạn băm" . Nếu "mã băm A do kiên băm" và "mã băm A do bạn băm"  giống nhau thì bạn có thể xác định đó là mình .

Vậy khi lộ "secret lòng tin" thì phương thức này sẽ không còn an toàn và dễ mạo danh. Nhưng vấn đề khi thông tin bạn gửi đi qua internet thì chỉ có 2 thứ công khai . Thông tin và mã băm . trừ khi người ta có thể đảo ngược mã băm của bạn thì người ta mới lấy được "secret lòng tin" và người ta có thể nói cho bạn bất kì điều gì với danh nghĩa là mình. 

Nhưng việc từ mã băm người ta đảo ngược lại lấy thông tin đầu vào lại một điều không thể ? tại sao? 

ví dụ : chuyển đổi nguồn là bit 1 thành kết quả bit 0 sử dụng toán tử xor với chính nó (1 xor 1 = 0) và coi bit 0 này là  mã băm . 

Vậy từ kết quả 0 ta có thể suy ra bit 1 là nguồn sau khi sử dụng toán tử xor với chính nó không?

Câu trả lời là "không" vì  nó cũng là kết quả nếu nguồn là bit 0  sử dụng toán tử xor với chính nó ( 0 xor 0 = 0 ) 

vậy khi cố gắng suy ngược kết quả ta chỉ có thể có các "tập kết quả khả năng là đầu vào là nguồn để tạo ra bit 0 " thế đấy bit 1 đã chuyển đổi thành 0 rồi nên k thể đảo ngược được


---
<h1>Đi cùng mình việc mình băm với đầu vào là "hello world" nhé :</h1>

Bài toán đưa ra băm ký tự : "hello world" dùng thuật toán mã băm (SHA-256) để băm nó
 
Quote :"Mọi thứ trên đời đều được máy tính đánh cho một mã đại diện cho nó"

( để hiểu tại sao 0 và 1 lại có thể được biểu diễn được tất cả mọi thứ bạn hãy đọc : https://hoangtrungkien210895.github.io/en/blog/van-vat-internet/
 )

Bước 1 Chuyển đổi : Ta có đầu vào là "hello world" sau khi phiên dịch sang các bit chúng sẽ có giá trị dãy bit là :

  01101000 01100101 01101100 01101100 01101111 00100000 01110111 01101111 01110010 01101100 01100100

( tổng số 0 và 1 nếu đếm sẽ có: 88 ký tự )

 ![Placeholder](/sha1.png)

Bước 2 Lấp đầy : thêm vào đuôi của dãy bit "hello world" với bit là 1 để ngầm hiểu rằng nó là kết thúc của thông tin đầu vào chúng ta cần băm. 
Tiếp tới ta thêm các chuỗi ký tự 0 theo sau tùy ý sao cho sau khi + với 64 bit cuối đại diện cho chiều dài bit của đầu vào dữ liệu( 88 ký tự ) có độ dài chia hết cho 512.

cụ thể ta có đường màu đỏ đại diện cho "hello world" -> bit 1 xanh lá gạch chân đại diện cho kết thúc đầu vào -> màu nâu đại diện cho các bit 0 được thêm -> màu cam đại diện cho 64 bit nhớ có giá trị là 88 :

![Placeholder](/saulapday.png)


Bước 3 Khởi tạo cứng : Định nghĩa  8 dãy dữ liệu h0 -> h7 mỗi dãy 32 bit nhớ : 

{{< highlight java >}}
h0 = 0x6a09e667 ( là dãy bit được thể hiện với hệ đếm 16: 0x6a09e667 tương đương 32 bit: 01101010000010011110011001100111 )

h1 = 0xbb67ae85 

h2 = 0x3c6ef372

h3 = 0xa54ff53a

h4 = 0x510e527f

h5 = 0x9b05688c

h6 = 0x1f83d9ab

h7 = 0x5be0cd19 

và định nghĩa tiếp 64 dãy dữ liệu mỗi dãy cũng là 32 bit

k[0..63] :=

   0x428a2f98, 0x71374491, 0xb5c0fbcf, 0xe9b5dba5, 0x3956c25b, 0x59f111f1, 0x923f82a4, 0xab1c5ed5,

   0xd807aa98, 0x12835b01, 0x243185be, 0x550c7dc3, 0x72be5d74, 0x80deb1fe, 0x9bdc06a7, 0xc19bf174,

   0xe49b69c1, 0xefbe4786, 0x0fc19dc6, 0x240ca1cc, 0x2de92c6f, 0x4a7484aa, 0x5cb0a9dc, 0x76f988da,

   0x983e5152, 0xa831c66d, 0xb00327c8, 0xbf597fc7, 0xc6e00bf3, 0xd5a79147, 0x06ca6351, 0x14292967,

   0x27b70a85, 0x2e1b2138, 0x4d2c6dfc, 0x53380d13, 0x650a7354, 0x766a0abb, 0x81c2c92e, 0x92722c85,

   0xa2bfe8a1, 0xa81a664b, 0xc24b8b70, 0xc76c51a3, 0xd192e819, 0xd6990624, 0xf40e3585, 0x106aa070,

   0x19a4c116, 0x1e376c08, 0x2748774c, 0x34b0bcb5, 0x391c0cb3, 0x4ed8aa4a, 0x5b9cca4f, 0x682e6ff3,

   0x748f82ee, 0x78a5636f, 0x84c87814, 0x8cc70208, 0x90befffa, 0xa4506ceb, 0xbef9a3f7, 0xc67178f2
{{< /highlight >}}

Bước 4 lấp đầy tiếp :
 Với bước 2 lấp đầy ta có đầu vào là 512 bit (hình dưới)

![Placeholder](/saulapday.png)

Với mỗi 32 bit đứng gần nhau ta hình thành nên  1 "word" (Ký hiệu là W ) vậy 512 bit trong hình ta sẽ hình thành 512/32 = 16 "word" 

Ta tiếp tục thêm bit 0 sau 512 bit ban đầu cho đến khi ta hình thành đủ 64 "word" gồm: 

16 "word" đến từ bước 2 và 48 "word" đến từ việc ta thêm bit 0 . (hình dưới)

![Placeholder](/chia-bang.png)

Bước 5: Áp dụng các phép cộng và dịch bit :

Đặt i bắt đầu từ 16 chạy đến 63 , với mỗi i ta thực hiện tuần tự phép tính  để gán giá trị mới cho w[i]:

công thức :
{{< highlight java >}}
for i from 16 to 63
        s0 := (w[i-15] rightrotate  7) xor (w[i-15] rightrotate 18) xor (w[i-15] rightshift  3)
        s1 := (w[i-2] rightrotate 17) xor (w[i-2] rightrotate 19) xor (w[i-2] rightshift 10)
        w[i] := w[i-16] + s0 + w[i-7] + s1

{{< /highlight >}}

hình ảnh tính toán :

{{< highlight java >}}

w[1] rightrotate 7: (phép xoay bit sang phải )
  01101111001000000111011101101111 -> 11011110110111100100000011101110
w[1] rightrotate 18:(phép xoay bit sang phải )
  01101111001000000111011101101111 -> 00011101110110111101101111001000
w[1] rightshift 3: (phép dịch bit phải )
  01101111001000000111011101101111 -> 00001101111001000000111011101101

// sử dụng toán tử logic XOR (ngoại trừ hoặc)

s0 = 11011110110111100100000011101110 XOR 00011101110110111101101111001000 XOR 00001101111001000000111011101101

s0 = 11001110111000011001010111001011

w[14] rightrotate 17:
  00000000000000000000000000000000 -> 00000000000000000000000000000000
w[14] rightrotate19:
  00000000000000000000000000000000 -> 00000000000000000000000000000000
w[14] rightshift 10:
  00000000000000000000000000000000 -> 00000000000000000000000000000000

s1 = 00000000000000000000000000000000 XOR 00000000000000000000000000000000 XOR 00000000000000000000000000000000

s1 = 00000000000000000000000000000000

w[16] = w[0] + s0 + w[9] + s1

w[16] = 01101000011001010110110001101100 + 11001110111000011001010111001011 + 00000000000000000000000000000000 + 00000000000000000000000000000000

// w[16] hiểu cơ bản là 01101000011001010110110001101100 + 11001110111000011001010111001011 tương đương 1751477356 + 3470890443 = 5222367799 ( 0100110111010001110000001000110111 có 34 bit ) ta sẽ lấy 32 bit đầu tiên tính từ trái sang để gán cho w[16]

w[16] = 00110111010001110000001000110111

{{< /highlight >}}

Sau khi chạy xong ta sẽ có kết quả là:
{{< highlight java >}}
01101000011001010110110001101100 01101111001000000111011101101111
01110010011011000110010010000000 00000000000000000000000000000000
00000000000000000000000000000000 00000000000000000000000000000000
00000000000000000000000000000000 00000000000000000000000000000000
00000000000000000000000000000000 00000000000000000000000000000000
00000000000000000000000000000000 00000000000000000000000000000000
00000000000000000000000000000000 00000000000000000000000000000000
00000000000000000000000000000000 00000000000000000000000001011000
00110111010001110000001000110111 10000110110100001100000000110001
11010011101111010001000100001011 01111000001111110100011110000010
00101010100100000111110011101101 01001011001011110111110011001001
00110001111000011001010001011101 10001001001101100100100101100100
01111111011110100000011011011010 11000001011110011010100100111010
10111011111010001111011001010101 00001100000110101110001111100110
10110000111111100000110101111101 01011111011011100101010110010011
00000000100010011001101101010010 00000111111100011100101010010100
00111011010111111110010111010110 01101000011001010110001011100110
11001000010011100000101010011110 00000110101011111001101100100101
10010010111011110110010011010111 01100011111110010101111001011010
11100011000101100110011111010111 10000100001110111101111000010110
11101110111011001010100001011011 10100000010011111111001000100001
11111001000110001010110110111000 00010100101010001001001000011001
00010000100001000101001100011101 01100000100100111110000011001101
10000011000000110101111111101001 11010101101011100111100100111000
00111001001111110000010110101101 11111011010010110001101111101111
11101011011101011111111100101001 01101010001101101001010100110100
00100010111111001001110011011000 10101001011101000000110100101011
01100000110011110011100010000101 11000100101011001001100000111010
00010001010000101111110110101101 10110000101100000001110111011001
10011000111100001100001101101111 01110010000101111011100000011110
10100010110101000110011110011010 00000001000011111001100101111011
11111100000101110100111100001010 11000010110000101110101100010110

{{< /highlight >}}


B6: Tiếp tục chạy vòng lặp

{{< highlight java >}}

Khởi tạo các biến:

    a = h0
    b = h1
    c = h2
    d = h3
    e = h4
    f = h5
    g = h6
    h = h7


Đặt i bắt đầu từ 0 chạy đến 63 , với mỗi i ta thực hiện tuần tự phép tính  để gán giá trị mới cho các biến a ,b ,c ta đã khởi tạo:
     
    for i from 0 to 63
        S1 = (e rightrotate 6) xor (e rightrotate 11) xor (e rightrotate 25)
        ch = (e and f) xor ((not e) and g)
        temp1 := h + S1 + ch + k[i] + w[i]
        S0 = (a rightrotate 2) xor (a rightrotate 13) xor (a rightrotate 22)
        maj = (a and b) xor (a and c) xor (b and c)
        temp2 = S0 + maj
 
        h = g
        g = f
        f = e
        e = d + temp1
        d = c
        c = b
        b = a
        a = temp1 + temp2

{{< /highlight >}}


Cách tính toán :

{{< highlight java >}}
Ta áp dụng các phép phủ định , phép dịch bit  , phép xoay bit , toán tử logic xor , 
và đặc biệt dấu cộng là ta cộng với modulo 32 (để dễ hiểu bạn chuyển đổi sang hệ số 10. 
sau đó cộng hệ số 10 lấy value decimal -> chuyển ngược thành dãy bit sau đó lấy 32 bit đầu từ bên trái sang)

a = 0x6a09e667 = 01101010000010011110011001100111
b = 0xbb67ae85 = 10111011011001111010111010000101
c = 0x3c6ef372 = 00111100011011101111001101110010
d = 0xa54ff53a = 10100101010011111111010100111010
e = 0x510e527f = 01010001000011100101001001111111
f = 0x9b05688c = 10011011000001010110100010001100
g = 0x1f83d9ab = 00011111100000111101100110101011
h = 0x5be0cd19 = 01011011111000001100110100011001

e rightrotate 6:
  01010001000011100101001001111111 -> 11111101010001000011100101001001
e rightrotate 11:
  01010001000011100101001001111111 -> 01001111111010100010000111001010
e rightrotate 25:
  01010001000011100101001001111111 -> 10000111001010010011111110101000
S1 = 11111101010001000011100101001001 XOR 01001111111010100010000111001010 XOR 10000111001010010011111110101000
S1 = 00110101100001110010011100101011

e and f:
    01010001000011100101001001111111
  & 10011011000001010110100010001100 =
    00010001000001000100000000001100
not e:
  01010001000011100101001001111111 -> 10101110111100011010110110000000
(not e) and g:
    10101110111100011010110110000000
  & 00011111100000111101100110101011 =
    00001110100000011000100110000000
ch = (e and f) xor ((not e) and g)
   = 00010001000001000100000000001100 xor 00001110100000011000100110000000
   = 00011111100001011100100110001100

temp1 = h + S1 + ch + k[i] + w[i]
temp1 = 01011011111000001100110100011001 + 00110101100001110010011100101011 + 00011111100001011100100110001100 + 01000010100010100010111110011000 + 01101000011001010110110001101100
temp1 = 01011011110111010101100111010100

a rightrotate 2:
  01101010000010011110011001100111 -> 11011010100000100111100110011001
a rightrotate 13:
  01101010000010011110011001100111 -> 00110011001110110101000001001111
a rightrotate 22:
  01101010000010011110011001100111 -> 00100111100110011001110110101000
S0 = 11011010100000100111100110011001 XOR 00110011001110110101000001001111 XOR 00100111100110011001110110101000
S0 = 11001110001000001011010001111110

a and b:
    01101010000010011110011001100111
  & 10111011011001111010111010000101 =
    00101010000000011010011000000101
a and c:
    01101010000010011110011001100111
  & 00111100011011101111001101110010 =
    00101000000010001110001001100010
b and c:
    10111011011001111010111010000101
  & 00111100011011101111001101110010 =
    00111000011001101010001000000000
maj = (a and b) xor (a and c) xor (b and c)
    = 00101010000000011010011000000101 xor 00101000000010001110001001100010 xor 00111000011001101010001000000000 
    = 00111010011011111110011001100111

temp2 = S0 + maj
      = 11001110001000001011010001111110 + 00111010011011111110011001100111
      = 00001000100100001001101011100101

h = 00011111100000111101100110101011
g = 10011011000001010110100010001100
f = 01010001000011100101001001111111
e = 10100101010011111111010100111010 + 01011011110111010101100111010100 = 00000001001011010100111100001110
d = 00111100011011101111001101110010
c = 10111011011001111010111010000101
b = 01101010000010011110011001100111
a = 01011011110111010101100111010100 + 00001000100100001001101011100101 = 01100100011011011111010010111001

{{< /highlight >}}

B7 : Thu thành quả:



 sau bước 6 ta có các biến a b c d e f g h và các biến h0->h7 từ bước 3 đã được tính toán giá trị:
{{< highlight java >}}
h0 = 6A09E667 = 01101010000010011110011001100111
h1 = BB67AE85 = 10111011011001111010111010000101
h2 = 3C6EF372 = 00111100011011101111001101110010
h3 = A54FF53A = 10100101010011111111010100111010
h4 = 510E527F = 01010001000011100101001001111111
h5 = 9B05688C = 10011011000001010110100010001100
h6 = 1F83D9AB = 00011111100000111101100110101011
h7 = 5BE0CD19 = 01011011111000001100110100011001

a = 4F434152 = 01001111010000110100000101010010
b = D7E58F83 = 11010111111001011000111110000011
c = 68BF5F65 = 01101000101111110101111101100101
d = 352DB6C0 = 00110101001011011011011011000000
e = 73769D64 = 01110011011101101001110101100100
f = DF4E1862 = 11011111010011100001100001100010
g = 71051E01 = 01110001000001010001111000000001
h = 870F00D0 = 10000111000011110000000011010000
{{< /highlight >}}

Ta sẽ tính toán bước cuối cùng

{{< highlight java >}}
    h0 = h0 + a
    h1 = h1 + b
    h2 = h2 + c
    h3 = h3 + d
    h4 = h4 + e
    h5 = h5 + f
    h6 = h6 + g
    h7 = h7 + h


Mã hash = h0 append h1 append h2 append h3 append h4 append h5 append h6 append h7

=> tương đương với :
h0 = h0 + a = 10111001010011010010011110111001 -> B94D27B9 (chuyển từ hệ 2 sang hệ 16)
h1 = h1 + b = 10010011010011010011111000001000 -> 934D3E08
h2 = h2 + c = 10100101001011100101001011010111 -> A52E52D7
h3 = h3 + d = 11011010011111011010101111111010 -> DA7DABFA
h4 = h4 + e = 11000100100001001110111111100011 -> C484EFE3
h5 = h5 + f = 01111010010100111000000011101110 -> 7A5380EE
h6 = h6 + g = 10010000100010001111011110101100 -> 9088F7AC
h7 = h7 + h = 11100010111011111100110111101001 -> E2EFCDE9

Mã hash = h0 append h1 append h2 append h3 append h4 append h5 append h6 append h7

Mã hash = B94D27B9 + 934D3E08 + A52E52D7 + DA7DABFA + C484EFE3 + 7A5380EE + 9088F7AC + E2EFCDE9

Mã hash = B94D27B9934D3E08A52E52D7DA7DABFAC484EFE37A5380EE9088F7ACE2EFCDE9

{{< /highlight >}}

Vậy ta sẽ thu được mã hash của "hello world" -> B94D27B9934D3E08A52E52D7DA7DABFAC484EFE37A5380EE9088F7ACE2EFCDE9




Hoàng Kiên 03/10/2022



{{< css.inline >}}

<style>
.canon { background: white; width: 100%; height: auto; }
</style>

{{< /css.inline >}}
