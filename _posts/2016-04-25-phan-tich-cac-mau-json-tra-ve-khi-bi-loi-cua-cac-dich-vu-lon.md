---
layout: post
title: "Phân tích các mẫu JSON trả về khi bị lỗi của các dịch vụ lớn"
description: ""
category: API
tags: [json, errors message]
comments: true
---

Khi viết REST API, một trong những vấn đề cần phải giải quyết triệt để và thống nhất là cách trả về JSON khi xảy ra lỗi.

Mỗi công ty, mỗi service sẽ có những cách xử lý khác nhau, và có lẽ không có cách làm nào là hay nhất hoặc chính xác nhất.

Bài viết này sẽ phân tích cách làm của 15 services lớn trên thế giới.

<!-- more -->

## Các services được phân tích
Các web API dưới đây là những trang web rất phổ biến đối với các lập trình viên.
Việc sắp xếp thứ tự là ngẫu nhiên.

1. Github
2. Facebook
3. Heroku
4. Toggl
5. Yahoo!
6. GREE
7. Nike
8. Twitter
9. Google
10. Twillio
11. Foursquare
12. Flickr
13. Linkedin
14. Philips Hue
15. Qiita

### Github

[Github Developer](https://developer.github.com/v3/#client-errors)

```json
{
   "message": "Validation Failed",
   "errors": [
     {
       "resource": "Issue",
       "field": "title",
       "code": "missing_field"
     }
   ]
 }
```

* `message`: nội dung errors
* `errors`:  trong trường hợp các fields trong request có lỗi thì phần này mới được thêm vào.
    * `resource`: resource đối tượng
    * `field`: field có lỗi
    * `code`: code lỗi

Những lỗi cơ bản thì chỉ cần `message` là đủ.  Trong trường hợp cần phải trả về nhiều lỗi thì cách viết bằng `errors` này cũng có thể đáp ứng được.

### Facebook

[Using the Graph API](https://developers.facebook.com/docs/graph-api/using-graph-api/v2.3#errors)

```json
     {
       "error": {
         "message": "Message describing the error",
         "type": "OAuthException",
         "code": 190,
         "error_subcode": 460,
         "error_user_title": "A title",
         "error_user_msg": "A message"
       }
     }
```

* `message`: nội dung lỗi
* `type`: kiểu lỗi
* `code`: code lỗi
* `error_subcode`: subcode của lỗi
* `error_user_title`:  title lỗi sẽ hiển thị lên cho user, nội dung này đã được dịch.
* `error_user_msg`:  nội dung lỗi sẽ được hiển thị, đã được dịch

Đặc trưng của Facebook là errors hướng tới người sử dụng. Vì thế mà đã được dịch, được xử lý trên server. Đối với người viết app thì cách làm này sẽ giúp công việc đơn giản hơn rất nhiều.

### Heroku

[Platform API Reference | Heroku Dev Center](https://devcenter.heroku.com/articles/platform-api-reference#errors)

```json
{
  "id":       "rate_limit",
  "message":  "Your account reached the API rate limit\nPlease wait a few minutes before making new requests",
  "url":      "https://devcenter.heroku.com/articles/platform-api-reference#rate-limits"
}
```

* `id`:  code lỗi
* `message`: nội dung
* `url`: links thông tin chi tiết về lỗi đó

Cách trả lại errors của Heroku cực kì đơn giản.

### Toggl

[toggl_api_docs/reports.md at master · toggl/toggl_api_docs](https://github.com/toggl/toggl_api_docs/blob/master/reports.md#failed-requests)

```json
  {
    "error": {
      "message":"We are sorry, this Error should never happen to you",
      "tip":"Please contact support@toggl.com with information on your request",
      "code":500
    }
  }
```

* `message`
* `tip`: cách xử lý lỗi này
* `code`: HTTP status code

Đặc trưng của toggl là trong errors có cả các tip, các hướng dẫn để xử lý lỗi.

### Yahoo!

```json
{
  "Error" : {
    "Message" : "error message"
  }
}
```

* `Message`

Chỉ có 1 từ, bá đạo T____T.

### GREE

[Error Codes - GREE Developer Center](https://docs.developer.gree.net/ja/globaltechnicalspecs/api/errorcodes)

```json
{
   "code":1001,
   "message":"Message API (batch type) to same user is called
             several times by official user in a certain period of time. Service unavailable.",
   "ref_url":"http:\/\/docs.developer.gree.net\/error.html"
}
```

* `code`
* `message`
* `ref_url`: link tham khảo về lỗi

Errors của GREE cũng rất đơn giản và giống như Heroku khi có link để tham khảo về lỗi đó.

### Nike

[API Error Codes](https://developer.nike.com/documentation/api-docs/error-messages/error-codes-and-responses.html)

```json
{
    "requestId": "-1712857370761229397",
    "errors": [
        {
            "code": 90,
            "message": "Invalid format: Start Date must follow format yyyy-mm-dd"
        },
        {
            "code": 100,
            "message": "Invalid format: End Date must follow format yyyy-mm-dd"
        },
        {
            "code": 110,
            "message": "Invalid Format: count must be greater than or equal to 1"
        }
    ]
}
```

* `requestId`
* `code`
* `message`

Với Nike thì request ID yêu cầu user phải có API token, lỗi sẽ được trả về theo dạng array.

### Twitter

[Error Codes & Responses | Twitter Developers](https://dev.twitter.com/overview/api/response-codes)

```json
{
  "errors": [
    {
      "message": "Sorry, that page does not exist",
      "code": 34
    }
  ]
}
```

* `message`
* `code`

Lỗi API của Twitter có đặc trưng là trả về theo dạng array.

### Google
Ông lớn trong những ông lớn.
[Standard Error Responses - DoubleClick Search API — Google Developers](https://developers.google.com/doubleclick-search/v2/standard-error-responses)

```json
{
  "error": {
    "errors": [
      {
        "domain": "global",
        "reason": "appNotConfigured",
        "message": "The app with id {appId} does not exist or is not properly configured as a Google Drive app."
      }
    ],
    "code": 403,
    "message": "The app with id {appId} does not exist or is not properly configured as a Google Drive app."
  }
}
```

* `domain`: Vì google có rất nhiều ứng dụng, nên key này dùng để xác định xem ứng dụng nào bị lỗi. Ví dụ, nếu YouTube bị lỗi thì key sẽ có giá trị là `youtube.parameter`.
* `reason`: error code
* `message`
* `code`: HTTP status code

Đặc trưng của google là họ có rất nhiều service và cùng thống nhất 1 định dạng JSON, do đó họ cần thêm key `domain` để xác định xem lỗi ở service nào.
Ngoài ra, để trả lại nhiều errors cùng lúc thì key `errors` được trả vê ở dạng array.

### Twillio

[Twilio Cloud Communications - APIs for Voice, VoIP, and Text Messaging](https://www.twilio.com/docs/api/rest/response#response-formats-exceptions)

```json
{
    "status": 400,
    "message": "No to number is specified",
    "code": 21201,
    "more_info": "http:\/\/www.twilio.com\/docs\/errors\/21201"
}
```

* `status`: HTTP status code
* `message`
* `code`
* `more_info`: link tới các thông tin bổ xung của errors đó.

Cũng có trường hợp `code`, `more_info` trống.

### Foursquare

[Responses & Errors](https://developer.foursquare.com/overview/responses)

```json
{
    "meta": {
        "code": 400,
        "errorDetail": "Missing access credentials. See https://developer.foursquare.com/docs/oauth.html for details.",
        "errorType": "invalid_auth"
    },
    "response": {}
}
```

* `code`: HTTP status code
* `errorDetail`: errors message
* `errorType`: error code

Đặc trưng của Foursquare là response khi lỗi và response bình thường không khác nhau. Khi xảy ra lỗi thì nội dung errors sẽ được đẩy vào key `meta`.

### Flickr

[Flickr Services](https://www.flickr.com/services/api/response.json.html)

```json
{
    "code": 96,
    "message": "Invalid signature",
    "stat": "fail"
}
```

* `code`
* `message`
* `stat`: trả về request thành công hay thất bại.

Error của Flickr cũng rất đơn giản, chỉ khác biệt ở key `stat`.

### Linkedin

```json
{
    "errorCode": 0,
    "message": "Unknown authentication scheme",
    "requestId": "PFB5T8NVLO",
    "status": 401,
    "timestamp": 1432283172233
}
```

* `errorCode`:
* `message`
* `requestId`
* `status`: HTTP status
* `timestamp`: timestamp của request

Đặc trưng của LinkedIn là có requestID và timestamp của request. Khi cần phải phân tích lại request thì 2 key này sẽ rất tiện lợi.

### Philips Hue

[API Core concepts | Philips Hue API](http://www.developers.meethue.com/documentation/api-core-concepts#_Toc259440465)

```json
[
  {
    "error": {
      "type": 2,
      "address": "/",
      "description": "body contains invalid json"
    }
  }
]
```

* `type`
* `address`: link URL xảy ra error
* `description`: error message

### Qiita

```json
{
  "message": "Not found",
  "type": "not_found"
}
```

* `message`
* `type`

## Tổng kết

### So sánh nội dung của errors

Tổng hợp lại những services ở trên.

|  - | Message  | Error code  | Nhiều errors  | Status code  | URL chi tiết
|---|---|---|---|---|
| Github  |&#10003; | string |&#10003;   |   |
| Facebook |  &#10003; | string, int  |   |   | |
| Heroku  | &#10003;  |   |   |   |&#10003;| |
| Toggl | &#10003; | | |&#10003; | | |
| Yahoo! |&#10003; | | | | | |
| GREE |&#10003; | int| | | &#10003;| |
| Nike |&#10003; |int |&#10003; | | | |
| Twitter |&#10003; |int |&#10003; | | | |
| Google |&#10003; |string |&#10003; |&#10003; | | |
| Twillio |&#10003; |int | |&#10003; | &#10003;| |
| Foursquare |&#10003; |string | |&#10003; | | |
| Flickr|&#10003; |int | | | | |
| LinkedIn|&#10003; |int | |&#10003; | | |
| Philips Hue|&#10003; |int | | | | |
| Qiita |&#10003; |string | | | | |

Trong tất cả những services được phân tích này, errors đều có cấu trúc rất đơn giản và dễ hiểu cho con người, và tất cả đều chứa message để truyền đạt lỗi một cách chính xác nhất.

Error code là cách để giúp user dễ dàng phân tích và nắm bắt được lỗi gặp phải. Với những service này, thường sẽ có 1 danh sách các error code và giải thích đi kèm.

Tuỳ theo tính chất của services mà lựa chọn trả lại 1 hay nhiều errors. Ví dụ như với những app 1 lần cho phép nhập nhiều fields, thì việc trả về nhiều errors cũng dễ xử lý hơn.


### So sánh tên các fields

Để hiển thị message, hầu hết các services đều sử dụng key `message`. Error code thì nhiều nhất là `code`, ngoài ra còn có thể thêm `type`.
Phần lỗi nếu trả lại nhiều thì dùng `errors`, nếu không thì là `error`. Các link liên quan thì dùng `url`, `rel_url`, `more_info`.

## Những vấn đề phải suy nghĩ về errors response

Sau khi phân tích các services ở trên, ta có thể rút ra 1 số kết luận sau :

* **errors message**: chứa các message sao cho dev có thể hiểu được.
* **error code**: giúp dev dễ dàng kiểm soát và tra cứu errors hơn. Điều này thường đi kèm với 1 list công khai các errors.
* **nhiều errors**: Tuỳ theo bản chất của services, nhưng việc trả lại nhiều errors cùng lúc sẽ thuận tiện hơn cho dev.
* **URL**: Khi có 1 web công khai tài liệu về API thì có URL sẽ giảm thiểu thời gian cho dev.
* **tính thống nhất**: ở bất cứ endpoint nào cũng phải có cùng 1 cấu trúc JSON.
* **đơn giản**: cấu trúc JSON thường đơn giản, ít NEST. Như thế sẽ thuận lợi cho việc phân tích và xử lý errors.
