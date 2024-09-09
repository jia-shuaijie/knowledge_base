配置ssh key有三步
1. 使用密钥工具生成`rsa`密钥和公钥
2. 将`rsa`公钥添加到代码管理平台
3. 将`rsa`密钥添加到ssh-agent中,为ssh client指定使用的密钥文件

## 密钥生成

## 检查是否已经存在ssh key
检查当前主机是否已经存在ssh key
```shell
# 切换到.ssh目录
cd ~/.ssh 
# 查看当前目录下的文件
ls
```
出现下面这种情况就是已经存在ssh key
```shell
$ ls
id_rsa  id_rsa.pub  known_hosts
```
输入ls什么都没有或者只有known_hosts的时候说明当前没有ssh key,这个时候就需要生成ssh key.

## 生成ssh key

```shell
# 输入下面命令生成ssh key
ssh-keygen -t rsa -C 'xxx@xxx.com'
# 查看生成的ssh key信息
cat ssh-rsa
```
![github sshKey](https://i.jpg.dog/1e48f0c3e12c1c050a3356c041320f93.png)
## GitHub设置密钥
![github click settings](https://i.jpg.dog/7d8fe9e1a2ca99b485129cb1beae7c35.png)
![github find sshKey](https://i.jpg.dog/90efa45506f5f701c33a68cfbf64afd1.png)
![github set sshKey](https://i.jpg.dog/337a6c0304e18ad6b952eef440cdb31f.png)

检查当前配置的密钥是否可用
```shell
ssh -T git@github.com
```