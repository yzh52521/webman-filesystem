#推荐使用插件 https://github.com/yzh52521/filesystem

# 安装

```
composer require yzh52521/webman-filesystem
```

在config目录下新建filesystem.php并将下列内容导入

```
return [
    'default' => 'local',
    'storage' => [
        'local' => [
            'driver' => \yzh52521\Filesystem\Adapter\LocalAdapter::class,
            'root' => public_path(),
        ],
       
        'memory' => [
            'driver' => \yzh52521\Filesystem\Adapter\MemoryAdapter::class,
        ],
        's3' => [
            'driver' => \yzh52521\Filesystem\Adapter\S3Adapter::class,
            'credentials' => [
                'key' => 'S3_KEY',
                'secret' => 'S3_SECRET',
            ],
            'region' => 'S3_REGION',
            'version' => 'latest',
            'bucket_endpoint' => false,
            'use_path_style_endpoint' => false,
            'endpoint' => 'S3_ENDPOINT',
            'bucket_name' => 'S3_BUCKET',
        ],
      
        'oss' => [
            'driver' => \yzh52521\Filesystem\Adapter\AliyunAdapter::class,
            'accessId' => 'OSS_ACCESS_ID',
            'accessSecret' => 'OSS_ACCESS_SECRET',
            'bucket' => 'OSS_BUCKET',
            'endpoint' => 'OSS_ENDPOINT',
            // 'timeout' => 3600,
            // 'connectTimeout' => 10,
            // 'isCName' => false,
            // 'token' => null,
            // 'proxy' => null,
        ],
        'qiniu' => [
            'driver' => \yzh52521\Filesystem\Adapter\QiniuAdapter::class,
            'accessKey' => 'QINIU_ACCESS_KEY',
            'secretKey' => 'QINIU_SECRET_KEY',
            'bucket' => 'QINIU_BUCKET',
            'domain' => 'QINBIU_DOMAIN',
        ],
        'cos' => [
            'driver' => \yzh52521\Filesystem\Adapter\CosAdapter::class,
            'region' => 'COS_REGION',
            'app_id' => 'COS_APPID',
            'secret_id' => 'COS_SECRET_ID',
            'secret_key' => 'COS_SECRET_KEY',
            // 可选，如果 bucket 为私有访问请打开此项
            // 'signed_url' => false,
            'bucket' => 'COS_BUCKET',
            'read_from_cdn' => false,
            // 'timeout' => 60,
            // 'connect_timeout' => 60,
            // 'cdn' => '',
            // 'scheme' => 'https',
        ],
        'obs'    => [
            'driver'     => \yzh52521\Filesystem\Adapter\ObsAdapter::class,
            'key'        => 'OBS_ACCESS_ID',
            // <Your Huawei OBS AccessKeyId>
            'secret'     => 'OBS_ACCESS_KEY',
            // <Your Huawei OBS AccessKeySecret>
            'bucket'     => 'OBS_BUCKET',
            // <OBS bucket name>
            'endpoint'   => 'OBS_ENDPOINT',
            // <the endpoint of OBS, E.g: (https:// or http://).obs.cn-east-2.myhuaweicloud.com | custom domain, E.g:img.abc.com> OBS 外网节点或自定义外部域名
            'cdn_domain' => 'OBS_CDN_DOMAIN',
            //<CDN domain, cdn域名> 如果isCName为true, getUrl会判断cdnDomain是否设定来决定返回的url，如果cdnDomain未设置，则使用endpoint来生成url，否则使用cdn
            'ssl_verify' => 'OBS_SSL_VERIFY',
            // <true|false> true to use 'https://' and false to use 'http://'. default is false,
            'debug'      => 'APP_DEBUG',
            // <true|false>
        ],
    ],
];
```


- 阿里云 OSS 适配器

```
composer require "yzh52521/flysystem-oss:^2.0"
```
- S3 适配器

```
composer require "league/flysystem-aws-s3-v3:^2.0"
```
- 七牛云适配器

```
composer require "overtrue/flysystem-qiniu:^2.0"
```
- 内存适配器

```
composer require "league/flysystem-memory:^2.0"
```
- 腾讯云 COS 适配器

```
composer require "overtrue/flysystem-cos:^4.0"
```

- 华为云 OBS 适配器

```
composer require "yzh52521/flysystem-obs:^2.0"
```
# 使用

通过Filesystem::storage('local') 来调用不同的适配器

```
    use yzh52521\Filesystem\Filesystem;
    public function upload(Request $request)
    {
        $file = $request->file('file');
        $stream = fopen($file->getRealPath(), 'r+');
        $path='uploads/'.$file->getUploadName();
        Filesystem::writeStream(
            $path,
            $stream
        );
        fclose($stream);
        return Filesystem::getStorageConfig('local','url').$path;
        
        // Write Files
        $filesystem->write('path/to/file.txt', 'contents');
        // Add local file
        $stream = fopen('local/path/to/file.txt', 'r+');
        $result = $filesystem->writeStream('path/to/file.txt', $stream);
        if (is_resource($stream)) {
            fclose($stream);
        }
        // Update Files
        $filesystem->update('path/to/file.txt', 'new contents');
        // Check if a file exists
        $exists = $filesystem->has('path/to/file.txt');
        // Read Files
        $contents = $filesystem->read('path/to/file.txt');
        // Delete Files
        $filesystem->delete('path/to/file.txt');
        // Rename Files
        $filesystem->rename('filename.txt', 'newname.txt');
        // Copy Files
        $filesystem->copy('filename.txt', 'duplicate.txt');
        // list the contents
        $filesystem->listContents('path', false);
    }
```
