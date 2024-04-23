### 版本说明
`guzzle/guzzle` 是 GuzzleHttp 第 3 版的一个较早的版本，它是在 GuzzleHttp 之前最受欢迎的版本之一，但它已经不再维护。
`guzzlehttp/guzzle` 是 GuzzleHttp 第 6 版的一个分支，它是目前推荐使用的 GuzzleHttp 版本。它也提供了更好的 PSR 支持和更好的异步请求处理方式


### 要求
- PHP >= 7.2.5
- 如果使用stream,必须开启allow_url_fopen
- 如果使用curl,curl扩展版本必须>= 7.19.4

### 安装
```
composer require guzzlehttp/guzzle:^7.0
```

### 快速启动
```php
require_once 'vendor/autoload.php';
use GuzzleHttp\Client;
$client = new Client([
	'base_uri' => 'http://httpbin.org',
    'timeout' => 2.0, //超时时间2秒
]);
// 发送请求 http://httpbin.org/test
$response = $client->request('GET', 'test');
// 发送请求 http://httpbin.org/root
$response = $client->request('GET', '/root');
```
```php
//也可以这样发起请求
$response = $client->get('http://httpbin.org/get');
$response = $client->delete('http://httpbin.org/delete');
$response = $client->head('http://httpbin.org/get');
$response = $client->options('http://httpbin.org/get');
$response = $client->patch('http://httpbin.org/patch');
$response = $client->post('http://httpbin.org/post');
$response = $client->put('http://httpbin.org/put');
```
```
//可以先实例化Request,再发起请求
use GuzzleHttp\Psr7\Request;
$request = new Request('PUT', 'http://httpbin.org/put');
$response = $client->send($request, ['timeout' => 2]);
```
#### 发送异步请求
```php
$promise = $client->getAsync('http://httpbin.org/get');
$promise = $client->deleteAsync('http://httpbin.org/delete');
$promise = $client->headAsync('http://httpbin.org/get');
$promise = $client->optionsAsync('http://httpbin.org/get');
$promise = $client->patchAsync('http://httpbin.org/patch');
$promise = $client->postAsync('http://httpbin.org/post');
$promise = $client->putAsync('http://httpbin.org/put');
```

```php
use Psr\Http\Message\ResponseInterface;
use GuzzleHttp\Exception\RequestException;

$promise = $client->requestAsync('GET', 'http://httpbin.org/get');
$promise->then(
    function (ResponseInterface $res) {
        echo $res->getStatusCode() . "\n";
    },
    function (RequestException $e) {
        echo $e->getMessage() . "\n";
        echo $e->getRequest()->getMethod();
    }
)->wait();
```

### 并发发送请求
```php
use GuzzleHttp\Client;
use GuzzleHttp\Promise;

$client = new Client(['base_uri' => 'http://httpbin.org/']);
// 不阻塞发起每个请求
$promises = [
    'image' => $client->getAsync('/image'),
    'png'   => $client->getAsync('/image/png'),
    'jpeg'  => $client->getAsync('/image/jpeg'),
    'webp'  => $client->getAsync('/image/webp')
];
//等待全部请求返回,如果其中一个请求失败会抛出异常
$responses = Promise\Utils::unwrap($promises);
//等待全部请求返回,允许某些请求失败
$responses = Promise\Utils::settle($promises)->wait();
```

### 使用Response
```
$code = $response->getStatusCode(); // 200
$reason = $response->getReasonPhrase(); // OK
//header是否存在Content-Length
if ($response->hasHeader('Content-Length')) {
    echo "It exists";
}

//获取Content-Length的值
echo $response->getHeader('Content-Length')[0];
//全部header
foreach ($response->getHeaders() as $name => $values) {
    echo $name . ': ' . implode(', ', $values) . "\r\n";
}

//getBody返回的是实现了StreamInterface接口的流对象
$body = $response->getBody();
//StreamInterface实现了__toString()方法,可以直接输出
echo $body;
//显示转换成字符串
$stringBody = (string) $body;
//当作流一样使用,使用read方法读取
$tenBytes = $body->read(10);
//读取剩余内容
$remainingBytes = $body->getContents();
```

### query参数的几种写法
```
//1
$response = $client->request('GET', 'http://httpbin.org?foo=bar');
//2 会自动使用http_build_query 
$client->request('GET', 'http://httpbin.org', [
    'query' => ['foo' => 'bar']
]);
//3
$client->request('GET', 'http://httpbin.org', ['query' => 'foo=bar']);
```

### 上传数据
```
//$body是字符串数据
$r = $client->request('POST', 'http://httpbin.org/post', [
    'body' => 'raw data'
]);

// $body是文件句柄
$body = Psr7\Utils::tryFopen('/path/to/file', 'r');
$r = $client->request('POST', 'http://httpbin.org/post', ['body' => $body]);

// body是PSR-7 stream流.
$body = Psr7\Utils::streamFor('hello!');
$r = $client->request('POST', 'http://httpbin.org/post', ['body' => $body]);
```

### 表单上传
```
// application/x-www-form-urlencoded
$response = $client->request('POST', 'http://httpbin.org/post', [
    'form_params' => [
        'field_name' => 'abc',
        'other_field' => '123',
        'nested_field' => [
            'nested' => 'hello'
        ]
    ]
]);

//multipart/form-data
use GuzzleHttp\Psr7;

$response = $client->request('POST', 'http://httpbin.org/post', [
    'multipart' => [
        [
            'name'     => 'field_name',//表单name
            'contents' => 'abc'//表单值
        ],
        [
            'name'     => 'file_name',//文件名
            'contents' => Psr7\Utils::tryFopen('/path/to/file', 'r')//文件句柄
        ],
        [
            'name'     => 'other_file',//没太看懂,前面两种够用了
            'contents' => 'hello',
            'filename' => 'filename.txt',
            'headers'  => [
                'X-Foo' => 'this is an extra header to include'
            ]
        ]
    ]
]);
```

### Cookies
```
//组装cookie
$jar = \GuzzleHttp\Cookie\CookieJar::fromArray(
    [
        'some_cookie' => 'foo',
        'other_cookie' => 'barbaz1234'
    ],
    'example.org' //域名
);

//带cookies发起请求
$jar = new \GuzzleHttp\Cookie\CookieJar;
$r = $client->request('GET', 'http://httpbin.org/cookies', [
    'cookies' => $jar
]);

//client共享cookie
$client = new \GuzzleHttp\Client(['cookies' => true]);
$r = $client->request('GET', 'http://httpbin.org/cookies');
```

### 异常树
```
. \RuntimeException
└── TransferException (implements GuzzleException)
    ├── ConnectException (implements NetworkExceptionInterface) //网络错误
    └── RequestException
        ├── BadResponseException
        │   ├── ServerException //500错误
        │   └── ClientException //400错误
        └── TooManyRedirectsException //太多重定向
```

### 常用的请求
```
//BASIC 授权
$client->request('GET', '/get', ['auth' => ['username', 'password']]);

//DIGEST授权
$client->request('GET', '/get', [
    'auth' => ['username', 'password', 'digest']
]);

//连接超时时间,0为无限期
$client->request('GET', '/delay/5', ['connect_timeout' => 3.14]);

//设置header
// Set various headers on a request
$client->request('GET', '/get', [
    'headers' => [
        'User-Agent' => 'testing/1.0',
        'Accept'     => 'application/json',
        'X-Foo'      => ['Bar', 'Baz']
    ]
]);

//请求超时
$client->request('GET', '/delay/5', ['timeout' => 3.14]);
```

### 总结
再见自己写的curl函数,拥抱guzzlehttp,还支持异步请求,推荐指数:五老星