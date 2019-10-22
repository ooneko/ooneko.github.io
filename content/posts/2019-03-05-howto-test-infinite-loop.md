---
title: 如何测试"无限循环"的代码
date: 2019-03-05 17:42:50
tags: [python, test]
---


在写 python 有时会遇到一些”无限循环“的代码需要测试，比如一个持续监控某个api状态的线程。很有可能会写成这样:
```python
def watch(self):
    while self.running:
        do_something()
        ...
```

这样的话，单元测试写起来就比较奇怪了。最先想到的是把其中某个函数mock掉，通过side_effect 返回一个exception 来结束测试。
```python
class TestCase(unittest.TestCase):
    def test_watch(self):
        do_something = mock.Mock()
        do_something.side_effect = ['some_valuable_info', Exception]
        self.assertRaises(Exception, do_something)
```

有没有更好的方法呢？
有.

把退出条件放到一个方法里面去，在测试的时候，通过mock掉这个方法，来达到退出条件。

```python
def is_running(self):
    return self.running

def watch(self):
    while self.is_running():
        do_something()
        ...
```

这样测试起来就容易多了, 当第一次运行时，调到的is_running 是True，第二次运行调用到is_running 的时候就会退出测试。

```python
class TestCase(unittest.TestCase):
    def test_watch(self):
        mock_is_running = mock.Mock()
        mock_is_running.side_effect = [True, False]
        watch()
        ...
```

**总结一下：**
几乎没有真正的‘无限循环’代码，找出它的退出条件，然后重构代码。