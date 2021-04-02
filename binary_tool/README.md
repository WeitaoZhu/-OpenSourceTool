## [映像文件工具srec主页](http://srecord.sourceforge.net/)  
## 介绍与帮助[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1953779441)


1.  MDK提供的简单[例子](http://www.keil.com/support/docs/2217.htm)
2.  官方的[例子](http://srecord.sourceforge.net/man/man1/srec_examples.html)
3.  在线hex，bin的[工具](https://hexed.it/)
4.  arm文件[格式](https://www.cnblogs.com/henjay724/p/8426744.html)

开源工具`SRecord`能够解析转换多种映像文件，`Binary，Intel,intel_16`等数十种文件.

-   srec_cat 转换，合并文件
-   srec_cmp 比较文件，可以比较hex和bin
-   srec_info 查看文件信息

## 常用例子[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1401244380)

### 常用选项[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1321354734)

1.  Disable\_Sequence\_Warnings 这个是因为如果输入的Intel HEX文件的记录未按升序地址顺序排序，则此选项会禁止发出警告。由OH51，OHX51，OH251或OH166生成的HEX文件未按升序地址顺序排序。
2.  address-length =2或3或4  
    指定Intel HEX输出文件中的地址字节数。默认情况下，srec_cat会生成扩展地址记录（类型04），地址范围最大为4GB。如果要避免生成扩展地址记录，请在指定输出文件之前指定 address-length = 2。这限制了最大值。地址空间为64K，可用于大多数8051应用程序。
3.  Output\_Block\_Size = ByteCount  
    指定每个HEX记录的长度。默认情况下，srec\_cat生成包含最多32个字节数据的行。如果你想限制最大值。行长为16字节（与OH51，OHX51，OH251或OH166兼容），使用-Output\_Block_Size = 16。

### 一个实际的例子[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1573823536)

```bash

if exist ..\\*256* ( 
 set lenth="0x40000"
) else (
 set lenth="0x80000"
)


rm *.bin
rm *.hex
 copy  ..\app.hex app.hex
 copy  ..\boot.hex boot.hex
 md HEX阅读器
 srec_cat.exe -o boot.bin -binary boot.hex -Intel -fill 0xff 0x0000 0x3000
 srec_cat.exe -o app.bin -binary app.hex -Intel  -fill 0xff 0x3000 %lenth%
 srec_cat.exe -o ok_0xFF.bin -binary boot.bin -binary -fill 0xff 0x00000 0x3000 app.bin -binary -crop 0x3000 %lenth%  -fill 0xFF 0x3000 %lenth%
 srec_cat.exe -o ok_0xFF.hex -Intel ok_0xFF.bin -binary -Output_Block_Size=16
 srec_cat.exe -o ok.hex -Intel boot.hex -Intel app.hex -Intel -Output_Block_Size=16
 srec_info.exe  boot.hex -Intel app.hex -Intel  ok_0xFF.hex -Intel ok.hex -Intel  >info.txt 
 hexcrc.exe ok_0xFF.hex
 srec_cat.exe -o  HEX阅读器\app.hex.txt -Hexdump  app.hex -Intel
 srec_cat.exe -o  HEX阅读器\boot.hex.txt -Hexdump boot.hex -Intel
 srec_cat.exe -o  HEX阅读器\ok_0xFF.hex.txt -Hexdump ok_0xFF.hex -Intel

```

### hex转bin[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#2015175990)

```bash
srec_cat.exe test.hex -intel -offset -0x3000 -o .out.bin -binary
```

1.  如果不指定-offset -0xxxx 则bin从0开始
    
2.  keil生成的51的hex，地址并不是递增的，会有如下警告，加入`-Disable_Sequence_Warning`选项在exe后
    
    ```bash
    srec_cat.exe  12864.hex -intel -o a.bin -Binary
    srec_cat: 12864.hex: 11: warning: data records not in strictly ascending order
        (expected >= 0x0188, got 0x012C)
    ```
    
    正确用法
    
    ```bash
    srec_cat.exe -Disable_Sequence_Warning 12864.hex -intel  -o .out.bin -binary
    ```
    

### 数据填充[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#3948367040)

```bash
-fill 0xFF 0x0000 0x8000
```

具体可以看下文件合并的例子

### 文件合并[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1805759512)

[参考](https://www.cnblogs.com/yangfengwu/p/9337033.html)

```bash
srec_cat -output "1.bin" -binary 0x00000.bin -binary -fill 0xff 0x00000 0x10000 0x10000.bin -binary -offset 0x10000

srec_cat.exe HexFile1.hex -Intel HexFile2.hex -Intel -o MergedHexFile.hex -Intel
```

1.  `-output "1.bin" -binary`指定输出文件
2.  `0x00000.bin -binary`第一个输入文件
3.  `-fill 0xff 0x00000 0x10000`先把0x00000到0x10000全部填充0xff(其实内部会先生成一个用于存储的文件)
4.  `0x10000.bin -binary -offset 0x10000`，找到第二个bin文件把自己的地址偏移到0x10000开始

其他例子http://www.keil.com/support/docs/1144.htm

### 文件分割[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#427494985)

详细例子[http://www.keil.com/support/docs/963.htm](http://www.keil.com/support/docs/963.htm)

```bash
srec_cat.exe HexFile.hex -Intel -crop 0x000000 0x007FFF -o HexFilePart1.hex -Intel
srec_cat.exe HexFile.hex -Intel -crop 0x008000 0x00FFFF -offset -0x008000 -o HexFilePart2.hex -Intel
```

另一个稍微复杂的

Content of**SplitHexPart1.cmd**command file:

```bash
# BL51 hex files are not sorted for ascending addresses. Suppress this warning
-disable-sequence-warning
# take HexFile.hex address area 0x000000-0x007FFF
.\OBJ\HexFile.hex -Intel -crop 0x000000 0x007FFF
#generate hex records with 16 byte data length (default 32 byte)
-Output_Block_Size=16
# generate a Intel hex file
-o .\OBJ\HexFilePart1.hex -Intel
```

Content of**SplitHexPart2.cmd**command file:

```bash
# BL51 hex files are not sorted for ascending addresses. Suppress this warning
-disable-sequence-warning
# take HexFile.hex address area 0x008000-0x00FFFF and shift it down to 0x000000
.\OBJ\HexFile.hex -Intel -crop 0x008000 0x00FFFF -offset 0x008000
#generate hex records with 16 byte data length (default 32 byte)
-Output_Block_Size=16
# generate a Intel hex file
-o .\OBJ\HexFilePart2.hex -Intel
```

Invocation of srec_cat.exe with two command files:

```bash
srec_cat.exe @SplitHexPart1.cmd
srec_cat.exe @SplitHexPart2.cmd
```

参考MDK计算CRC的文档[an277.pdf](http://www.keil.com/appnotes/files/an277.pdf)

```bash
srec_cat SelfTestROM_Ex2.hex -intel -crop 0x00000 0x0FFFC -fill 0x00 0x00000 0x0FFFC -crc32-l-e 0x0FFFC -o SelfTestROM.0.hex -intel
srec_cat SelfTestROM_Ex2.hex -intel -crop 0x10000 0x1FFFC -fill 0x00 0x10000 0x1FFFC -crc32-l-e 0x1FFFC -o SelfTestROM.1.hex -intel
srec_cat SelfTestROM_Ex2.hex -intel -crop 0x20000 0x2FFFC -fill 0x00 0x20000 0x2FFFC -crc32-l-e 0x2FFFC -o SelfTestROM.2.hex -intel
srec_cat SelfTestROM_Ex2.hex -intel -crop 0x30000 0x3FFFC -fill 0x00 0x30000 0x3FFFC -crc32-l-e 0x3FFFC -o SelfTestROM.3.hex -intel
```

### 加入CRC[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#93197007)

这里的意思是转换生成的bin，最后4字节存着前面到倒数4字节前的CRC32

参考MDK计算CRC的文档[an277.pdf](http://www.keil.com/appnotes/files/an277.pdf)，里面有单个文件的crc，还有将一个hex分割成四个区域的代码算4个crc的方式 ，[SelfTestROM代码链接](http://www.keil.com/appnotes/docs/apnt_277.asp)

```bash
srec_cat SelfTestROM_Ex2.hex -intel -crop 0x00000 0x0FFFC -fill 0x00 0x00000 0x0FFFC -crc32-l-e 0x0FFFC -o SelfTestROM.0.hex -intel
```

1.  `srec_cat SelfTestROM_Ex2.hex -intel`读取hex
    
2.  `-crop 0x00000 0x0FFFC -fill 0x00`读取的区域，没有的填充`0x00`
    
3.  `0x00000 0x0FFFC -crc32-l-e 0x0FFFC`计算CRC，CRC放在`0x0FFFC`
    
4.  代码中的定位
    
    ```cpp
    uint32_t crc_nominal __attribute__((at(0x0003FFFC)));
    #define ROM_START 0x00000000
    #define ROM_LEN   0x0003FFFC
    ```
    

### 查看信息[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1646360287)

```bash
srec_info.exe  SelfTestROM_Ex.hex -intel
```

### 使用命令集合的文本[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#2549985391)

1.  新建一个文本
    
    ```bash
    -Disable_Sequence_Warnings    #disable warning about hex records not in ascending order
    MyInputFile.hex               #input file name
    -Intel                        #read input file in intel hex format
    -fill 0xFF 0x0000 0x8000      #fill gaps from 0x0000-0x7FFF (32K) with value 0xFF
    -Output_Block_Size=16         #generate hex records with 16 byte data length (default 32 byte)
    -address-length=2             #generate 16-bit address records (no extended adress records type 04)
    -o MyOutputFile.hex           #output file name
    -Intel                        #generate intel hex output file
    ```
    
2.  使用命令`srec_cat.exe @CommandFile`
    

## 详细文件格式的描述[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#3776910186)

-   [_srec_aomf_(5)](http://srecord.sourceforge.net/man/man5/srec_aomf.html)
-   [_srec\_ascii\_hex_(5)](http://srecord.sourceforge.net/man/man5/srec_ascii_hex.html)
-   [_srec\_atmel\_generic_(5)](http://srecord.sourceforge.net/man/man5/srec_atmel_generic.html)
-   [_srec_binary_(5)](http://srecord.sourceforge.net/man/man5/srec_binary.html)
-   [_srec_brecord_(5)](http://srecord.sourceforge.net/man/man5/srec_brecord.html)
-   [_srec_coe_(5)](http://srecord.sourceforge.net/man/man5/srec_coe.html)
-   [_srec_cosmac_(5)](http://srecord.sourceforge.net/man/man5/srec_cosmac.html)
-   [_srec\_dec\_binary_(5)](http://srecord.sourceforge.net/man/man5/srec_dec_binary.html)
-   [_srec_emon52_(5)](http://srecord.sourceforge.net/man/man5/srec_emon52.html)
-   [_srec_fairchild_(5)](http://srecord.sourceforge.net/man/man5/srec_fairchild.html)
-   [_srec_fastload_(5)](http://srecord.sourceforge.net/man/man5/srec_fastload.html)
-   [_srec\_formatted\_binary_(5)](http://srecord.sourceforge.net/man/man5/srec_formatted_binary.html)
-   [_srec_forth_(5)](http://srecord.sourceforge.net/man/man5/srec_forth.html)
-   [_srec_fpc_(5)](http://srecord.sourceforge.net/man/man5/srec_fpc.html)
-   [_srec_idt_(5)](http://srecord.sourceforge.net/man/man5/srec_idt.html)
-   [_srec_intel_(5)](http://srecord.sourceforge.net/man/man5/srec_intel.html)
-   [_srec_intel16_(5)](http://srecord.sourceforge.net/man/man5/srec_intel16.html)
-   [_srec_logisim_(5)](http://srecord.sourceforge.net/man/man5/srec_logisim.html)
-   [_srec_mem_(5)](http://srecord.sourceforge.net/man/man5/srec_mem.html)
-   [_srec_mif_(5)](http://srecord.sourceforge.net/man/man5/srec_mif.html)
-   [_srec\_mips\_flash_(5)](http://srecord.sourceforge.net/man/man5/srec_mips_flash.html)
-   [_srec\_mos\_tech_(5)](http://srecord.sourceforge.net/man/man5/srec_mos_tech.html)
-   [_srec_motorola_(5)](http://srecord.sourceforge.net/man/man5/srec_motorola.html)
-   [_srec_msbin_(5)](http://srecord.sourceforge.net/man/man5/srec_msbin.html)
-   [_srec_needham_(5)](http://srecord.sourceforge.net/man/man5/srec_needham.html)
-   [_srec_os65v_(5)](http://srecord.sourceforge.net/man/man5/srec_os65v.html)
-   [_srec_ppb_(5)](http://srecord.sourceforge.net/man/man5/srec_ppb.html)
-   [_srec_ppx_(5)](http://srecord.sourceforge.net/man/man5/srec_ppx.html)
-   [_srec_signetics_(5)](http://srecord.sourceforge.net/man/man5/srec_signetics.html)
-   [_srec_spasm_(5)](http://srecord.sourceforge.net/man/man5/srec_spasm.html)
-   [_srec_spectrum_(5)](http://srecord.sourceforge.net/man/man5/srec_spectrum.html)
-   [_srec_stewie_(5)](http://srecord.sourceforge.net/man/man5/srec_stewie.html)
-   [_srec_tektronix_(5)](http://srecord.sourceforge.net/man/man5/srec_tektronix.html)
-   [_srec\_tektronix\_extended_(5)](http://srecord.sourceforge.net/man/man5/srec_tektronix_extended.html)
-   [_srec\_ti\_tagged_(5)](http://srecord.sourceforge.net/man/man5/srec_ti_tagged.html)
-   [_srec\_ti\_tagged_16_(5)](http://srecord.sourceforge.net/man/man5/srec_ti_tagged_16.html)
-   [_srec\_ti\_txt_(5)](http://srecord.sourceforge.net/man/man5/srec_ti_txt.html)
-   [_srec_trs80_(5)](http://srecord.sourceforge.net/man/man5/srec_trs80.html)
-   [_srec_vmem_(5)](http://srecord.sourceforge.net/man/man5/srec_vmem.html)
-   \[_srec_wilson_(5)\](

## 附录:MDK的例子（谷歌翻译的）[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#29000395)

### 用例[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#721729376)

请参阅以下知识库文章如何使用**srec_cat.exe**实用程序和Keil微控制器开发工具转换，合并和拆分HEX和BINARY文件 。

**一般HEX文件操作**

-   [概述：分类英特尔HEX文件](http://www.keil.com/support/docs/2217.htm)\- 生成一个英特尔HEX文件，其中：地址记录按升序排序，精确大小为32K，并用0xFF填充所有间隙
-   [概述：](http://www.keil.com/support/docs/1187.htm)生成具有偶数个字符的HEX文件 - 生成一个Intel HEX文件，其中包含：偶数地址，每个记录中的偶数个字节，所有间隙都具有填充0xFF的奇数地址
-   [μVision：HEX文件的起始和结束地址没有效果](http://www.keil.com/support/docs/991.htm)\- 生成一个精确大小为64K的Intel HEX文件并填充所有间隙0xFF
-   [一般：组合INTEL HEX文件的实用程序](http://www.keil.com/support/docs/1940.htm)\- 合并两个没有地址重叠的HEX文件，或者两者之间有间隙
-   [一般：将两个应用程序](http://www.keil.com/support/docs/2666.htm)合并到[一个英特尔六进制文件中](http://www.keil.com/support/docs/2666.htm)\- 合并两个没有地址重叠的HEX文件，或者两者之间存在间隙
-   [概述：](http://www.keil.com/support/docs/963.htm)分割[HEX文件](http://www.keil.com/support/docs/963.htm)\- 将一个HEX文件分成两个HEX文件，其中一个具有地址范围的下半部分，另一个具有移动到地址0x0的高地址范围

**用于8051设备的HEX文件操作**

-   [OH51：组合代码银行HEX文件](http://www.keil.com/support/docs/1144.htm)\- 使用不同方法组合多个hex文件
-   [LX51：](http://www.keil.com/support/docs/2349.htm)为银行[应用](http://www.keil.com/support/docs/2349.htm)程序生成[HEX文件](http://www.keil.com/support/docs/2349.htm)\- 64K代码库，每个库中重复公共区域

**ARM设备的HEX文件操作**

-   [ARM：将二进制映像包含到应用程序中](http://www.keil.com/support/docs/3770.htm)\- 组合二进制文件并将它们包含在项目中

**一般BINARY文件操作**

-   [一般：从二进制文件制作六进制文件](http://www.keil.com/support/docs/275.htm)
-   [一般：从六进制文件制作二进制文件](http://www.keil.com/support/docs/274.htm)

**其他应用**

-   [一般：从二进制或十六进制数据创建C文件](http://www.keil.com/support/docs/2218.htm)\- 从二进制文件生成.c和.h文件
-   [概述：创建MOTOROLA S-RECORD文件](http://www.keil.com/support/docs/276.htm)\- 从Intel Hex文件生成Motorola S-record文件
-   [μVision：CRC示例](http://www.keil.com/support/docs/3806.htm)使用ROM自检来执行ROM的启动和定期测试

### 教程[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1804706129)

设置工具以在每个项目构建/重建后执行**srec_cat.exe**。

1.  打开μVision
2.  选择下拉菜单**项目**\- >目标选项**，然后单击**用户\*\* 选项卡
3.  下面**生成后/重建**检查框**运行＃1**
4.  对于user命令，使用命令选项调用**srec_cat.exe**。该[键序列](http://www.keil.com/support/man/docs/uv4/uv4_ut_keysequence.htm)`#H`可用于输入十六进制文件名。
5.  例如：

```bash
srec_cat.exe #H -Intel -o SortedHexFile.hex -Intel
```

**注意**：如果需要许多参数，则使用命令文件会更容易。在μVision中使用srec_cat.exe的命令文件时，请确保将“@”字符加倍，否则μVision可能会将其解释为[键序列](http://www.keil.com/support/man/docs/uv4/uv4_ut_keysequence.htm)。

[![img](http://www.keil.com/support/docs/images/1940_user.png)](http://www.keil.com/support/docs/images/1940_user.png)

### 句法[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#712784542)

有关所有srec_cat.exe参数的完整说明，请参阅“**SRecord参考手册”**。以下是一些对Keil工具有用的选项：

-   **-Disable Sequence Warnings**  
    禁止显示输入文件的HEX记录未按升序地址顺序排序的警告消息。OH51，OHX51，OH251或OH166无法按地址升序生成HEX文件。
-   **-address-length =**2或3或4  
    英特尔HEX输出文件中的地址字节数（默认为**4**：生成扩展地址记录（类型04），地址范围最大为4GB）。在命令字符串中，`address-length`必须在before之前`-o`（指定输出文件）。  
    _对于非代码库8051应用程序_：避免生成扩展地址记录。设置`-address-length=2`为将最大地址空间限制为64K。代码库应用程序可以大于64K。
-   **-Output Block Size =**ByteCount  
    每个HEX记录的长度（默认为**32**：行包含最多32个字节的数据）。  
    _对于C51，C251，C166应用_：设置 （与OH51，OHX51，OH251或OH166兼容）。`-Output*Block*Size=16`
-   **-fill**FillValue StartAddress EndAddress  
    使用指定的常量值填充未使用的区域。  
    _建议：_使用值**0xFF**\- 这对应于擦除闪存的值。
-   **-crop**StartAddress EndAddress  
    仅从上一个输入文件加载指定的地址区域。将此选项与`-offset`。结合使用。
-   **-offset**Offset  
    为上一个输入文件添加地址偏移量。允许正值或负值。将此选项与`-crop`。结合使用 。
-   **-Intel**  
    将前面的文件名视为Intel Hex，例如，\[input filename\]`-Intel`=将输入文件解释为Intel HEX，或者\[output filename\]`-Intel`=在Intel HEX中生成输出文件
-   **-Binary**  
    对待前面的文件名是二进制文件，例如，\[input filename\]`-Binary`=将输入文件解释为二进制文件，或者\[输出文件名\]`-Binary`=生成二进制文件的输出文件
-   **@CommandFile**  
    命令文件可以包含**srec_cat.exe**的部分或全部调用参数。  
    _提示_：使用“＃”开始一行，将所有内容评论到该行的末尾。
-   **-Motorola**  
    可以在输入或输出文件名之后使用，以指定输入文件应解释为Motorola S-Record文件，或输出文件应生成为Motorola S-Record文件。
-   **-C-Array**filename \[-INClude\]  
    可以在输出文件名之后使用，以指定它应该作为带有可选包含文件的C源文件生成。

### 更多信息[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#1263389516)

-   请参阅μVision用户指南中的**启动外部工具**。
-   请参阅**SRecord参考手册**
-   请参阅μVision用户指南中的**工具参数的关键顺序**。
-   [一般：英特尔HEX文件格式](http://www.keil.com/support/docs/1584.htm)
-   [μVision：为基于臂的设备创建Intel Hex文件](http://www.keil.com/support/docs/1759.htm)

### 也可以看看[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#3719849375)

-   [ULINK：使用ULINK作为设备编程器](http://www.keil.com/support/docs/3061.htm)
-   [ARMLINK：在建筑物内生成二元输出](http://www.keil.com/support/docs/3213.htm)
-   [ARMCC：Arm编译器输出格式](http://www.keil.com/support/docs/3206.htm)

### 另见8051[#](https://www.cnblogs.com/zongzi10010/p/10229223.html#3945308246)

-   请参阅**OH51用户指南**。
    
-   请参阅**OHX51用户指南**。
    
-   [LX51：为银行应用程序生成HEX文件](http://www.keil.com/support/docs/2349.htm)
    
-   [http://srecord.sourceforge.net/man/man5/srec_wilson.html](http://srecord.sourceforge.net/man/man5/srec_wilson.html))