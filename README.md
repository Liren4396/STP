**STP 段传输模拟**
概览  
模拟了使用 UDP 套接字的简单发送方-接收方通信  

功能  
命令行参数解析  
随机数生成  
模算术运算  
UDP 套接字通信  
非阻塞和阻塞套接字操作  
主机与网络字节顺序转换  
滑动窗口机制  
多线程（发送方）  
简单的日志记录机制  
  
使用方法  
运行接收方  
bash  
python3 receiver.py receiver_port sender_port txt_file_received max_win  
python3 receiver.py 56007 59606 1000  
此命令启动接收方，指定自身端口为56007，接收 59606端口 的接收方发过来的数据，滑动窗口窗口大小为1000字节  
  
运行发送方  
bash  
python3 sender.py sender_port receiver_port txt_file_to_send max_win rto flp rlp  
python3 sender.py 59606 56007 test1.txt 1000 500 0.1 0.1  
此命令启动发送方，指定自身端口为59606，向 56007端口 的接收方传输随机数字，滑动窗口大小为 1000 字节，超时重传时间间隔为 500ms 正向丢包率0.1，反向丢包率0.1  
  
描述  
发送方有三个命令行参数：接收方的主机名/IP、端口号和运行时长  
接收方在指定端口上监听并等待消息，有指定的超时时间  
消息格式  
发送方至接收方  
2 字节：16 位无符号整数  
接收方至发送方  
3 字节：原始数字（2 字节），奇数/偶数标志（1 字节）  
  
代码结构  
STP_segment：代表消息数据结构的类  
parse_port()：验证和解析端口号的函数  
parse_wait_time()：验证和解析等待时间的函数  
parse_win()：验证和解析滑动窗口大小的函数  
recvISN_sendACK()：接收初始 SYN 消息并发送 ACK 的函数  
send_data()：发送数据并处理丢失和确认的函数  
主执行块：设置套接字，处理消息传输，并记录活动  
  
滑动窗口机制  
滑动窗口用于控制发送方在等待确认前可以发送的数据量  
发送方维护一个窗口，包含已发送但尚未确认的数据段  
接收方通过发送 ACK 消息来确认接收到的数据段  
发送方在接收到 ACK 后，将窗口向前滑动，继续发送新的数据段  
