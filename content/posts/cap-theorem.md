---
title: "CAP Theorem"
date: 2020-05-26T14:51:46+07:00
draft: false
---

Trong bài viết hôm nay, mình sẽ tìm hiều về định lý CAP thông qua việc dịch bài gốc từ website của IBM Cloud nhé: [https://www.ibm.com/cloud/learn/cap-theorem](https://www.ibm.com/cloud/learn/cap-theorem)
<!--more-->

## What is the CAP theorem?

Định lý CAP phát biểu rằng: Trong một hệ thống phân tán, chỉ có 2 trong số 3 thuộc tính: **consistency**, **availability**, và **partition tolerance** là có thể tồn tại cùng lúc ('**C**', '**A**' và '**P**' trong CAP).

Một hệ thống phân tán là một network lưu trữ dữ liệu trong nhiều hơn 1 node (máy tính vật lý hoặc máy ảo) tại cùng một thời điểm. Bởi vì tất cả ứng dụng cloud đều là hệ thống phân tán, nên hiểu được định lý CAP là điều cần thiết để khi thiết kế một ứng dụng cloud, bạn có thể chọn được loại hệ thống quản lý dữ liệu phù hợp nhất.

<!--TODO
The CAP theorem is also called Brewer’s Theorem, because it was first advanced by Professor Eric A. Brewer during a talk he gave on distributed computing in 2000. Two years later, MIT professors Seth Gilbert and Nancy Lynch published a proof of “Brewer’s Conjecture.”
-->

## The ‘CAP’ in the CAP theorem, explained

### Consitency

Consistency có nghĩa là tại cùng một thời điểm, tất cả client sẽ thấy các dữ liệu giống nhau, bất kể client đó đang kết nối tới node nào. Đề đạt được điều này, bất cứ khi nào dữ liệu được lưu xuống ở một node thì nó cần phải forwarded hoặc replicated tới tất cả các node còn lại trong hệ thống ngay lập tức, khi đó việc lưu trữ mới được xem là thành công.

### Availability

Availability nghĩa là khi bất kỳ một client nào gửi request thì đều phải nhận được response, kể cả khi 1 hoặc nhiều node bị sập. Hay nói cách khác, tất cả các node đang hoạt động trong hệ thống phân tán đều phải phản hồi lại bất kỳ request nào, không có ngoại lệ.

### Partition tolerance

Partition là hiện tượng xảy ra khi giao tiếp trong hệ thống phân tán bị ngắt, có thể do mất kết nối hoặc kết nối bị chậm giữa 2 node với nhau. Partition tolerance nghĩa là cluster cần phải tiếp tục hoặt động bất kể có bao nhiêu node bị ngắt kết nối với nhau trong hệ thống.

## CAP theorem NoSQL database types

NoSQL database rất phù hợp cho các ứng dụng phân tán. Không giống như SQL database scale theo chiều dọc (vertically scale), các NoSQL databases được thiết kết để scale theo chiều ngang (horizontally scale) và hoạt động phân tán, chúng có thể nhanh chóng scale trên một network đang tăng trưởng bao gồm nhiều node liên kết với nhau.

Ngày này, các NoSQL databases được phân loại dựa theo 2 loại thuộc tính mà chúng hỗ trợ:

- **CP database:** hỗ trợ consistency và partition tolerance thay cho availability. Khi mà partition xuất hiện giữa bất kỳ 2 nodes nào, hệ thống phải tắt node không consitent đi cho tới khi giải quyết được partition.

- **AP database:** hỗ trợ availability và partition tolerance thay cho consitency. Khi partition xuất hiện giữa 2 node, cả 2 node đều available, tuy nhiên 1 node sẽ chữa dữ liệu cũ hơn so với node kìa. Khi partition được giải quyết, các AP datatabases sẽ đồng bộ dự liệu giữa các node lại.

- **CA database:** hỗ trợ consitency và availability. Nó không thể làm được điều này nếu partition xảy ra trong hệ thống, vì vậy nó không đảm bảo partition tolerance.

CA database được liệt kê cuối cùng vì 1 lý do: không thể tránh khỏi tình trạng partition trong một hệ thống phân tán. Vì vậy, chúng ta có thể thảo luận về CA database trên lý thuyết, nhưng trong thực tế, CA distribute database không thể tồn tại. Tuy nhiên, điều đó không có nghĩa là bạn không thể có một CA database cho ứng dụng phân tán của mình nếu cần. Nhiều cơ sở dữ liệu quan hệ (relational databases), như PostgreSQL, hỗ trợ consistency và availability và có thể được deployed trên nhiều node sử dụng replication.