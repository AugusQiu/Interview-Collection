# TCP、UDP的区别，最佳场景
TCP和UDP都是传输层协议，TCP提供面向连接、可靠的字节流服务，UDP是不可靠的面向数据报的服务.  所谓**不可靠**即没有确认机制、重传机制；**无连接**:知道对方的ip和端口就直接进行传输，不需要建立连接；**面向数据报**：应用层交给UDP多长的报文，UDP就直接原样发送，不会拆分和合并  
区别:  
1.报头不同  
2.特点不同  
3.协议不同  
最佳使用场景:  
TCP:一般用于文件传输(FTP和HTTP对于数据准确性要求高)、邮件发送(POP IMAP SMTP)、远程连接(Telnet ssh)  
UDP:一般用于即时通讯，qq聊天、在线视频(RTSP要求速度快，视频连续就行，掉帧可接受)、网络语音电话
## TCP和UDP报文的格式，字段的意义
TCP报文段分为**首部和数据**两部分， TCP报文段首部的前20个字节是固定的，后面有4N字节是根据需要而增加的选项（N是整数）。因此TCP首部的最小长度是20字节  
https://blog.csdn.net/xieyutian1990/article/details/24913027  
UDP有两个字段：数据字段和首部字段 
https://blog.csdn.net/xieyutian1990/article/details/24907797

## TCP通过什么方式来提供可靠传输
https://www.cnblogs.com/allen-rg/p/7190325.html