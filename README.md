# AutomatedPackaging
Python实现iOS自动化打包

首先你得下载python，这一步自己百度，这里就不说了，下载之后打开IDE
打开Xcode->product->获取.app的路径
### mac的话，打开Preferences -> Project: untitled1 -> Project Interpreter ,点击加号，输入requests.这是一个类似于AFN的东西，是网络请求框架，然后Install Packge

### windows -> settings -> Project: untitled1 -> Project Interpreter ,点击加号，输入requests.

### 或者你可以自己去百度，下载一个requests，拖到python目录下的lib下

把下载的python文件拖入到untitled1项目下，然后终端执行命令
```
$ python /Users/XXXX/Downloads/iOSAutoPage-master/AutoPage/DaoBao\ 3.6.py 
```

修改下面代码中主机名字和key，还有刚刚获取的App的路径
```
#!/usr/bin/env python
#coding=utf-8 
import os
import requests
import webbrowser
import subprocess
import shutil

appFileFullPath = '/Users/XXXX/Library/Developer/Xcode/DerivedData/MoneyTree-bjgtycapnrnkowannfuavmzoeetf/Build/Products/Debug-iphoneos/MoneyTree.app'
PayLoadPath = '/Users/XXXX/Desktop/Payload'
packBagPath = '/Users/XXXX/Desktop/ProgramBag'



#上传蒲公英 这个key在蒲公英->我的应用->XX应用->API里面可以找到
openUrlPath = 'https://www.pgyer.com/manager/dashboard/app/bcee25xxxxxxxxxxxxxxxx2225b74525'
USER_KEY = "f36c1xxxxxxxxxxxxxxxx7564554aed8"
API_KEY = "97b831xxxxxxxxxxxxxxxx04ee02b0a7"

#上传蒲公英
def uploadIPA(IPAPath):
    if(IPAPath==''):
        print("\n*************** 没有找到对应上传的IPA包 *********************\n")
        return
    else:
        print("\n***************开始上传到蒲公英*********************\n")
        url='http://www.pgyer.com/apiv1/app/upload'
        data={
            'uKey':USER_KEY,
            '_api_key':API_KEY,
            'installType':'2',
            'password':'',
            'updateDescription':"修复已知BUG"
        }
        files={'file':open(IPAPath,'rb')}
        r=requests.post(url,data=data,files=files)

def openDownloadUrl():
    webbrowser.open(openUrlPath,new=1,autoraise=True)
    print ("\n*************** 更新成功 *********************\n")

#编译打包流程
def bulidIPA():
    
    #删除之前打包的ProgramBag文件夹
    subprocess.call(["rm","-rf",packBagPath])
    #创建PayLoad文件夹
    mkdir(PayLoadPath)
    #将app拷贝到PayLoadPath路径下
    subprocess.call(["cp","-r",appFileFullPath,PayLoadPath])
    #在桌面上创建packBagPath的文件夹
    subprocess.call(["mkdir","-p",packBagPath])
    #将PayLoadPath文件夹拷贝到packBagPath文件夹下
    subprocess.call(["cp","-r",PayLoadPath,packBagPath])
    #删除桌面的PayLoadPath文件夹
    subprocess.call(["rm","-rf",PayLoadPath])
    #切换到当前目录
    os.chdir(packBagPath)
    #压缩packBagPath文件夹下的PayLoadPath文件夹夹
    subprocess.call(["zip","-r","./Payload.zip","."])
    print ("\n*************** 打包成功 *********************\n")
    #将zip文件改名为ipa
    subprocess.call(["mv","payload.zip","Payload.ipa"])
    #删除payLoad文件夹
    subprocess.call(["rm","-rf","./Payload"])


#创建PayLoad文件夹
def mkdir(PayLoadPath):
    isExists = os.path.exists(PayLoadPath)
    if not isExists:
        os.makedirs(PayLoadPath)
        print(PayLoadPath + '创建成功')
        return True
    else:
        print (PayLoadPath + '目录已经存在')
        return False


if __name__ == '__main__':
    des = input("请输入更新的日志描述:")
    bulidIPA()
    uploadIPA('%s/Payload.ipa'%packBagPath)
    openDownloadUrl()

```
