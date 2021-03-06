此IP文件，为 **Taobao Ip** 离线版。有 国家，省份，城市，运营商 字段。

字节序使用 **little-endian**，使用其他字节序的主机需要自行处理。

ps: **little-endian** 中数字是倒序的，其他是正常排序。

[Wikipedia 操作系统和架构对应的字节序](https://en.wikipedia.org/wiki/Endianness#Endianness_and_operating_systems_on_architectures)

## 文件结构图示

![文件图示](https://github.com/slene/iploc/raw/master/format.png)

## 文件结构说明

#### 文件头
---

头12个字节，每4个是一个 uint32 数字。分别是：

|索引起始地址|数据起始地址 |索引IP数量  |
|------------|-------------|------------|
|6a 5f 2a 00 |c8 10 02 00  |58 eb 01 00 |

ps: 上面的12个字节只是示例，当文件里IP数据改变时会有变化，以实际为准。

#### 文本区
---
每一段文本 string 都以字节 0x00 结束，所有文字数据都是UTF-8编码的。

#### 数据区
---
每 9 个字节为一段，其顺序为：

| flag      | 国家代码   | 省份       | 城市       | 运营商     |
| :-------- | :--------- | :--------- | :--------- | :--------- |
| uint8 - 1 | string - 2 | uint16 - 2 | uint16 - 2 | uint16 - 2 |

* flag == 1 IANA保留地址
* flag == 2 已分配地址
* flag == 4 IANA未分配地址

当 国家代码 全等于字节 0x00，或者，flag标识为保留地址或未分配地址。那么其后面的数据都是不存在的。

国家代码 eg: CN

省份, 城市, 运营商，都是连接到文本区的地址，此地址为绝对地址，从文件开始处起始。

#### 索引区
---
每 4 个字节为一个ip范围起始数字地址，uint32，总共数量为 **索引IP数量**

0.0.0.0 ～ 255.255.255.255 之间的IP地址，索引区已经全部覆盖。

意味着，在索引里，匹配IP地址时，当一个IP地址大于等于IP A，小于他的下一个IP B时，那他就是匹配A的，从而计算出数据地址。

关于数据地址计算:

数据区里的分段数和索引区的是相同的，所以当前索引的地址对应的数据地址是：

> 数据地址 = 索引地址 / 4 * 9 + **数据起始地址**

ps: 上述公式里“索引地址”表示从索引起始地址开始的字节长度

## 使用方法

    go get github.com/slene/iploc

api 调用方法，参见 example 里的例子

## TODO

* 增加 python 版本 iploc






