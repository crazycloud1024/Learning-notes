### kafka消息队列
##### 1. 介绍
##### 2. 安装
##### 3. python使用
1. 安装支持kfaka的库 `pip install kafka`  (python3.7以前)
   >注：如果在import kafka的时候出现:
    ```python
    File "/usr/lib/python3.7/site-packages/kafka/producer/simple.py", line 54
        return '<SimpleProducer batch=%s>' % self.async
                                                    ^
    SyntaxError: invalid syntax
    ```
    是因为在python3.5之后 async 变成了python的关键字，所以导致错误，这个时候应该安装`pip install kafka-python` 而不是 `pip install kafka`
