# netty学习笔记

### channelpipeline

引用:http://blog.csdn.net/zxhoo/article/details/17264263

### 编码器解码器handler

引用1: http://www.importnew.com/26577.html ，拆包，粘包的问题

引用2: http://www.infoq.com/cn/articles/netty-codec-framework-analyse/ 内置编码解码器

LineBasedFrameDecoder 回车换行解码器

DelimiterBasedFrameDecoder 分隔符解码器

FixedLengthFrameDecoder 固定长度解码器

ObjectEncoder Java序列化编码器

LengthFieldBasedFrameDecoder 通用解码器

### ChannelInitializer


### ByteBuf

BtyeBuf读操作主要提供以下功能：

readByte：取1字节的内容；
skipBytes： 跳过内容
readUnsignedByte：取1字节的内容，返回（(short) (readByte() & 0xFF)）；（能把负数转换为无符号吗？）
readShort：取2字节的内容，返回转换后的short类型；
readUnsignedShort：取2字节的内容，返回readShort() & 0xFFFF；
readMedium：取3字节的内容，返回转换后的int类型；
readUnsignedMedium：取3字节的内容，返回转换后的int类型；
readInt：取4字节的内容；
readUnsignedInt：取4字节的内容，返回readInt() & 0xFFFFFFFFL；
readLong：取8字节的内容；
readChar：取1字节的内容；
readFloat：取4字节的int内容，转换为float类型；
readDouble：取8字节的long内容，转换为double类型；
readBytes：取指定长度的内容，返回ByteBuf类型；
readSlice：取指定长度的内容，返回ByteBuf类型；
readBytes：取指定长度的内容到目标容器。

 写操作

写操作提供的功能主要是往ByteBuf中写入byte内容，不再一一赘述。主要区别在于写入前根据类型转换为相对应长度的byte数组。

主要函数是：writeBoolean、writeByte、writeShort、writeMedium、writeInt、writeLong、writeChar、writeFloat、writeDouble、writeBytes、writeZero。

### ChannelFuture