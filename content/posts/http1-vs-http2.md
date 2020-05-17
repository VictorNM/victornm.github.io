---
title: "HTTP/1 vs HTTP/2"
date: 2020-05-17T13:47:44+07:00
draft: true
author: VictorNM
tags: ["comparision"]
---

Chào các bạn, trong bài viết này mình sẽ cùng tìm hiểu xem HTTP là gì, cùng với đó là so sánh sự khác biệt giữa HTTP/1 và HTTP/2 nhé.

# HTTP là gì?

Hiểu nôm na thì HTTP là 1 loại giao thức (protocol) cho phép client và server giao tiếp với nhau để truyền tải dữ liệu. Giao thức ở đây nghĩa là 1 cách thức chung để giao tiếp với nhau, ví dụ như khi bạn trò chuyện với người khác bằng tiếng Việt, nếu người đó cũng hiểu được tiếng Việt thì 2 người có thể giao tiếp với nhau, như vậy tiếng Việt là 1 loại giao thức chung giúp cho 2 người hiểu được nhau. Tương tự thì HTTP là 1 trong các cách giúp cho máy tính giao tiếp với nhau thông qua Interet.

# Các vấn đề của HTTP/1:

## Head of Line (HOL) blocking

Khi bạn mở browser lên và truy cập tới 1 server nào đó, ví dụ `example.com`, thì browser phải mở 1 connection tới server, sau đó thực hiện request và nhận response trên connection này. Tuy nhiên, khi muốn gửi tiếp 1 request khác trên cùng connection thì browser phải chờ cho request ban đầu thực hiện xong và nhận về được response, điều này chính là Head of Line blocking. Để giải quyết vấn đề này, browser sẽ mở thêm nhiều connection khác để cùng lúc send được nhiều request hơn, nhưng vẫn tồn tại 2 vấn đề, thứ 1, đó là số lượng connection tối đa là 6, thứ 2, mỗi khi open 1 connection, browser và server phải thực hiện TCP 3-ways handshake tương đôi tốn thời gian. HTTP/1 sau đó giới thiệu thêm 1 tính năng là Pipelining, cho phép browser gửi request mà không cần chờ response của request trước đó, tuy nhiên, server vẫn phải gửi response về theo thứ tự, nên cơ bản, HOL vẫn không được giải quyết triệt để.

## Uncompressed Headers

## Security

# HTTP/2 giúp giải quyết các vấn đề này như thế nào?