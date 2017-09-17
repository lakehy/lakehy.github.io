---
layout: post
title: "在Linux上使用Makefile编译stm32程序"
description: ""
date: 2017-09-17 12:22:21 +0800
category: 
tags: []
---
# STM32的Linux开发环境设置

stm32开发常用的IDE开发环境，虽然比较方便使用，但是却有各种不便：

- ~~~MDK需要购买~~~
- ~~~MDK不支持Linux~~~
- TrueStudio启动太慢，启动后太消耗系统资源
- TrueStudio定期弹出广告
- 如果TrueStudio不是焦点窗口，弹广告之后有一个子窗口无法关闭，需要关闭IDE一次再重新打开 ->_->

那么**如何使用神器Vim+Gcc来进行自定义开发**呢？

arm公司已经提供了现成的arm-none-eabi开发工具链，只需要写好Makefile就可以了。

## 生成Makefile

由于使用的工程是用TrueStudio的模板来生成的，可以借助TrueStudio来生成Makefile模板，然后再根据实际情况修改一下。

在TrueStudio创建一个新工程，选择新的Embedded C Makefile工程，然后选择对应的MCU。

将生成的模板工程的Makefile复制到需要Makefile的工程源码目录，并修改对应配置，主要是link脚本路径，以及头文件路径：

```makefile
LINK_SCRIPT="stm32_flash.ld"
ASSEMBLER_FLAGS=-c -g -O0 -mcpu=cortex-m3  -mthumb  -D"STM32F10X_HD" -D"USE_STDPERIPH_DRIVER"  -x assembler-with-cpp  -Isrc -IOtherIncludes
COMPILER_FLAGS=-c -g -mcpu=cortex-m3  -O0 -Wall -ffunction-sections -fdata-sections -mthumb  -D"STM32F10X_HD" -D"USE_STDPERIPH_DRIVER"   -Isrc -IOtherIncludes
```

这些编译选项，在IDE中的工程环境配置中也有，直接复制过来即可。

## 安装编译工具

如果你已经安装了Atollic TrueStudio，可以使用 TrueStudio的arm编译工具链。

默认安装路径是`/opt/Atollic_TrueSTUDIO_for_ARM_x86_64_8.0.0`。将Atollic的arm编译工具添加到环境中：

    echo "export PATH=/opt/Atollic_TrueSTUDIO_for_ARM_x86_64_8.0.0/ARMTools/bin:\$PATH" > $HOME/.bashrc

如果你没有安装任何Linux上的STM32开发工具，可以安装arm提供的：

    sudo apt install binutils-arm-none-eabi gcc-arm-none-eabi gdb-arn-none-eabi libnewlib-arm-none-eabi libstdc++-arm-none-eabi

## 安装编辑器

到这里您就可以使用以下命令来安装Vim了：

    sudo apt install vim

如果想要使用现代化一点的图形界面工具，可以使用Visual Studio Code，一款在Github Atom基础上改写的一款文本编辑器，提供了如下好处：

- Team Explorer插件可以用来访问Team Foundation Server上的代码和Task。
- Git Lens插件(基于git blame功能)提供的逐行提交记录可以方便的查阅团队代码。
- 代码高亮，自动补全，代码纠错，等等，安装插件方便。

安装VS Code可以直接去官网下载deb包，或者使用最新版的ubuntu make一键安装。

    sudo add-apt-repository ppa:ubuntu-desktop/ubuntu-make
    sudo apt update
    sudo apt install ubuntu-make
    umake ide visual-studio-code

下载完成后安装需要的插件，打开源码目录。

## 配置编辑器

这是一个说不完的主题，简单来说，

- 使用vim请参考官方文档
- 使用VS Code，安装c/C++插件，添加索引目录

## 安装烧写工具

根据不同的下载接口，安装对应的烧写工具。

### 串口烧写

通过串口或I2C接口烧写，可以下载[stm32flash源码](https://github.com/ARMinARM/stm32flash.git)编译，ubuntu 15.04及以上用户可以直接安装：

    sudo apt install stm32flash
    stm32flash -w filename -v -g 0x0 /dev/ttyS0

### ST-Link烧写

使用ST-Link接口的烧写器，可以安装[stlink工具](https://github.com/texane/stlink.git)：

    git clone https://github.com/texane/stlink.git
    cd stlink
    sudo apt install libusb-1.0-0-dev cmake build-essenial
    make release
    cd build/Release; make install DESTDIR=$HOME/.local/bin
    echo "export PATH=\$HOME/.local/bin:\$PATH" > $HOME/.bashrc
    st-flash --format ihex write filename.hex

### 修改Makefile自动烧写

生成的Makefile模板默认生成的是elf格式的文件，如果想要生成hex或者bin格式，可以在Makefile中添加：

`Makefile`

```makefile
CC = arm-none-eabi-gcc
CP = arm-none-eabi-objcopy

OBJECT_DIR = build
BIN_DIR = $(OBJECT_DIR)
PROJ = test
ELF = $(PROJ).elf
HEX = $(PROJ).hex
BIN = $(PROJ).bin
MAP = $(PROJ).map

all: buildelf

buildelf: $(OBJS)
    $(CC) -o $(ELF) $(OBJS) $(LINKER_FLAGS)
    $(CP) -O ihex $(ELF) $(HEX)
    $(CP) -O binary $(ELF) $(BIN)

flash: $(HEX)
    st-flash --format ihex write $(HEX)

clean:
    $(RM) $(OBJS) $(ELF) $(HEX) $(BIN) $(MAP)
```

编译并烧写hex文件：

    make
    make flash

## 示例

这是一份修改后的示例Makefile:

```makefile
###############################################################################
#
#       File        : Makefile
#
#       Abstract    : Example Makefile for a C Project
#
#       Environment : Atollic TrueSTUDIO(R)
#
###############################################################################

# System configuration
CC = arm-none-eabi-gcc
CP = arm-none-eabi-objcopy
RM=rm -rf

# Define output directory
OBJECT_DIR = build
BIN_DIR = $(OBJECT_DIR)

# Output file names
PROJ = test
HEX = $(BIN_DIR)/$(PROJ).hex
BIN = $(BIN_DIR)/$(PROJ).bin
ELF = $(BIN_DIR)/$(PROJ).elf
MAP = $(BIN_DIR)/$(PROJ).map

# Assembler, Compiler and Linker flags and linker script settings
LINKER_FLAGS=-lm -mthumb -mcpu=cortex-m3  -Wl,--gc-sections -T$(LINK_SCRIPT) -static  -Wl,--start-group -lc -lm -Wl,--end-group  -specs=nosys.specs  -Wl,-cref "-Wl,-Map=$(MAP)" -Wl,--defsym=malloc_getpagesize_P=0x1000
LINK_SCRIPT="stm32_flash.ld"
ASSEMBLER_FLAGS=-c -g -O0 -mcpu=cortex-m3  -mthumb  -D"STM32F10X_HD" -D"USE_STDPERIPH_DRIVER"  -x assembler-with-cpp  -Isrc -IOtherIncludes
COMPILER_FLAGS=-c -g -mcpu=cortex-m3  -O0 -Wall -ffunction-sections -fdata-sections -mthumb  -D"STM32F10X_HD" -D"USE_STDPERIPH_DRIVER"   -Isrc -IOtherIncludes

# Define sources and objects
SRC := $(wildcard */*/*/*/*/*/*/*.c) \
    $(wildcard */*/*/*/*/*/*.c) \
    $(wildcard */*/*/*/*/*.c) \
    $(wildcard */*/*/*/*.c) \
    $(wildcard */*/*/*.c) \
    $(wildcard */*/*.c) \
    $(wildcard */*.c)
SRCSASM :=  $(wildcard */*/*/*/*/*/*/*/*.s) \
    $(wildcard */*/*/*/*/*/*/*.s) \
    $(wildcard */*/*/*/*/*/*.s) \
    $(wildcard */*/*/*/*/*.s) \
    $(wildcard */*/*/*/*.s) \
    $(wildcard */*/*/*.s) \
    $(wildcard */*/*.s) \
    $(wildcard */*.s)
OBJS := $(SRC:%.c=$(OBJECT_DIR)/%.o) $(SRCSASM:%.s=$(OBJECT_DIR)/%.o)
OBJS := $(OBJS:%.S=$(OBJECT_DIR)/%.o)

###############
# Build project
# Major targets
###############
all: buildelf

buildelf: $(OBJS)
    $(CC) -o $(ELF) $(OBJS) $(LINKER_FLAGS)
    $(CP) -O ihex $(ELF) $(HEX)
    $(CP) -O binary $(ELF) $(BIN)

flash: $(HEX)
    st-flash --format ihex write $(HEX)

clean:
    $(RM) $(OBJS) $(ELF) $(HEX) $(BIN) $(MAP)


##################
# Specific targets
##################
$(OBJECT_DIR)/src/main.o: src/main.c
    @mkdir -p $(dir $@) 2> /dev/null
    $(CC) $(COMPILER_FLAGS) src/main.c -o $(OBJECT_DIR)/src/main.o


##################
# Implicit targets
##################
$(OBJECT_DIR)/%.o: %.c
    @mkdir -p $(dir $@) 2> /dev/null
    $(CC) $(COMPILER_FLAGS) $< -o $@

$(OBJECT_DIR)/%.o: %.s
    @mkdir -p $(dir $@) 2> /dev/null
    $(CC) $(ASSEMBLER_FLAGS) $< -o $@

$(OBJECT_DIR)/%.o: %.S
    @mkdir -p $(dir $@) 2> /dev/null
    $(CC) $(ASSEMBLER_FLAGS) $< -o $@

```
