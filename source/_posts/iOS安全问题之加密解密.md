title: iOS安全问题之加密解密
date: 2016-02-5 9:48:13
tags: [iOS,RSA,AES,MD5,SQLCipher,加密解密]
---


### 一、应用场景
  越来越多的应用和金钱都要打交道，用户隐私安全的保护也变得空前的重要了，互联网金融应用、电商应用支付、网络存储、数据网络传输……都需要对数据进行保护。

  <!-- more -->
### 二、加密解密解决方案 
  
  既然数据安全如此重要，那么解决思路呢？
  1、对数据内容加密（通常在传输过程中）
  2、对数据文件进行加密（通常用于本地数据库文件）

### 三、SQLCipher加密数据库文件
  SQLCipher是完全开源的，代码可以在github上面找到。
  我们可以通过Pod引进sqlcipher依赖
  ``` bash
   pod 'FMDB', '~> 2.6'
   pod 'SQLCipher', '~> 3.3.1'
  ```
  然后，在项目中添加相应的头文件，重写- (BOOL)open 方法：
  ``` bash
   - (BOOL)open {
    if (_db) {
        return YES;
    }
    
    int err = sqlite3_open([self sqlitePath], &_db );
    if(err != SQLITE_OK) {
        NSLog(@"error opening!: %d", err);
        return NO;
    } else{
        //数据库open后设置加密key
        [self setKey:@"abc123"];
    }
    
    if (_maxBusyRetryTimeInterval > 0.0) {
        // set the handler
        [self setMaxBusyRetryTimeInterval:_maxBusyRetryTimeInterval];
    }
    
    
    return YES;
   }
  ```
  好啦，至此，数据库文件加密已经完成！

### 四、对数据内容加密
  1、MD5系列不可逆加密
  2、DES对称加密
  3、AES对称加密
  4、RSA非对称加密
  5、ECC椭圆形加密
  这5种方法加密安全系数从上到下依次递增。


<!--   ``` bash
  $hexo deploy
  ```
  More info: [Deployment](https://hexo.io/docs/deployment.html)
 

  ``` bash
  $npm install hexo-deployer-git --save
  ``` 
 

  ``` bash
  $hexo server
  ```

  ![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)!
 -->


