# RDPCrawlerSDK
a crawler sdk of rong360

支持的iOS最低版本7.1, Xcode 8.0

需要先申请appid, 配置好相应的证书和回调url

接入步骤：

1.在Podfile文件里添加 pod 'RDPCrawlerSDK', 然后pod update(需更新下本地pod仓库，避免本地仓库不包含sdk的信息)



2.权限配置

a.如果支持了ATS, 需要对uiwebview放开限制

	<key>NSAllowsArbitraryLoadsInWebContent</key>
	<true/>

b.如果使用支付宝抓取，需要在工程中的info.plist中的添加LSApplicationQueriesSchemes数组,在数组里面添加一个支持的scheme:alipayqr

	<key>LSApplicationQueriesSchemes</key>
	<array>
		<string>alipayqr</string>
	</array>

c.使用运营商时，如果支持了ATS,需要对以下域名放开限制

10086.cn,

10010.com,

189.cn,

chinamobile.com,

telecomjs.com,

ct10000.com,

chinatelecom-ec.com


plist配置：

    <key>NSAppTransportSecurity</key>
	<dict>
		<key>NSExceptionDomains</key>
		
		<dict>
		
			<key>10086.cn</key>
			<dict>
				<key>NSExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSExceptionMinimumTLSVersion</key>
				<string>TLSv1.0</string>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
				<key>NSIncludesSubdomains</key>
				<true/>
			</dict>
			
			<key>10010.com</key>
			<dict>
				<key>NSExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSExceptionMinimumTLSVersion</key>
				<string>TLSv1.0</string>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
				<key>NSIncludesSubdomains</key>
				<true/>
			</dict>
			
			<key>189.cn</key>
			<dict>
				<key>NSExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSExceptionMinimumTLSVersion</key>
				<string>TLSv1.0</string>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
				<key>NSIncludesSubdomains</key>
				<true/>
			</dict>
			
			<key>chinamobile.com</key>
			<dict>
				<key>NSExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSExceptionMinimumTLSVersion</key>
				<string>TLSv1.0</string>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
				<key>NSIncludesSubdomains</key>
				<true/>
			</dict>
			
			<key>telecomjs.com</key>
			<dict>
				<key>NSExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSExceptionMinimumTLSVersion</key>
				<string>TLSv1.0</string>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
				<key>NSIncludesSubdomains</key>
				<true/>
			</dict>
			
			<key>ct10000.com</key>
			<dict>
				<key>NSExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSExceptionMinimumTLSVersion</key>
				<string>TLSv1.0</string>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
				<key>NSIncludesSubdomains</key>
				<true/>
			</dict>
			
			<key>chinatelecom-ec.com</key>
			<dict>
				<key>NSExceptionAllowsInsecureHTTPLoads</key>
				<true/>
				<key>NSExceptionMinimumTLSVersion</key>
				<string>TLSv1.0</string>
				<key>NSExceptionRequiresForwardSecrecy</key>
				<false/>
				<key>NSIncludesSubdomains</key>
				<true/>
			</dict>
			
		</dict>
		
		<key>NSAllowsArbitraryLoadsInWebContent</key>
		<true/>
		
	</dict>




3.初始化sdk, 调用接口传入appid和对应的p12格式的私钥    

  	NSString *keyPath = [[NSBundle mainBundle] pathForResource:@"private" ofType:@"p12"];

 	NSData *keyData = [NSData dataWithContentsOfFile:keyPath];

  如私钥文件没有设置密码调用以下方法：
  
  	[RDPCrawlerManager configAppId:@"申请的appid" delegate:self privateKey:keyData];
 
  如私钥文件设置了密码调用另一个方法:
  
  	[RDPCrawlerManager configAppId:@"申请的appid" delegate:self privateKey:keyData password:@"私钥密码"];
  

4.调用所需类型的抓取服务

  支付宝抓取:
  
  	[RDPCrawlerManager startCrawlerByType:kRDPCrawlerTypeAlipay identifier:@"自定义的任务id"];
  
  淘宝抓取:
  
  	[RDPCrawlerManager startCrawlerByType:kRDPCrawlerTypeTaobao identifier:@"自定义的任务id"];
  
  运营商抓取:
  
  	  RDPC_OperatorConfig *config = [RDPC_OperatorConfig new];
	  config.phone = @"xxxxxxxxxx";
	  config.canEditPhone = YES;
	  [RDPCrawlerManager startCrawlerOperatorByConfig:config identifier:@"自定义的任务id" addtionalParams:nil];
	
  ps:
  
    如需使用测试环境联调，需调用[RDPCrawlerManager setIsDebug:YES]

    pem转p12的步骤，需在终端输入如下命令:

    如pem文件为private.pem

    
    (1) 生成一个csr文件

      openssl req -new -key private.pem -out rsacert.csr

      这时候要求输入以下一些证书配置信息

      Country Name (2 letter code) [AU]:CN

      State or Province Name (full name) [Some-State]:beijing

      Locality Name (eg, city) []:beijing

      Organization Name (eg, company) [Internet Widgits Pty Ltd]:Rong360

      Organizational Unit Name (eg, section) []:com

      Common Name (e.g. server FQDN or YOUR name) []:R360

      Email Address []:whj5484138@gmail.com

      Please enter the following 'extra' attributes

      to be sent with your certificate request

      A challenge password []:

      An optional company name []:



   (2)生成证书并且签名,有效期10年

      openssl x509 -req -days 3650 -in rsacert.csr -signkey private.pem -out rsacert.crt


   (3)生成P12文件

      openssl pkcs12 -export -out private.p12 -inkey private.pem -in rsacert.crt

 
