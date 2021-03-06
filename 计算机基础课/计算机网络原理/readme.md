## OSI参考模型

- 物理层：在物理线路上传输原始的二进制数据位
- 数据链路层：在有差错的物理线路上提供无差错的数据传输
- 网络层：控制通信子网提供源点到目的点的数据传送
- 传输层：为用户提供端到端的数据传送服务

- 会话层：为用户提供会话控制服务（安全认证）
- 表示层：为用户提供数据转换和表示服务
- 应用层



## TCP/IP参考模型

- 把物理层和数据链路层合起来称为Host-to-Network
- 网络层：控制通信子网提供源点到目的点的IP包传送，实现异构网络互联
- 传输层：提供端到端的数据传送服务。TCP和UDP
- 应用层



## 多路复用技术

- 时分复用
- 频分复用
- 波分复用



## 交换技术

- 交换式
  - 电路交换：在发送数据前，建立点到点的物理通路。传输完毕之后拆除物理通路。
  - 报文交换：信息以报文（逻辑上完整的信息段，很大的一段）为单位进行存储转发
  - 分组交换：源节点把报文分成packet，在中间节点存储转发，目的节点把分组合成报文
    - 数据报分组交换：每个packet带有网络地址（源、目的），可走不同的路径
    - 虚电路分组交换：
- 广播式



## 物理层

传输介质：

- 基带同轴电缆（数字传输，0/1）
- 宽带同轴电缆（模拟传输，连续）
- 光纤



## 数据链路层

- 组帧：把物理层的比特流组成帧，并计算每个帧的校验和。
- 检错/纠错
- 基本的数据链路协议
  - 无约束单工协议（理想情况）
  - 单工停等协议：接收方不能无休止接收。接收方每收到一个帧后，给发送方回送一个响应，告诉发送方可以再发了。
  - 有噪声信道的单工协议：如果正确到达，则发送相应帧；如果错误/丢失，则等待超时重传。



## 信道争用

解决信道争用的协议称为介质访问控制协议MAC，是数据链路层的一部分。

