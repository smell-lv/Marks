Python的字节数据
====

微机原理告诉我们，计算机使用8位单字节数据来表示0～255的整形数据，并使用多个字节表示16位、32位的数据。在一些底层语言（以C语言为例），数据的区别仅仅是位数不同，同一内存空间的数据通过不同的表征方式可表示字符、整形、浮点型数据，亦可表示字符串、整形数组和浮点型数组。

以单字节的0x31为例，当表示为整形时为数字49，表示为ASCii码字符时为字符‘1’；再以连续地址的四个字节0x31323334为例，整数时为数字522199330，再以单精度浮点型表示时为1.6688933612840628e-07。正是因为C语言的变量在内存上的无差别性，使得有些操作变得异常容易。

笔者最近使用python做一些数据分析、算法和项目工作，因为其提供了大量数据类型，又有numpy、pandas等工具，给数据处理带来了很多方便，但却在硬件处理上遇到了难题。

因为python的数据是经过封装的，很多转换适合人类理解却不符合最早的C语言逻辑，给硬件操作带来了不小的麻烦。如串口、socket通信等，使用的是字节数据，而python的字节数据对封装数据的转换方法是需要认真理解和记录的，在此将用到的方法总结如下。

# 1. bytes和bytearray，字节序列和字节数组

python的字节类型分为bytes和bytearray两种，分别对应可变长和不可变长序列，推测前者可能是线性结构，后者是链式结构.依据如下：

python中使用id函数来看变量所在内存地址，我们首先定义4字节的bytes和bytearray，依次查看每个字节所在地址,再向bytearray中插入一个字节，再查看。

```python
bs = bytes(b'1234')
ba = bytearray(b'1234')
print(id(bs))
print(id(bs[0]))
print(id(bs[1]))
print(id(bs[2]))
print(id(bs[3]))
print(id(ba))
print(id(ba[0]))
print(id(ba[1]))
print(id(ba[2]))
print(id(ba[3]))
ba.insert(1,1)
print(id(ba))
print(id(ba[0]))
print(id(ba[1]))
print(id(ba[2]))
print(id(ba[3]))
print(id(ba[4]))
```


# 2. 其他类型转换为bytearray

bytes是python的字节序列，bytearray是python的字节数组，二者是用来表示字节数据的核心方式，当有单字节数据需要处理时，最好能先转换为该类型再做其他操作。至于哪种方法更好，笔者倾向于更加灵活的bytearray，毕竟硬件资源是很廉价的，没必要锱铢必较。

```python
l1 = list(range(12)) # 数字范围为0～255，否则无法转换为字节数组，不需要解释
bl1 = bytearray(l1)

s1 = 'ABCDEFG'.encode()
 # python将去封装解释为encode，与通常的理解相反，应该是将字节流当成一种特定情况下的特定格式来考虑
 # 这里encode内为编码方式，如内容为纯英文字母则为'utf-8'（也可以不要），但如果里面有简体中文要记得使用'gb2312'
bs1 = bytearray(s1)

#以上方法还可以写做如下，不同的是这里的s1为通用格式：
s1 = 'ABCDEFG'
bs1 = bytearray(s1,'utf-8')

#从hex表示法的字符串中获得bytearray，所有与bytearry相关的16进制表示法都是不要0x前缀的
bh1 = bytearray.fromhex('31323334') # bytearray(b'1234')
#相反的方法是 bh1.hex()
```

# 3. bytearry的一些操作

bytearray的索引和切片是不同的，索引像list，切片像string，原因是bytearray是由单个数字组成的集合，数字被单独取出来时没有了byte属性。

bytearray又可以像C语言一样表示，现整理如下：

```python
b1 = bytearray(range(32))
#索引
b1[0] == 0 # True
#切片
b1[:1] == b'\x00'
b1.hex() # '000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1f'
hex(b1[0]) == '0x0' # True
b1[:1].hex() == '00' # True

b2 = bytearray(range(int('0x30',16),int('0x3a',16)))
b2.decode('utf-8') # '0123456789'
```

# 4. 其他相关操作

## 4.1 整形数字到0x表示法16进制字符的互相转换

```python
hex(65535) # '0xffff'
format(65535, 'x') # 'ffff'
# 参考[这里](http://www.runoob.com/python/att-string-format.html)
int('0xffff', 16) # 65535
int('ffff', 16) # 65535
```

## 4.2 四个整形数字到浮点型数字的相互转换
```python
import struct
x = bytearray.fromhex('31323334')
f = struct.unpack('f',x)[0] # 这里的x只能是四字节

f = 3.14
x = list(struct.pack('f',f))
```

## 4.3 多个8位字节到对应长整形转换
```python
l1 = [255,255]
n1 = int.from_bytes(bytearray(l1),'big') # 65535

n2 = 65535
l2 = list(n2.to_bytes(2,'big'))
```




