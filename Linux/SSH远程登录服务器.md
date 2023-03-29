# SSH远程登录服务器

### 登录端
1. 生成公私钥对
   * ssh-keygen -t rsa 【-C "邮箱"】
2. 将公钥文件上传至服务器
   * windows下文件位于C:/Users/***/.ssh
   * id_rsa.pub为公钥，id_rsa为私钥

### 服务器
1. 存放公钥pub文件
   * cp id_rsa.pub  .ssh/authorized_keys
   
2. 修改ssh配置文件
   1. 修改前备份文件
       * cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
       
   2. 修改文件
       * vim /etc/ssh/sshd_config
          ```
         PubkeyAuthentication yes                #启用公钥私钥配对认证方式
         AuthorizedKeysFile .ssh/authorized_keys #公钥文件路径
         PermitRootLogin yes                     #允许root使用ssh登录
         ```
       
   3. 重启ssh服务(命令参考)
        * systemctl start sshd.service					#开启
        * systemctl enable sshd.service		        #设为开机自启
        * systemctl stop sshd.service					#关闭
        * systemctl status sshd.service				 #查看状态
        * systemctl restart sshd.service				#重启
        * systemctl reload sshd.service				#重新加载配置文件

### 登录
* ```
  ssh  [-p 端口号] username@服务器地址
  ```

### 安全配置
* **查看ssh失败日志（是否有攻击记录）**
   * ```
     grep "Failed password" /var/log/secure
     ```
* **修改ssh默认端口  9229 **    

  1. 修改ssh配置文件
     * vim /etc/ssh/sshd_config
        ```
       Port 22     #暂且保留，成功后再删除
       Port 9229   #新端口,注意不要冲突
       ```
     * 防火墙开放新端口(参考命令)
        ```
       firewall-cmd --zone=public --add-port=9229/tcp --permanent   #开放指定端口
       firewall-cmd --reload        #重启防火墙
       firewall-cmd --list-port     #查看已开放端口
       ```
     * 云服务器防火墙开启相应端口
           

  3. 重启ssh服务
     * 参考命令
       ```
       systemctl start sshd.service    #开启
       systemctl enable sshd.service   #设为开机自启
       systemctl stop sshd.service     #关闭
       systemctl status sshd.service   #查看状态
       systemctl restart sshd.service  #重启
       systemctl reload sshd.service   #重新加载配置文件
       ```
  4. 测试ssh新端口是否可用
       * ```ssh  [-p 端口号] username@服务器地址  -p  新端口```
  5. 关闭原ssh端口 (22)
       * vim /etc/ssh/sshd_config
         ```
         #Port 22    #关闭原端口
         Port 9229   #保留新端口
         ```

