---
title: Bash brace expansion
date: 2018-12-11 15:17:28
tags:
---
bash 中的大括号"{}",通常用来生成一组有规律的字符串

最常用的 `mkdir /tmp/test{1..3}`
在执行的时候会被翻译成:
```bash
mkdir /tmp/test1 /tmp/test2 /tmp/test3
```

生成连续的字符或者数字用`".."`
```bash
  {0..12}        => 0 1 2 3 4 5 6 7 8 9 10 11 12
  {3..-2}        => 3 2 1 0 -1 -2
  {a..g}         => a b c d e f g
  {g..a}         => g f e d c b a
```

生成非连续的字符用逗号分隔“,”
```bash
{aa,bb,cc,dd}  => aa bb cc dd
```

当然也可以组合使用
```bash
{a,b{0..3},c}  => a b0 b1 b2 b3 c
```

当brace expation 和其他字符串一起出现时，会重复整个字符串
```bash
"Hello"{0..1}       => Hello0 Hello1
"Hello"{a,b{0..1},c} => Helloa Hellob0 Hellob1 Helloc
```

还有一个比较常用个的地方是for循环
**for**
```bash
for i in {1..3};
do
    echo ${i}
done
# brace expantion 在这里会把 {1..3}翻译成 1 2 3
for i in 1 2 3;
do
    echo ${i}
done
```

也可以通过 set +B 来关闭 brace expansion 特性,这个特性默认是打开的
```bash
man set
...
-B      The shell performs brace expansion (see Brace Expansion above).  This is on by default.
...
```
