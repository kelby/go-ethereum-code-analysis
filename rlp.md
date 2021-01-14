RLP是Recursive Length Prefix的简写。是以太坊中的序列化方法，以太坊的所有对象都会使用RLP方法序列化为字节数组。

```
字符串 “dog” = [ 0x83, ’d’, ‘o’, ‘g’ ]
列表 [ “cat”, “dog” ] = [ 0xc8, 0x83, ‘c’, ‘a’, ’t’, 0x83, ’d’, ‘o’, ‘g’ ]
空字符串 (‘null’) = [ 0x80 ]
空列表 = [ 0xc0 ]
数字 15 (’\x0f’) = [ 0x0f ]
数字 1024 (’\x04\x00’) = [ 0x82, 0x04, 0x00 ]
空子集合 [ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]
字符串 “Lorem ipsum dolor sit amet, consectetur adipisicing elit” = [ 0xb8, 0x38, ‘L’, ‘o’, ‘r’, ‘e’, ’m’, ‘ ‘, … , ‘e’, ‘l’, ‘i’, ’t’ ]
```

序列化工具

