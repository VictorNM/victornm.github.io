---
title: "Testing and Refactoring"
date: 2020-05-21T08:39:31+07:00
draft: false
author: VictorNM
---

Sau một thời gian học và làm việc trong ngành lập trình này, mình nhận ra có 2 kỹ năng cực kỳ thiết yếu để trở thành một lập trình viên tốt, đó là Testing và Refactoring. Tuy nhiên, có vẻ như đây lại là 2 kỹ năng rất ít được đề cập tới cả trong trường học lần ở các công ty. Vậy Test là gì, và Refactor là gì, tại sao mình lại cho rằng nó là thiết yếu, hôm nay mình sẽ trình bày một số suy nghĩ về điều này nhé.
<!--more-->

## Refactoring

### Definition

Refactoring là gì? Mình sẽ dẫn lại định nghĩa về "refactoring" trong cuốn "Refactoring - Improving the Design of Existing Code" của Martin Fowler:

> Refactoring (noun): a change made to the internal structure of software to make it easier to understand and cheaper to modify without changing its observable behavior.

> Refactoring (verb): to restructure software by applying a series of refactorings without changing its observable behavior.

Vậy để hiểu đơn giản thì "refactoring" là việc chỉnh sửa code để làm cho code dễ hiểu và dễ thay đổi hơn, mà không làm thay đổi behavior của phần code được chỉnh sửa đó. Ví dụ ngày trước mình có gặp một hàm lấy thông tin Account của AWS về verify và lưu vào DB như sau:

{{< highlight php >}}
public function verifyUser()
{
    // init $client
    // ...
    $account = new Account()
    $user = $client->getUser()['User'];
    $account->iam_arn = $user['Arn'];
    preg_match('/(?<=::)([0-9]+):/', $user['Arn'], $amz_id);
    $account->amz_id = $amz_id[1];

    // some verification
    // ...
    $account->save();
}
{{< /highlight >}}

Thực tế hàm dài và phức tạp hơn vậy, nhưng mình chỉ trích 1 đoạn ngắn để mình họa. Như các bạn có thể thấy thì hàm sẽ thực hiện gọi client của AWS để lấy thông tin user gồm IAM ARN và Amazon ID, tuy nhiên có 1 đoạn khi lần đầu đọc tới mình thực sự không hiều là dùng làm gì:

{{< highlight php >}}
public function verifyUser()
{
    // ...

    preg_match('/(?<=::)([0-9]+):/', $iam_user['Arn'], $amz_id);
    $account->amz_id = $amz_id[1];

    // ...
}
{{< /highlight >}}

Sau một hồi nghiên cứu và đọc document của AWS thì mình mới biết được trong IAM ARN của AWS thì có chứa Amazon ID, ví dụ IAM_ARN của account = "arn:aws:iam::123456789012:user/JohnDoe" thì số "123456789012" chính là Amazon ID của account đó, tức là đoạn code này đang dùng regex để tách Amazon ID ra. Chính vì bị confuse như vậy khi đọc code nên sau khi hiểu ra, mình quyết định refactor như sau: 

{{< highlight php >}}
public function verifyUser()
{
    // init $client
    // ...
    $account = new Account()
    $user = $client->getUser()['User'];
    $account->iam_arn = $user['Arn'];
    $account->amz_id = extractAmazonId($account->iam_arn);

    // some verifications
    // ...
    $account->save();
}

private function extractAmazonId($iamArn)
{
    preg_match('/(?<=::)([0-9]+):/', $iamArn, $matches);
    return $matches[1];
}

{{< /highlight >}}

Các bạn thấy đấy, mình chỉ đơn giản là tách 2 dòng code thành 1 hàm riêng, tuy nhiên đã làm code dễ hiểu hơn hắn vì lúc đã remove được low level abstraction code ra khỏi hàm `verifyUser()`.

Refactoring không chỉ diễn ra ở level function, mà ở các level cao hơn như refactor class hay module, thậm chí architecture. Việc quan trọng là cần đảm bảo refactor từ từ, từng bước nhỏ, và tại bất kỳ bước refactor nào thì cũng không được để phần mềm bị failed vì:

> If someone says their code was broken for a couple of days while the are refactoring, you can be pretty sure they were not refactoring. - *"Refactoring - Improving the Design of Existing Code"*

### Why refactoring? And When?

Như đề cập ở trên, refactoring giúp code dễ hiểu, cũng như dễ thích nghi với sự thay đổi của phần mềm hơn. Với việc code dễ hiểu hơn, developer cũng tăng cơ hội tìm ra các bug tồn tại trong phần mềm, giúp tăng chất lượng chung của phần mềm lên. Ngoài ra, thông qua continuous refactoring, giống như tập thể dục thường xuyên vậy, chúng ta có thể đảm bảo luôn giữ cho phần mềm được "khỏe mạnh", giúp quá trình phát triển phần mềm thuận lợi và đạt tốc độ tốt hơn.

Vậy tại sao chúng ta không viết code dễ hiểu ngay từ đầu mà cần phải refactor? Lý do chính cho việc này đó là tính năng của phần mềm sẽ thay đổi, và thay đổi thường xuyên. Điều này dẫn tới việc rất khó để tạo ra 1 thiết kế tốt ngày từ đầu (và "big design up front" thường được xem là không nên, vì dựa nhiều trên các giả định có thể sẽ không xảy ra, làm phức tạp hóa phần mềm 1 cách không cần thiết). Cho nên, mình nói refactoring là kỹ năng thiết yếu, vì nếu làm tốt, chúng ta có thể đưa ra 1 simple solution dựa trên yêu cầu và thông tin có được lúc đó, và tự tin rằng mình có thể gradually improve the solution when needed.

Vậy cụ thể thì chúng ta sẽ refactor khi nào?

Đầu tiên, khi thêm / chỉnh sửa tính năng phần mềm, nếu ta cảm thấy không dễ dàng để làm điều này, đó là lúc cần phải refactor lại một chút, để việc thêm tính năng dễ dàng hơn.

Thứ 2, đó là khi fix bug. Nếu có bug xuất hiện, đó có thể là dấu hiệu cho thấy đoạn code của phần mềm không thực sự dễ hiểu, làm cho chúng ta không dễ thấy bug. Điều cần làm lúc này là sau khi fix bug, hay refactor lại đoạn code liên quan để tránh các trường hợp tương tự xảy ra.

Thứ 3, khi viết thêm code vào phần mềm, sẽ có lúc chúng ta thấy rằng những đoạn code bị lặp đi lặp lại, copy / paste từ chỗ này qua chỗ khác. Đó cũng là một dấu hiệu cho thấy chúng ta cần refactor, ví dụ như tách các đoạn code trùng lặp thành một hàm / module riêng và tái sử dụng lại sau này.

Cuối cùng, continuous refactoring, với một tinh thần của người nghệ nhân phần mềm, sự tận tâm đối với sản phẩm chính tay mình tạo ra, hãy làm cho code tốt lên từng ngày, từng giờ, từng phút một.

> When programming, follow the camping rule: Always leave the code base healthier than when you found it. - *"Refactoring - Improving the Design of Existing Code"*