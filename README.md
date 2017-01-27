# morgan

[![NPM Version][npm-image]][npm-url]
[![NPM Downloads][downloads-image]][downloads-url]
[![Build Status][travis-image]][travis-url]
[![Test Coverage][coveralls-image]][coveralls-url]
[![Gratipay][gratipay-image]][gratipay-url]

HTTP request logger middleware for node.js

Một HTTP request xuất ra sử dụng cho nodejs

> Named after [Dexter](http://en.wikipedia.org/wiki/Dexter_Morgan), a show you should not watch until completion.

## API

```js
var morgan = require('morgan')
```

### morgan(format, options)

Create a new morgan logger middleware function sử dụng the thiết lập `format` and `options`.
The `format` đối số của nó là một chuỗi 1 tên được định nghĩa trước  (xem phía dưới của các tên ),
a string of a format string, or a function that will produce a log entry.

một chuỗi của một định dạng string, hoặc một function mà sẽ tạo ra mộ log tài liệu

#### Options "Tùy Chọn"

Morgan accepts chấp nhận các thuộc tính trong object các opyions

##### immediate

Ghi một dòng trên file log reqeust thay thế cho phản ứng 
Write log line on request instead of response. 
Điều này có nghĩa rằng một yêu cầu sẽ được đăng nhập ngay cả khi máy chủ bị treo, nhưng data từ các response (giống như các
response code, content length, etc.)  không thể được logged_. bởi nó chỉ làm việc với request để log request.

##### skip
 
Function để chỉ rõ nếu ghi chép lỗi là được bỏ qua, mặc định là không để `false`. Function này với cuộc gọi giống như 
This function  `skip(req, res)`.

```js
// ví dụ : chỉ rõ lỗi xuất ra lỗi error cho phản ứng responses

morgan('combined', {
  skip: function (req, res) { return res.statusCode < 400 }
})
```

##### stream

Ghi chép dòng vào log của đầu ra stream. Mặc định để `process.stdout`.

Output stream for writing log lines, defaults to `process.stdout`.

#### Predefined Formats  "Format là đối số 1"

Có rất nhiều định dạng được xác định trước được cung cấp:

##### combined

Standard Apache combined log output.

áp dụng chuẩn cho quá trình biên dịch output
```
:remote-addr - :remote-user [:date[clf]] ":method :url HTTP/:http-version" :status :res[content-length] ":referrer" ":user-agent"
```

ví dụ :

var morgan = require("morgan");

app.use(morgan("short"));

app.get("/",function(req,res){

    console.log("hello morgan");
    res.end();

});


url trong cmd sẽ hiển thị đây đủ như :



C:\Users\IT\Desktop\testMorgan>node index.js
hello morgan
::1 - - [27/Jan/2017:03:19:00 +0000] "GET / HTTP/1.1" 200 - "-" "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) coc_coc_browser/60.4.136 Chrome/54.4.2840.136 Safari/537.36"


##### common

Standard Apache common log output.

áp dụng chuẩn cho apache common log đầu ra.

```
:remote-addr - :remote-user [:date[clf]] ":method :url HTTP/:http-version" :status :res[content-length]
```

ví dụ 

C:\Users\IT\Desktop\testMorgan>node index.js
hello morgan
::1 - - [27/Jan/2017:03:21:05 +0000] "GET / HTTP/1.1" 200 

##### dev

Concise output colored by response status for development use. The `:status`
token will be colored red for server error codes, yellow for client error
codes, cyan for redirection codes, and uncolored for all other codes.

```
:method :url :status :response-time ms - :res[content-length]
```

##### short


Shorter than default, also including response time.

```
:remote-addr :remote-user :method :url HTTP/:http-version :status :res[content-length] - :response-time ms
```

ví dụ :



C:\Users\IT\Desktop\testMorgan>node index.js
::1 - GET / HTTP/1.1 200 - - 4.574 ms


##### tiny

The minimal output.

```
:method :url :status :res[content-length] - :response-time ms
```

#### Tokens

##### Creating new tokens

with the name and a callback function. This callback function is expected to return a string value. The value returned is then available as ":type" in this case:

Để định nghĩa 1 token đơn giản sử dụng invoke  `morgan.token()` với các tên và một chức năng gọi lại callback function là một ngoại lệ để trả về một chuỗi giá trị.

Các giá trị trả về các biến trong trường hợp này.


```js
morgan.token('type', function (req, res) { return req.headers['content-type'] })
```

Calling `morgan.token()` using the same name as an existing token will overwrite that token definition.

##### :date[format]

Xuất ra ngày giờ hiện tại theo utc. :date["tham số format"] sau

  - `clf` cho các truy cập xuất định dạng thông thường (`"10/Oct/2000:13:55:36 +0000"`)
  - `iso` sử dụng cho các ISO 8601 định dạng theo ngày tháng (`2000-10-10T13:55:36.000Z`)
  - `web` sử dụng cho các   RFC 1123 định dạng ngày tháng (`Tue, 10 Oct 2000 13:55:36 GMT`)

Nếu không có định dạng được đưa ra, sau đó mặc định là `web`.

##### :http-version

Trả về các HTTP version của request 

##### :method

Trả về Http method của request

##### :referrer

The Referrer header of the request. This will use the standard mis-spelled Referer header if exists, otherwise Referrer.

Trả về tham khảo đến header của request. Điều này sử dụng với chuẩn mis-spelled Referer header nếu có tồn tại. Nếu không thì cách khác được tham chiếu sử dụng.


##### :remote-addr

The remote address of the request. This will use `req.ip`, otherwise the standard `req.connection.remoteAddress` value (socket address).

##### :remote-user

The user authenticated as part of Basic auth for the request.

Trả về người dùng xác thực sử dụng với đường dẫn của basic auth của các request

##### :req[header]

The given `header` of the request.

Trả về các qui định của hread request

##### :res[header]

The given `header` of the response.

Trả về các qui định `header` của phản ứng


##### :response-time[digits]

The time between the request coming into `morgan` and when the response
headers are written, in milliseconds.


Thời gian giữa các yêu cầu đến thành 'morgan` và khi các phản ứng
tiêu đề được viết, trong mili giây

The `digits` argument is a number that specifies the number of digits to
include on the number, defaulting to `3`, which provides microsecond precision.

Các `digist` là một số quy định cụ thể số lượng các chữ số
bao gồm về số lượng, mặc định là `3`, cung cấp micro có độ chính xác.

##### :status

Trả về status của code bởi phản ứng

The status code of the response.

If the request/response cycle completes before a response was sent to the
client (for example, the TCP socket closed prematurely by a client aborting
the request), then the status will be empty (displayed as `"-"` in the log).


Nếu chu kỳ yêu cầu / đáp ứng hoàn tất trước khi một phản ứng đã được gửi đến
khách hàng (ví dụ, các socket TCP đóng sớm bởi một khách hàng hủy bỏ
theo yêu cầu), sau đó tình trạng sẽ trống (hiển thị như `" - "` trong nhật ký).

##### :url

The URL of the request. This will use `req.originalUrl` if exists, otherwise `req.url`.

Trả về các URL được yêu cầu.  Điều này sẽ sử dụng `req.originalUrl` nếu tồn tại, nếu không 'req.url`.

##### :user-agent

Trả về các nội dung  User-Agent header của các request.

### morgan.compile(format)

Compile a format string into a function for use by `morgan`. A format string
is a string that represents a single log line and can utilize token syntax.
Tokens are references by `:token-name`. If tokens accept arguments, they can
be passed using `[]`, for example: `:token-name[pretty]` would pass the string
`'pretty'` as an argument to the token `token-name`.

Normally formats are defined using `morgan.format(name, format)`, but for certain
advanced uses, this compile function is directly available.

Sử dụng một chuỗi biên dịch thông tin cho 1 function sử dụng bởi morgan.

1 định dạng chuỗi là một chuỗi thay mặt chỉ 1 bản ghi log và có thể sử dụng cú pháp token.

Token là một tham chiếu đến `:token-name`. Nếu token được áp dụng cho đối số họ cần sử dụng phân tích `[]`. Cho ví dụ `:token-name[pretty]` sẽ vượt qua chuỗi như một đối số token. `token-name`.


các định dạng thông thường được định nghĩa sử dụng `morgan.format(name, format)`, nhưng đối với một số
sử dụng nâng cao, chức năng biên dịch này là trực tiếp có sẵn.

## Examples "Ví dụ"

### express/connect "Express / kết nối"

Một app đơn giản sử dụng với xuất all request trong apache biên dịch bởi định dạng STDOUT.

Simple app that will log all request in the Apache combined format to STDOUT

```js
var express = require('express')
var morgan = require('morgan')

var app = express()

app.use(morgan('combined'))

app.get('/', function (req, res) {
  res.send('hello, world!')
})
```

### vanilla http server

Một ứng dụng đơn giản sử udngj với tất cả các yêu cầu request all tới apache sử dụng định dạng STD OUT


```js
var finalhandler = require('finalhandler')
var http = require('http')
var morgan = require('morgan')

// create "middleware"
var logger = morgan('combined')

http.createServer(function (req, res) {
  var done = finalhandler(req, res)
  logger(req, res, function (err) {
    if (err) return done(err)

    // respond to request
    res.setHeader('content-type', 'text/plain')
    res.end('hello, world!')
  })
})
```

### write logs to a file " Viết log vào 1 file"

#### single file

Simple app that will log all requests in the Apache combined format to the file

Ứng dụng đơn giản sẽ ghi lại tất cả các yêu cầu trong các định dạng kết hợp Apache đến tập tin

`access.log`.

```js
var express = require('express')
var fs = require('fs')
var morgan = require('morgan')
var path = require('path')

var app = express()

// create a write stream (in append mode)
// tạo ra một dòng ghi (trong chế độ phụ thêm)

var accessLogStream = fs.createWriteStream(path.join(__dirname, 'access.log'), {flags: 'a'})

// setup the logger
//  thiết lập các logger

app.use(morgan('combined', {stream: accessLogStream}))

app.get('/', function (req, res) {
  res.send('hello, world!')
})
```

#### log file rotation

Simple app that will log all requests in the Apache combined format to one log
file per date in the `log/` directory using the

[file-stream-rotator module](https://www.npmjs.com/package/file-stream-rotator).

Một ứng dụng đơn giản mà ghi lại các reuqest đăng nhập mỗi ngày trong 1 file thực hiện trong thư mục `log/` sử dụng các

Nên dùng cách này.

sẽ được kết quả trong file log ghi phân tích cách request đến :

::1 - - [27/Jan/2017:08:01:36 +0000] "GET / HTTP/1.1" 200 13 "-" "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) coc_coc_browser/60.4.136 Chrome/54.4.2840.136 Safari/537.36"
::1 - - [27/Jan/2017:08:04:56 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) coc_coc_browser/60.4.136 Chrome/54.4.2840.136 Safari/537.36"
::1 - - [27/Jan/2017:08:05:01 +0000] "GET /use HTTP/1.1" 404 16 "-" "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) coc_coc_browser/60.4.136 Chrome/54.4.2840.136 Safari/537.36"
::1 - - [27/Jan/2017:08:05:07 +0000] "GET /user HTTP/1.1" 200 17 "-" "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) coc_coc_browser/60.4.136 Chrome/54.4.2840.136 Safari/537.36"


[file-stream-rotator module](https://www.npmjs.com/package/file-stream-rotator).


```js
var FileStreamRotator = require('file-stream-rotator')
var express = require('express')
var fs = require('fs')
var morgan = require('morgan')
var path = require('path')

var app = express()
var logDirectory = path.join(__dirname, 'log')

// ensure log directory exists Đảm bảo thư mục log tồn tại 
fs.existsSync(logDirectory) || fs.mkdirSync(logDirectory)

// create a rotating write stream
var accessLogStream = FileStreamRotator.getStream({
  date_format: 'YYYYMMDD',
  filename: path.join(logDirectory, 'access-%DATE%.log'),
  frequency: 'daily',
  verbose: false
})

// setup the logger
app.use(morgan('combined', {stream: accessLogStream}))

app.get('/', function (req, res) {
  res.send('hello, world!')
})
```

### use custom token formats

Sample app that will use custom token formats. This adds an ID to all requests and displays it using the `:id` token.

Ứng dụng mẫu mà sẽ sử dụng định dạng tùy chỉnh token. Điều này thêm một ID cho tất cả các yêu cầu và hiển thị nó bằng cách sử dụng `:id`.

```js
var express = require('express')
var morgan = require('morgan')
var uuid = require('node-uuid')

morgan.token('id', function getId (req) {
  return req.id
})

var app = express()

app.use(assignId)
app.use(morgan(':id :method :url :response-time'))

app.get('/', function (req, res) {
  res.send('hello, world!')
})

function assignId (req, res, next) {
  req.id = uuid.v4()
  next()
}
```

## License

[MIT](LICENSE)

[npm-image]: https://img.shields.io/npm/v/morgan.svg
[npm-url]: https://npmjs.org/package/morgan
[travis-image]: https://img.shields.io/travis/expressjs/morgan/master.svg
[travis-url]: https://travis-ci.org/expressjs/morgan
[coveralls-image]: https://img.shields.io/coveralls/expressjs/morgan/master.svg
[coveralls-url]: https://coveralls.io/r/expressjs/morgan?branch=master
[downloads-image]: https://img.shields.io/npm/dm/morgan.svg
[downloads-url]: https://npmjs.org/package/morgan
[gratipay-image]: https://img.shields.io/gratipay/dougwilson.svg
[gratipay-url]: https://www.gratipay.com/dougwilson/
