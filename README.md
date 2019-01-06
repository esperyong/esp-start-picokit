# esp-start-picokit
用于学习原生C的esp-picokit开发板的demo程序

安装esp开发环境：

1.自己构建开发环境

a.使用homebrew安装构建所需要的工具：
```
brew install gnu-sed gawk binutils gperftools gettext wget help2man libtool autoconf automake
```

```
mkdir ~/esp/
```

创建大小写敏感的文件系统镜像:
```
hdiutil create ~/esp/crosstool.dmg -volname "ctng" -size 10g -fs "Case-sensitive HFS+"
```

挂载：
```
hdiutil mount ~/esp/crosstool.dmg
```
这时候在Finder里也可以看到一个叫做ctng的磁盘被挂载

创建指向工作目录的符号链接:
```
mkdir -p ~/esp
ln -s /Volumes/ctng ~/esp/ctng-volume
```

进入新创建的工作目录,下载 crosstool-NG 然后编译:
```
cd ~/esp/ctng-volume
git clone -b xtensa-1.22.x https://github.com/espressif/crosstool-NG.git
cd crosstool-NG
```

需要执行下面两行，否则构建时会报错：
```
export PATH="/usr/local/opt/binutils/bin:$PATH"
wget http://downloads.sourceforge.net/project/expat/expat/2.1.0/expat-2.1.0.tar.gz
```

编译工具链:
```
./bootstrap && ./configure --enable-local && make install
./ct-ng xtensa-esp32-elf
./ct-ng build
chmod -R u+w builds/xtensa-esp32-elf
```

编译得到的工具链会被保存到 
```
~/esp/ctng-volume/crosstool-NG/builds/xtensa-esp32-elf
```

然后将工具链加入到PATH中，这样在终端会话中就可以使用了：
```
export PATH=$HOME/esp/xtensa-esp32-elf/bin:$PATH
```
或者
```
alias get_esp32="export PATH=$HOME/esp/xtensa-esp32-elf/bin:$PATH"
```

也可以直接下载工具链：
https://dl.espressif.com/dl/xtensa-esp32-elf-osx-1.22.0-80-g6c4433a-5.2.0.tar.gz

```
mkdir -p ~/esp
cd ~/esp
tar -xzf ~/Downloads/xtensa-esp32-elf-osx-1.22.0-80-g6c4433a-5.2.0.tar.gz
```

工具链（包括用于编译和构建应用程序的程序）安装完后，你还需要 ESP32 相关的 API/库。API/库在 ESP-IDF 仓库 中。
```
cd ~/esp
git clone --recursive https://github.com/espressif/esp-idf.git
export IDF_PATH=~/esp/esp-idf
printenv IDF_PATH
virtualenv --distribute venv
./venv/bin/activate
pip install -r $IDF_PATH/requirements.txt
```

```
cp -r $IDF_PATH/examples/get-started/hello_world .
```
在继续后续操作前，先将 ESP32 开发板连接到 PC，然后检查串口号，看看它能否正常通信。如果你不知道如何操作，请查看 Establish Serial Connection with ESP32
https://docs.espressif.com/projects/esp-idf/zh_CN/latest/get-started/establish-serial-connection.html
中的相关指导。请注意一下端口号，我们在下一步中会用到。


一下两句很关键，一个是将工具链加入到PATH，一个是将API库环境变量加载到系统中。
```
export PATH=$HOME/esp/xtensa-esp32-elf/bin:$PATH
export IDF_PATH=~/esp/esp-idf
```

现在可以编译和烧写应用程序了，执行指令：
```
make flash
```
这条命令会编译应用程序和所有的 ESP-IDF 组件，生成 bootloader、分区表和应用程序 bin 文件，并将这些 bin 文件烧写到 ESP32 板子上。
如果没有任何问题，在编译过程结束后将能看到类似上面的消息。最后，板子将会复位，应用程序 “hello_world” 开始启动。
如果要查看 “hello_world” 程序是否真的在运行，输入命令 make monitor。这个命令会启动 IDF Monitor 程序：

```
$ make monitor
MONITOR
--- idf_monitor on /dev/ttyUSB0 115200 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
ets Jun  8 2016 00:22:57

rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
ets Jun  8 2016 00:22:57
...
```

在启动消息和诊断消息后，你就能看到 “Hello world!” 程序所打印的消息：
```
Hello world!
Restarting in 10 seconds...
I (211) cpu_start: Starting scheduler on APP CPU.
Restarting in 9 seconds...
Restarting in 8 seconds...
Restarting in 7 seconds...
```
要退出监视器，请使用快捷键 Ctrl+]。

你已完成 ESP32 的入门！

现在你可以尝试其他的示例工程 examples，或者直接开发自己的应用程序。

























