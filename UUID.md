
# OpenUDID 和 IDFA 比较  

[OpenUDID](https://github.com/ylechelle/OpenUDID)
  
广告标识符（IDFA-identifierForIdentifier），是AdSupport.framework里面的。可以通过ASIdentifierManager进行获得。  
```
// 广告标识符
NSString *idFA = [[[ASIdentifierManager sharedManager] advertisingIdentifier] UUIDString];
```
在iOS中可以通过-设置-隐私-广告-还原广告标识符，手机无需重启，即可重置IDFA的值。  

![Markdown](http://i2.kiimg.com/1949/6b4e94d129fab226.png)

## KeyChain变化场景  
1. 程序删除，系统升级安装后依然可以获取之前的数据。
2. iOS中，就算还原广告标识，也可以获取到以前的数据。
3. 只有操作-设置 - 通用 - 还原 - 抹掉所有内容和设置，数据便获取不了了。
