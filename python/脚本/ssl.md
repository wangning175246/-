自动识别证书中扩展域名

```python
import os,subprocess,argparse,time,shutil

def getTime():
    return time.strftime("%Y-%m-%d_%H:%M:%S", time.localtime())

def getData():
    return time.strftime("%Y-%m-%d", time.localtime())
logDir="/tmp/nginx/"+getData()
if not os.path.exists(logDir):
       os.makedirs(logDir)
def getArgs():
    dirName="/data/crtfile/"
    endKey="key"
    endCrt="crt,pem"
    distDir="/data/nginx/ssl/"
    serverNameConf="/data/nginx/conf/vhost/servername.txt"
    parser = argparse.ArgumentParser()
    parser.add_argument('--dn',  type=str, help='输入绝对路径 默认/data/crtfile/')
    parser.add_argument('--dd',  type=str, help='输入绝对路径 默认/data/nginx/ssl/')
    parser.add_argument('--snf',  type=str, help='输入绝对路径 默认/data/nginx/conf/vhost/servername.txt')
    parser.add_argument('--ec',  type=str, help='crt文件的后缀 默认key')
    parser.add_argument('--ek',  type=str, help='key文件的后缀 默认crt,pem')
    args = parser.parse_args()

    if args.dn == "" :
        print("证书路径不能为空")
    elif args.dn is  None:
        pass
    else:
        dirName= args.dn
    if args.ek =="" :
        print("目标路径不能为空")
    elif args.ek is None:
        pass
    else:
        endKey= args.ek
    if args.snf == "" :
        print("需要制定servername配置文件")
    elif args.snf is  None:
        pass
    else:
        serverNameConf= args.snf
    if args.ec =="" :
        print("证书文件的后缀 不能为空")
    elif args.dn is  None:
        pass
    else:
        endCrt= args.ec
    if args.ek =="" :
        print("key文件的后缀 不能为空")
    elif args.dn is None:
        pass
    else:
        endKey= args.ek

    return dirName,endCrt,endKey,distDir,serverNameConf

# 获取证书文件列表
def GetFileList(dirName,endCrt,endKey):
    crtList=[]
    keyList=[]
    for i in os.listdir(dirName):
        for endkey in endKey.split(","):
            if i.endswith(endkey):
                keyList.append(dirName+i)
                continue
        for endcrt in endCrt.split(","):
            if i.endswith(endcrt):
                crtList.append(dirName+i)
                continue
    return crtList,keyList

# 获取crt key 的所有MD5
def getFileMD5(keyfileMD5,cerfileMD5,crtFileList,keyFileList):
    for i in crtFileList:
        child=subprocess.Popen("openssl x509 -noout -modulus -in {} |openssl md5".format(i),shell=True,stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        md5=child.stdout.read().decode("utf-8").strip().split("=")[-1].strip()
        cerfileMD5[i]=md5
    for i in keyFileList:
        print(i)
        child=subprocess.Popen("openssl rsa -noout -modulus -in {} |openssl md5".format(i),shell=True,stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        md5=child.stdout.read().decode("utf-8").strip().split("=")[-1].strip()
        print(md5)
        keyfileMD5[md5]=i

# 获取证书的所有域名
def crtUsableName(crtFileList,domain):
    for i in crtFileList:
        # crt=crypto.load_certificate(crypto.FILETYPE_PEM,open(i,"r").read())
        domainlist=[]
        # for dn in crt.get_extension(2).__str__().split(","):
        #     domainlist.append(dn.split(":")[-1])
        # openssl x509 - in jksdfdfff.1234lol.com.crt - noout - text | grep 'X509v3 Subject Alternative Name' - A 1 | tail - 1
        # print(i)
        result=subprocess.Popen("openssl x509 -in {} -noout -text | grep 'X509v3 Subject Alternative Name' -A 1 | tail -1 ".format(i),shell=True,stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        domainstr=result.stdout.read().decode("utf-8").strip()
        for dn in domainstr.split(","):
            dstr=dn.split(":")[-1]
            if not  dstr.find("*"):
                # print(dstr)
                # print(dstr)
                pass
            else:
                domainlist.append(dstr)
        domain[i]=domainlist

# 生成key文件和crt文件
def generatedKeyCrt(keyfileMD5,cerfileMD5,domain,distDir):
    for i in domain.keys():
        # print(i)
        if keyfileMD5.get(cerfileMD5[i]) is not None:
            crt=open(i,"r").read()
            # print(type(crt))
            key=open(keyfileMD5.get(cerfileMD5[i]),"r").read()
            # key=open("")
            # print(i)
            for j in domain[i]:
                # print(j)
                crtfile=distDir+j+".crt"
                keyfile=distDir+j+".key"
                print(crtfile,keyfile)
                if  os.path.exists(crtfile) and  os.path.exists(keyfile) :
                    shutil.copyfile(crtfile,logDir+"/"+j+getTime())
                with open(crtfile, "w") as f:
                    f.write(crt)
                with open(keyfile, "w") as f:
                    f.write(key)
def editServerNameFile(domain,serverNameConf):
    srcServernameList=[]
    shutil.copyfile(serverNameConf,logDir+"/servername.txt-"+getTime())
    for i in domain.values():
        srcServernameList.extend(i)
    with open(serverNameConf,"r+") as f:
        for i in f.readlines():
            # print(i)
            if  i != "" and i.strip() != "server_name" and  i.strip() != ";":
                srcServernameList.append(i.strip())
                # print(srcServernameList)
    with open(serverNameConf,"w") as f:
        f.seek(0)
        f.truncate()
        f.write("server_name")
        f.write("\n")
        for i in set(srcServernameList):
            f.writelines(i)
            f.write("\n")
        f.writelines(";")
        f.write("\n")

if __name__ == "__main__":
    keyfileMD5={}
    cerfileMD5={}
    domain={}
    dirName,endCrt,endKey,distDir,serverNameConf=getArgs()
    crtFileList,keyFileList=GetFileList(dirName,endCrt,endKey)
    getFileMD5(keyfileMD5,cerfileMD5,crtFileList,keyFileList)
    crtUsableName(crtFileList,domain)
    generatedKeyCrt(keyfileMD5,cerfileMD5,domain,distDir)
    editServerNameFile(domain=domain,serverNameConf=serverNameConf)
    print(keyfileMD5)
```

