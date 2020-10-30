# 学习周报

## 学习

* 已理解
1. 通过AT指令利用MQTT协议使BC26模组连接到onenet平台，并进行远程通信，使得能顾通过串口接收到onenet派台下发的命令。
2. 通过编写程序来使得单片机接收到onenet平台下发的指令，代码如下：
 ```
 void connect(void)
{
	 printf("AT+QMTCFG=\"version\",0,4\r\n");
	  delay_ms(300);
	 strx=strstr((const char*)RxBuffer,(const char*)"OK");
	 while(strx==NULL)
	 {
			strx=strstr((const char*)RxBuffer,(const char*)"OK");
      delay_ms(100);
	 }
	 Clear_Buffer2();
	printf("AT+QMTOPEN=0,\"183.230.40.39\",6002\r\n");
	  delay_ms(300);
	 strx=strstr((const char*)RxBuffer,(const char*)"+QMTOPEN: 0,0");
	 while(strx==NULL)
	 {
			strx=strstr((const char*)RxBuffer,(const char*)"+QMTOPEN: 0,0");
      delay_ms(100);
	 }
	 Clear_Buffer2();	 
   printf("AT+QMTCONN=0,\"632116653\",\"373333\",\"202009250001\"\r\n");
	  delay_ms(300);
	  strx=strstr((const char*)RxBuffer,(const char*)"+QMTCONN: 0,0,0");
 	 while(strx==NULL)
		{
       strx=strstr((const char*)RxBuffer,(const char*)"+QMTCONN: 0,0,0");
       delay_ms(100);
		}
		Clear_Buffer2();	
}
```
下发指令图片如图所示：
![下发指令图片](11.png)
3. BC26模组连接onenet可以使用两种模式，一种是缓冲区访问模式，一种是直接推送模式，当时用缓冲区访问模式时，需要获得onenet平台下发指令需要去编写指令函数去完成指令的接收。当采用直接推送模式时，直接通过下发指令便可接收到下发的指令。

* 未理解
1. 从onenet平台下发的数据需要经过解析算法才能够提取出我们想要的数据。

## BC26以mqtt格式收发数据	

1. Buffer Access Mode
	*  AT+QIOPEN=1,0,”TCP”,”183.230.40.39”,6002,1234,0 //通过tcp模式连接到mqtt
	* AT+QISEND ：发送数据
	  
	  AT+QISENDEX：数据已16进制发送数据

	  指令发送数据 将产品ID 设备ID 鉴权信息等数据将以JSON的格式放到设备中，进而实现设备的连接。
	* AT+QIRD=0,512 
	  
	  接收指令，通过此条指令将存在模组里面的数据提取出来
2. Direct Push Mode
    *   AT+QIOPEN=1,0,”TCP”,”183.230.40.39”,6002,0,1
	*   AT+QISEND ：发送数据
	  
	    AT+QISENDEX：数据已16进制发送数据 
	* 在这种模式下将接收到的数据直接存在数组里面
3. 一定要注意在数据下发的时候对数组的处理，在里面可能包括非法字符，一定要将指针数据进行处理，将非法字符进行略过处理。