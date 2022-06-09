<center><h2>window10系统下在非系统盘安装kali subsystem</h2></center>

1. 安装之前，需要打开系统的开发者模式，然后在打开和关闭window系统的功能里勾选window subsystem 选项

2. 等待系统重启

3. 系统重启完成以后，启用windows WSL，操作方法：以管理员身份运行powershell，执行以下命令：

   ~~~powershell
   Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
   
   命令输出结果：
   Path          :
   Online        : True
   RestartNeeded : False
   
   他将会提示你重启你的windows系统。这里我们已经启用了WSL，因此没有提示说明。因此不需要重新启动。
   ~~~

4. 创建安装目录

   选择一个你认为空间大的分区来创建一个目录用安装WSL的Linux发行版，他可以是你的系统上任意分区，任意文件夹，当然我们不建议你使用中文命名你的安装文件或者路径存在中文。分区可以是你任意分区，如D,E,F,G,H盘等。

   这里我们使用powershell在D盘分区中创建一个名为Ubuntu的文件夹，wsl Linux发行版将安装在这个位置。注意请不要使用管理员身份创建这个文件夹，这可能会导致当前用户对该文件夹不可写。

   如你跟着我们教程创建文件夹，请打开你的PowerShell终端执行以下命令创建文件夹：

   ~~~powershell
   New-Item D:\WSL -ItemType Directory
   
   然后执行以下命令设置当前工作目录的位置为D盘的Ubuntu文件夹：
   Set-Location d:\WSL
   
   注意不要忘记这一步，接下来的工作都是在该目录(d:\SWL)中进行
   ~~~

5. 下载Linux发行版，这里需要找到kali的发行版：

   ```powershell
   Invoke-WebRequest -Uri https://wsldownload.azureedge.net/kali-linux-08-06-2019.appx -OutFile kali.appx -UseBasicParsing
   
   你可以点击下面的链接手动下载，然后将下载好的kali.appx文件存放在D:\WSL文件夹中：
   ```

6. 下载完成后，我们将文件名的格式.appx改为zip，以下命令将重命名kali.appx为kali.zip:

   ```powershell
   Rename-Item .\Ubuntu.appx Ubuntu.zip
   ```

7. 解压压缩文件

   ```powershell
   Expand-Archive .\kali.zip -Verbose
   
   解压后的目录结构如下：
   D:\WSL\kali.zip
   	|____ AppxMetadata
   	|____ [Content_Types].xml
   	|____ AppxBlockMap.xml
   	|____ AppxSignature.p7x
   	|____ DistroLauncher-Appx_1.1.9.0_scale-100.appx
   	|____ DistroLauncher-Appx_1.1.9.0_scale-125.appx
   	|____ DistroLauncher-Appx_1.1.9.0_scale-150.appx
   	|____ DistroLauncher-Appx_1.1.9.0_scale-400.appx
   	|____ DistroLauncher-Appx_1.1.9.0_x64.appx
   ```

   64位的系统，直接选择DistroLauncher-Appx_1.1.9.0_x64.appx这个文件把后缀名改为zip，然后再解压这个ZIP包。解压以后在文件夹里应该有个kali.exe的文件，先不要点击这个这个exe文件。

8. 安装前的准备

   安装之前需要升级以下WSL，直接下载 https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi ，然后安装即可。

9. 安装

   完成上面所有的步骤以后，点击kali.exe，进行安装。安装的过程中输入用户名和用户密码。

10. 升级

    1. 安装完成以后如果直接升级，会有签名错误，具体原因暂时不知道，因此需要安装一个archive keys，具体方式如下：

       ~~~bash
       wget -O kali-archive-keyring_2022.1_all.deb https://http.kali.org/kali/pool/main/k/kali-archive-keyring/kali-archive-keyring_2022.1_all.deb  --no-check-certificate
       ~~~

    2. 执行下载完成的deb文件

       ~~~bash
       sudo dpkg -i kali-archive-keyring_2022.1_all.deb
       ~~~

    3. 修改 /var/lib/dpkg/info/libc6\:amd64.postinst 文件，否则升级会失败

       ~~~bash
       sudo vi /var/lib/dpkg/info/libc6\:amd64.postinst
       
       注释掉 set -e 这一行
       # set -e
       ~~~

    4. 执行升级命令

       ~~~bash
       sudo apt-get update && sudo apt-get upgrade
       ~~~

       

    

    

