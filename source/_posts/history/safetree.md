---
title: 安全教育平台完成脚本
date: 2018-10-16 13:29:43
tags: history
desc: 一键完成安全教育平台作业，简易GET练习
---

# LOGIN

提交方式：GET

```
https://fujianlogin.xueanquan.com/LoginHandler.ashx?&userName=[明文账号]&password=[明文密码]&checkcode=&type=login&loginType=1&r=0.8298471421981155&_=[时间戳]
```

# 安全教育平台一键完成

很简单的抓包练习

每个月学校都有这个破作业

没有人想做又要有指标

一个一个帮同学点过去实在是麻烦

还好这个安全教育平台抓包难度没有智学网难，基本都是明文，post都用不上

需要在根目录附带一个accounts.txt，里面每行一个账号

```python
import requests
import time
import re
file_object = open('accounts.txt')
line = file_object.readline()
num=0
print("开始自动化任务")
while(line):
    num+=1
    tot=1
    # 登录账号
    t=time.time()
    timestamp=int(round(t*1000))
    url_login='https://fujianlogin.xueanquan.com/LoginHandler.ashx?jsoncallback=&userName='+line.strip()+'&password=123456&checkcode=&type=login&loginType=1&r=&_='+str(timestamp)
    r_login=requests.get(url_login)
    result=re.findall("TrueName:'(.+?)'", r_login.text)
    if len(result)>0:
        print('序号:'+str(num)+'   账号:'+line.strip()+'   姓名:'+result[0])
        cookies=r_login.cookies
        # 完成作业
        while tot<=2:
            url_ac='https://fuzhou.xueanquan.com/WebApi/Holiday/FinishWork?jsoncallback=&r=&sportYear=2019&semester=1&workStep='+str(tot)+'&_='+str(timestamp)
            r_ac=requests.get(url_ac,cookies=cookies)
            print('['+str(tot)+']'+r_ac.text)
            tot+=1
    else: print("账号:"+line+"登录失败")
    line=file_object.readline()
```
