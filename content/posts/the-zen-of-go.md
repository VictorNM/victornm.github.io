---
title: "The Zen of Go"
date: 2020-05-17T22:29:23+07:00
draft: false
author: VictorNM
tags: ["go"]
---

Nhân dịp tao blog mình đăng lại 1 bài lược dịch cách đây không lâu của mình, chi tiết các bạn có thể tìm đọc tại bài gốc **"The Zen of Go"** của Dave Cheney: [https://dave.cheney.net/2020/02/23/the-zen-of-go](https://dave.cheney.net/2020/02/23/the-zen-of-go)
<!--more-->


## A GOOD PACKAGE STARTS WITH A GOOD NAME

Trong Go, mỗi package nên có một mục đích cụ thể, và cách tốt nhất để thể hiện mục đích đó là thông qua tên của nó - một danh từ. Tên của một package cho biết chức năng của package đó, và mỗi package trong Go nên chỉ có 1 mục đích / nhiệm vụ duy nhất. Lý do cho việc này chính là:

> *“Design is the art of arranging code to work today, and be changeable forever.”* - Sandi Metz

Sự thay đổi là tên của trò chơi mà chúng ta đang tham gia. Điều mà chúng ta – các lập trình viên – đang làm chính là quản lý sự thay đổi. Khi làm tốt, ta gọi đó là design, architecture. Khi làm không tốt, ta gọi đó là technical debt, legacy code.
Nếu bạn viết một chương trình chỉ cần chạy một lần, với một tập input cố định, thì không ai cần quan tâm tới việt code được viết ra là tốt hay xấu, bởi vì cuối cùng thì output của chương trình là tất cả những gì business quan tâm.
Tuy nhiên, điều đó không bao giờ đúng. Phần mềm luôn có bugs, các requirement sẽ thay đổi, input cũng thay đổi, và rất ít chương trình được viết ra chỉ để chạy một lần, vậy nên, chương trình của bạn sẽ phải thay đổi theo thời gian. Có thể chính bạn sẽ là người đảm nhiệm các thay đổi đó, nhưng đa phần sẽ là người khác, người phải chỉnh sửa code của bạn, người phải bảo trì code cho bạn.
Vậy, làm thế nào tạo ra các chương trình dễ thay đổi? Dùng inteface mọi lúc? Làm cho tất cả mọi thứ có thể mock? Lạm dụng dependency injection? Well, các kỹ thuật này có thể có ích với một số ít chương trình. Tuy nhiên, với đa phần chương trình, design để flexible sớm là over engineering.
Sẽ ra sao, nếu thay vì vậy, ta đơn giản là thay thế các component. Và cách tốt nhất để biết khi nào cần thay thế một thứ, đó là khi nó không còn đáp ứng được điều nó cần làm.
Một package tốt bắt đầu bằng một cái tên tốt. Nghĩ về tên của package như một "elevator pitch", chỉ sử dụng 1 từ, để miêu tả nhiệm vụ của nó. Khi cái tên không còn phù hợp với requirement, thay thế nó.
