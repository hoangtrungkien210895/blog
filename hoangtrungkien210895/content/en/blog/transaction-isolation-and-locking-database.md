---
author: "Hoàng Kiên"
title: "Transaction isolation and Locking Database"
date: 2022-10-01
description: "Transaction isolation and Locking Database "
tags: ["Transaction", "isolation","Locking"]
thumbnail: /avatar-locking.png
---

<h3>Mở đầu:</h3>

 Lời đầu : Như các bạn biết với mọi chương trình thì việc lưu và truy xuất thông tin là một việc vô cùng quan trọng . Việc lưu và truy xuất thông tin được bắt nguồn từ việc lưu và đọc các thông tin trên các file trên máy tính . Dần dần hiểu được tầm quan trọng của việc lưu và truy xuất , Chúng ta phát triển ra những chương trình để quản lý lưu và truy xuất thông tin và chúng ta gọi nó là "Database"  . Vậy hôm nay mình sẽ giới thiệu về hai cơ chế hoạt động vô cùng quan trọng trong database đó là Transaction isolation and Locking .

 Mục đích : Đưa ra ý tưởng cụ thể về cách thức hoạt động Transaction isolation and Locking trong Database . ví dụ thực tế khi phát triển chương trình sử dụng Transaction isolation and Locking trong Database .

---


<h3>Các khái niệm cơ bản :</h3>

Để mở đầu các khái niệm cơ bản mình sẽ đưa ra ý tưởng step-to-step về quá trình kết nối và làm việc tới database như sau:

Chương trình -> Tạo connection vật lý tới database -> Connection thiết lập phiên làm việc (Session) với database   -> Trong session mở ra các transaction(phiên giao dịch) -> Transaction là nơi thực thi lệnh tới cơ sở dữ liệu -> Commit hoặc Rollback transaction để kết thúc 1 transaction.

Vậy Locking và Transaction isolation là gì?

---

<h3>Locking</h3>

- Locking là việc thực hiện "khóa" một unit (ví dụ : row , table ) trong database đảm bảo cho việc đồng bộ truy xuất thao tác với unit đó. Tránh để dữ liệu bị ảnh hưởng bởi các transaction user khác thay đổi trên chính cùng unit dữ liệu. 
 
  Ví dụ : Khi chúng ta  thay đổi dữ liệu bất kì . Transaction của chúng ta sẽ "đòi" database system cung cấp cho chúng ta một cái "lock độc quyền " đại diện cho dữ liệu đó  . Chỉ transaction cầm khóa độc quyền mới được phép update dữ liệu mà khóa độc quyền đó đại diện. Khi có lệnh commit hoặc rollback thực thi sẽ kết thúc transaction và cũng là lúc transaction trả khóa lại cho database sytem.

- Đối với các cơ sở dữ liệu khác nhau thì sẽ có các kiểu khóa khác nhau (Lock modes / Lock types) nhưng bản chất chúng sẽ xoay quanh 2 loại khóa lớn : Exclusive locks and Shared locks.

- Exclusive locks : Hiểu cơ bản là khóa được sinh ra cho việc INSERT, UPDATE, hoặc DELETE data . Khi database system cung cấp cho transaction một Exclusive locks đại diện cho một unit (row,table ... ) thì sẽ không có transaction khác nào có thể có cùng khóa Exclusive locks đại diện unit đó trên một thời điểm . Chúng bắt buộc phải đợi transaction giữ khóa trả lại mới có thể cũng cấp cho transaction mới. do đó các câu lệnh INSERT, UPDATE, or DELETE sẽ đảm bảo tính đồng bộ khi thay đổi dữ liệu .

- Shared locks: Hiểu cơ bản là khóa sinh ra cho việc khi một transaction đọc một bản ghi và chỉ muốn bản ghi này không được thay đổi bới các transaction khác . Database system có thể cung cấp cho nhiều transaction khác nhau cùng một shared locks đại diện cho 1 unit cùng 1 thời điểm . Nhưng khi shared lock cho unit đó còn tồn tại và chưa thu hồi hết thì database system không thể cung cấp Exclusive locks với unit đó .

- Vậy việc cung cấp 1 Exclusive locks cho một unit sẽ phải phụ thuộc vào tình trạng cung cấp Shared locks của unit đó và ngược lại khi Exclusive locks còn chưa thu hồi thì việc cấp Shared locks cũng phải "đợi".

- Khi muốn thay đổi dữ liệu của 1 unit ta bắt buộc phải chiếm được khóa Exclusive locks

- Từ việc "khóa" dữ liệu ta phát triển thành hai loại chiến lược  để đảm bảo việc control data .
Pessimistic  control (control bi quan ) và Optimistic control(control lạc quan)

---

<h3>Transaction isolation </h3>

Transaction isolation : (tính cô lập trong một transaction) sẽ có 4 cấp độ:  Read uncommitted -> Read committed -> Repeatable read -> Serializable.

Mình sẽ chia làm 2 phần lớn : Read uncommitted -> Read committed và Repeatable read -> Serializable.

1: Read uncommitted -> Read committed

Đúng như tên gọi với Read uncommitted và Read committed sẽ là 2 loại isolation mà khi được set trong transaction sẽ quyết định transaction của bạn có được ưu tiên đọc loại dữ liệu nào ? commited hoặc chưa commited ?
Việc 1 transaction chiếm khóa và thay đổi dữ liệu nhưng chưa kết thúc transaction thì trạng thái mới của bản ghi đã thay đổi gọi là uncommited trong khi đó trạng thái cũ cho đến lần gần nhất được commit thành công của dữ liệu gọi là committed.

Note: với uncommited ta có thể rollback .

2: Repeatable read -> Serializable.

Là việc transaction khi thực hiện câu lệnh đọc sẽ auto đòi khóa với dữ liệu truy vấn ra (với việc đòi khóa khi đọc dẫn tới trạng thái dữ liệu đọc luôn là commited vì nếu có dữ liệu uncommited thì chúng phải đợi khóa được nhả ra ) . Việc đòi khóa như nào với 2 isolation này còn tùy cả vào từng database impl khác nhau . 

Mình sẽ lấy sql-server làm ví dụ với Repeatable read -> Serializable thì cả 2 trạng thái này nếu ta sử dụng đều đòi khóa trên dữ liệu chúng truy vấn ra . Điểm khác là Repeatable read sẽ không khóa theo range còn Serializable sẽ khóa theo cả range tìm kiếm . VD: WHERE ID > 5 and ID < 10;  

Chú ý: có những case khác nhau như Mariadb thì Repeatable read sẽ k đòi khóa chúng sẽ có cơ chế đảm bảo cho việc cùng 1 câu lệnh truy vấn sẽ có cùng 1 kết quả trong 1 transaction. Bởi vậy anh em nên đọc kĩ document của 2 loại isolation này theo từng loại database anh em dùng.


**Kết luận :  Tổng kết cả 2 phần ta sẽ hình thành được mindset về việc cơ chế cấp phát khóa , việc đọc dữ liệu uncommitted,committed và đọc dữ liệu kết hợp đòi khóa .** 

**Anh em thấy đấy việc một bản ghi của anh em bị locking và không thể đọc được đó là kết quả của một cuộc thương thảo bất thành giữa thằng giữ khóa và 1 thăng đòi khóa :)))**


---

<h3>Phần DEMO</h3>

Mình sẽ demo trực quan khi sử dụng spring jpa và  DBeaver để demo các kịch bản cho phần locking và phần transaction isolation Như là 2 thằng user cùng sử thao tác trên bản ghi chung 1 thời điểm

Theo các tình huống sau:

--

**Demo locking : Exclusive locks**

Kịch Bản Lock row : (1) JPA đòi khóa dữ liệu bằng câu lệnh sql đòi khóa ,  (2)  DBeaver thực hiện update dữ liệu hoặc (3)đọc dữ liệu đòi khóa thì bị fail do bị thằng JPA đã chiếm khóa 

(1) JPA đòi khóa dữ liệu bằng câu lệnh sql đòi khóa Exclusive locks cho row id '01b293a0-c290-4ee5-96ff-40baafb2897a'  

(SELECT *  FROM LOYALTY WHERE id = '01b293a0-c290-4ee5-96ff-40baafb2897a' FOR UPDATE)

![Placeholder](/k1.png)

(2)DBeaver thực hiện update dữ liệu bị block do thằng JPA đã chiếm Exclusive locks

![Placeholder](/k2.png)

(3)DBeaver đọc dữ liệu đòi khóa bị block do thằng JPA đã chiếm Exclusive locks

![Placeholder](/k3.png)


---

**Demo isolation read un-commited và commited**

Kịch bản: dùng sql thay đổi dữ liệu loyalty từ 193 điểm thành 194 điểm nhưng k commit transaction . Dùng JPA set Transaction isolation đọc 2 trạng thái của bản ghi là commited và un-commited

Dữ liệu trước khi thay đổi 193 điểm loyalty

![Placeholder](/k4.png)

Update dữ liệu nhưng chưa commit transaction: + 1 loyalty

![Placeholder](/k5.png)


JPA set isolation read commited . 193 Điểm

![Placeholder](/k6.png)


JPA set isolation read un-commited . 194 Điểm

![Placeholder](/k7.png)


**Demo isolation khi đọc rồi tự động chiếm khóa**

Kịch bản: (1) JPA set isolation là Serializable khi thực hiện câu lệnh select tự chiếm khóa . (2)  DBeaver thực hiện  sql update fail

(1) JPA set isolation là Serializable khi thực hiện câu lệnh select sẽ tự chiếm khóa

![Placeholder](/k8.png)

(2)  DBeaver thực hiện update fail
![Placeholder](/k9.png)


Hoàng Kiên 01/10/2022



{{< css.inline >}}

<style>
.canon { background: white; width: 100%; height: auto; }
</style>

{{< /css.inline >}}
