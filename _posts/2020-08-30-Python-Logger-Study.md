---
layout: post
title: "Logging的相关知识"
date: 2020-08-30
description: "Logging的相关知识点"
tag: Python
---

# 一、logging的对象

 - Logger: 写日志
 - Handler: 将log发送handler指定的位置：控制台或者文件夹
 - Filter：对日志进行过滤
 - Formatter: 日志的格式
 ```python
import logging
'''
logging 模块的四个组成部分：
1.looger : 写日志
2.handler ： 让日志在工作台或者文件夹中显示
3.filter： 对日志进行过滤
4.formatter： 日志的格式

'''

def log():

    #1.配置日志记录器
    logger = logging.getLogger('AutoTest')

    #2.设置日志等级：debug< INFO < Warning < Error < Critical
    logger.setLevel(logging.DEBUG)

    #设置handler： logging.FileHandler(name,encoding)/ logging.StreamHandler()
    fh = logging.FileHandler('test.log',encoding='utf-8')

    #设置格式:
    '''
    1. %(level_name)s --日志等级
    2. %(asctime)s -- 日志时间
    3. %(message)s --日志信息
    4. %(name)s -- 日志名
    '''
    fmt = '%(levelname)s-%(asctime)s-%(message)s'

    #把格式放在Formatter
    format = logging.Formatter(fmt)
    fh.setFormatter(format)
    logger.addHandler(fh)

    sh = logging.StreamHandler()
    sh.setFormatter(format)
    logger.addHandler(sh)
    return logger

if __name__ == '__main__':

    logger = log()

    try:
        a = b
        print(a)
    except Exception as e:
        logger.warning(e)

    logger.info('done')
 
 ```

