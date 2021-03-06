---
title: flash
layout: post
date: 2017-11-29 10:10:28 +0800
categories: [硬件]
tags: flash 闪存 内存
---


* content
{:toc}                                                                                                          




- [解述DRAM、SDRAM及DDR SDRAM的概念](http://www.dzsc.com/data/2014-8-27/106499.html)
- [华邦WINBOND SPI FLASH](http://www.victor-ic.cn/cn/products/20160307155758.html)



### flash

#### Winbond Serial Flash系列产品特点：                                                                                                               

- 体积小— 由于串行flash采用比并行flash更少的连线在一个系统中传送数据，所以缩小的系统板的空间，能常采用SOIC8、DFN、BGA等封装；
- 低功耗 — 串行SPI FLASH器件采用串行接口进行连续数据存取，所以可以达到工作电流：7mA  待机电流：8uA； 
- 工作温度范围宽 - 最新的Q版本工作温度在-40 to +105；
- 快速擦除烧录程式 —通过4KByte统一Sector-Erase,32Kbyte或者64Kbyte Block-Erase或者Chip- Erase能力，目前已达到数据擦除：18mS；  
- 成本低，用途广 - 由于是台湾生产与加工，无形中降低了很多成本。广泛应用于数码类以及消费类电子产品中；  
- 产品线长 - 串行SPI FLASH产品提供512Kb - 128Mb的密度、I2C、Microwire 和 SPI 兼容协议。 

##### Winbond Serial Flash系列规格参数

| Part No. | Density | Voltage | Temp Range | Max Freq | Package(s) |
| ------ | ------ | ------ | ------ | ------ |------ |
| W25X05CL | 512Kb(64KB*8) | 2.3V-3.6V | -40°C to +85°C | 104MHz(208Mhz Dual-SPI) | SOIC8(150mil) TSSOP8(173mil) WSON8(6*5mm) USON8(2*3mm) |
| W25X10CL | 1Mb(128KB*8) | 2.3V-3.6V | -40°C to +85°C | 104MHz(208Mhz Dual-SPI) | SOIC8(150mil) TSSOP8(173mil) WSON8(6*5mm) USON8(2*3mm) |
| W25X10BVSSIG | 1Mb(128KB*8) | 2.7V-3.6V | -40°C to +85°C | 104MHz(208Mhz Dual-SPI) | SOIC8(150mil) WSON8(6*5mm) |
| W25X10BL | 1Mb(128KB*8) | 2.3V-3.6V | -40°C to +85°C | 50MHz(100Mhz Dual-SPI) | SOIC8(150mil) WSON8(6*5mm) |
| W25X20CL | 2Mb(256KB*8) | 2.3V-3.6V | -40°C to +85°C | 104MHz(208Mhz Dual-SPI) | SOIC8(150mil) TSSOP8(173mil) WSON8(6*5mm) USON8(2*3mm) |
| W25X20BVSSIG | 2Mb(256KB*8) | 2.7V-3.6V | -40°C to +85°C | 104MHz(208Mhz Dual-SPI) | SOIC-8(150mil) WSON8(6*5mm) |
| W25X20BL | 2Mb(256KB*8) | 2.3V-3.6V | -40°C to +85°C | 50MHz(100Mhz Dual-SPI) | SOIC8(150mil) WSON8(6*5mm) |
| W25X20BW | 2Mb(256KB*8) | 1.65V-1.95V | -40°C to +85°C | 80MHz(160/320Mhz Dual-SPI) | SOIC8(150mil) WSON8(6*5mm) |
| W25X40BVSSIG | 4Mb(512KB*8) | 2.7V-3.6V | -40°C to +85°C | 104MHz(208Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) PDIP8(300mil) |
| W25X40BL | 4Mb(512KB*8) | 2.3V-3.6V | -40°C to +85°C | 50MHz(100Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) PDIP8(300mil) |
| W25Q40BVSSIG | 4Mb(512KB*8) | 2.7V-3.6V | -40°C to +85°C | 80/104MHz(160/320Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) PDIP8(300mil) |
| W25Q40BL | 4Mb(512KB*8) | 2.3V-3.6V | -40°C to +85°C | 50MHz(100/200Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) PDIP8(300mil) |
| W25Q40BW | 4Mb(512KB*8) | 1.65V-1.95V | -40°C to +85°C | 80MHz(160/320Mhz Dual-SPI) | SOIC8(150mil) WSON8(6*5mm) |
| W25Q80BVSSIG | 8Mb(1MB*8) | 2.7V-3.6V | -40°C to +85°C | 80/104MHz(160/320Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) PDIP8(300mil) |
| W25Q80BL | 8Mb(1MB*8) | 2.3V-3.6V | -40°C to +85°C | 50MHz(100/200Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) PDIP8(300mil) |
| W25Q80BW | 8Mb(1MB*8) | 1.65V-1.95V | -40°C to +85°C | 80MHz(160/320Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) |
| W25Q16BVSSIG | 16Mb(2MB*8) | 2.7V-3.6V | -40°C to +85°C | 80/104MHz(160/320Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) SOIC16(300mil) WSON8(6*5mm) PDIP8(300mil) |
| W25Q32BVSSIG | 32Mb(4MB*8) | 2.7V-3.6V | -40°C to +85°C | 104/80MHz(208/320Mhz Dual-SPI) | SOIC8(208mil) SOIC16(300mil) WSON8(6*5mm) WSON8(8*6mm) PDIP8(300mil) TFBGA24(6*8mm) |
| W25Q64DWSTIM | 64Mb(8MB*8) | 1.65V-1.95V | -40°C to +85°C | 50MHz(100/200Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) SOIC16(300mil)WSON8(6*5mm) PDIP8(300mil) |
| W25Q64FVDAIG | 64Mb(8MB*8) | 2.3V-3.6V | -40°C to +85°C | 104MHz(208/416Mhz Dual-SPI) | SOIC8(150mil) SOIC8(208mil) WSON8(6*5mm) PDIP8(300mil) TFBGA24(6*8mm) |
| W25Q64BVFIG | 64Mb(8MB*8) | 2.7V-3.6V | -40 to +85 -40 to +105 | 104/80MHz(208/320Mhz Dual-SPI) | SOIC8(208mil) SOIC16(300mil) WSON8(6*5mm) WSON8(8*6mm) PDIP8(300mil) TFBGA24(6*8mm) |
| W25Q64BVSSIG | 64Mb(8MB*8) | 2.7V-3.6V | -40°C to +85°C | 104MHz(208/416Mhz Dual-SPI) | SOIC8(208mil) SOIC16(300mil) WSON8(6*5mm) WSON8(8*6mm) TFBGA24(6*8mm) |
| W25Q64BVSSIG | 64Mb(8MB*8) | 2.7V-3.6V | -40°C to +85°C | 80MHz(160/320MhzDual/Quad-SPI) | SOIC8(208mil) SOIC16(300mil) WSON8(8*6mm) PDIP8(300mil) |
| W25Q64CVSSIG | 64Mb(8MB*8) | 2.7V-3.6V | -40 to +85 -40 to +105 | 80MHz(160/320MhzDual/Quad-SPI) | SOIC8(208mil) SOIC16(300mil) WSON8(6*5mm) PDIP8(300mil) TFBGA24(6*8mm) |
| W25Q128FVSSIG | 128Mb(16MB*8) | 2.7V-3.6V | -40°C to +85°C | 104MHz(208/416Mhz Dual-SPI) | SOIC8(208mil) SOIC16(300mil) WSON8(6*5mm) WSON8(8*6mm) TFBGA24(6*8mm) |
| W25Q128BVFG | 128Mb(16MB*8) | 2.7V-3.6V | -40 to +85 -40 to +105 | 104/70MHz(208/280Mhz Dual-SPI) | SOIC16(300mil) WSON8(8*6mm) TFBGA24(6*8mm) |
| W25Q256BVFG | 256Mb(32MB*8) | 2.7V-3.6V | -40°C to +85°C | 104/80MHz(208/320Mhz Dual-SPI) | SOIC16(300mil) WSON8(8*6mm) TFBGA24(6*8mm) |


### 内存容量说识别

#### （1）HYUNDAI（现代）

现代的内存颗粒现在都改名为Hynix了，不过旧颗粒上还是印有HYUNDAI的标志。其SDRAM芯片编号格 式 为：HY 5<1> <2> V <345> <67> <8> <9> <10> <11.12>-<13.14> s， 具体的编号含义可以在现代的网站上找到：
其中HY代表现代的产品：
- 5<1>表示芯片类型(57=SDRAM，5D=DDR SDRAM)；
- <2> 代表工作电压(空白=5V，V=3.3V,U=2.5V)；
- <345> 代表容量和刷新速度（16=16Mbits、4K Ref，64=64Mbits、8K Ref，65=64Mbits、 4K Ref，128=128Mbits、8K Ref，129=128Mbits、4K Ref，256=256Mbits、 16K Ref，257=256Mbits、8K Ref）；
- <67> 代表芯片输出的数据位宽（40、80、16、32分别代表4位、8位、16位和32位）；
- <8> 代表内存芯片内部由几个Bank组成（1、2、3分别代表2个、4个和8个Bank，是2的幂次关系）；
- <9> 代表接口（0=LVTTL［Low Voltage TTL］接口）；
- <10> 代表内核版本（可以为空白或A、B、C、D等字母，越往后代表内核越新）代表功耗（L=低功耗芯片，空白=普通芯片）；
- <11.12>代表封装形式（JC=400mil SOJ，TC=400mil TSOP-Ⅱ，TD=13mm TSOP-Ⅱ，TG=16mm TSOP-Ⅱ）；
- <13.14>代表速度（7=7ns［143MHz］，8=8ns［125MHz］，10p=10ns［PC-100 CL2或 3］，10s=10ns［PC-100 CL3］，10=10ns［100MHz］，12=12ns［83MHz］，15=5ns［66MHz］）。
另外，以前LGS(LG Semicon)也是韩国的一大内存芯片厂商，但后来被HY兼并。LGS SDRAM内存芯片编号格式 为：GM72V <12> <34> <5> 1 <6> <7> <8> <9.10>
其中GM代表LGS的产品；72代表SDRAM；V代表3.3V；<12>代表容量(16=16Mbits，66=64Mbits)；& lt;34>表示数据位宽(一般为4、8、16等)；<5>代表Bank（2=2个Bank，4=4个Bank）；1代表I/O接 口：LVTTL；<6>表示内核版本；<7>代表功耗（L=低功耗，空白=普通）；<8>代表封装（T=常见的 TSOPⅡ封装，I=BLP封装）；<9.10>代表速度 （7.5=7.5ns［133MHz］，8=8ns［125MHz］，7K=10ns［PC-100 CL2或 3］，7J=10ns［100MHz］，10K=10ns［100MHz］，12=12ns［83MHz］，15=15ns［66MHz］）。
以上的内存编号只是属于旧编号，下面是现代最新编号的规则，不过大体上还是相同，这里就不再重复了。

#### （2）SEC（Samsung Electronics，三星）

三星内存也是市场上常见的内存芯片之一 。三星SDRAM内存芯片编号规则可以看看下面详细的列表：
除了DRAM，我们还可以了解一下RDRAM的芯片编号：
三星RAMBUS DRAM内存芯片编号，以KM418RD8C为例：
- KM表示三星内存；
- 4代表RAM种类（4=DRAM）；
- 18代表内存芯片组成x18（16 = x16、18 = x18）；
- RD表示产品类型（RD=Direct RAMBUS DRAM）；
- 8代表内存芯片密度8M（4 = 4M、8 = 8M、16 = 16M）；
- C代表封装类型（C = 微型BGA、D =微型BGA [逆转CSP]、W = WL-CSP）；
- 80代表速度（60 = 600Mbps、80 = 800Mbps）。即三星8M*18bit=144M，BGA封装，速度800Mbps。

#### （3）Kingmax(胜创)

Kingmax内存采用TinyBGA的封装方式，所以芯片大小是TSOP封装内存的三分之一。在同等空间下TinyBGA封装可以将存储容量提高三 倍，而且体积更小、更薄，其金属基板到散热体的最有效散热路径仅有0.36mm，线路阻抗也小，因此具有良好的超频性能和稳定性。
Kingmax SDRAM内存目前有PC150、PC133、PC100三种。其中PC150内存实际上是能上150外频且能稳定在CL=3（有些能上 CL=2）的极品PC133内存条，该类型内存的REV1.2版本主要解决了与VIA 694X芯片组主板兼容问题，因此要好于REV1.1版本。
KINGMAX PC150内存最后两位编号为-6，PC-133内存最后两位编号为-07；而PC100内存芯片有两种情况：部分是-8的（例如编号KSV884T4A0-08），部分是-7的（例如编号KSV884T4A0-07）。

#### （4）Geil(金邦、原樵风金条)

金邦金条分为“金、红、绿、银、蓝”五种内存条，各种金邦金条的SPD均是确定的，对应不同的主板。其中：
- 红色金条是PC133内存；
- 金色金条针对PC133服务器系统，适合双处理器主板；
- 绿色金条是PC100内存；
- 蓝A色金条针对AMD750/760 K7系主板，面向超频玩家；
- 蓝V色金条针对KX133主板；蓝T色金条针对KT-133主板；
- 银色金条是面向笔记本电脑的PC133内存。
- 金邦内存芯片编号：GL2000 GP 6 LC 16M8 4 TG -7 AMIR 00 32
- 其中GL2000代表芯片类型
- GL2000=千禧条TSOPs即小型薄型封装，金SDRAM=BLP；
- GP代表金邦科技的产品；
- 6代表产品家族（6=SDRAM）；
- LC代表处理工艺（C=5V Vcc CMOS，LC=0.2微米3.3V Vdd CMOS，V=2.5V Vdd CMOS）；
- 16M8是设备号码（深度*宽度，内存芯片容量 = 内存基粒容量 * 基粒数目 = 16 * 8 = 128Mbit，其中16 = 内存基粒容量；
- 8 = 基粒数目；
- M = 容量单位，无字母=Bits，K=KB，M=MB，G=GB）；
- 4表示版本；
- TG是封装代码（DJ=SOJ，DW=宽型SOJ，F=54针4行FBGA，FB=60针8*16 FBGA，FC=60针11*13
- FBGA，FP=反转芯片封装，FQ=反转芯片密封，F1=62针2行FBGA，F2=84针2行FBGA，LF=90针FBGA，LG=TQFP，R1=62针2行微型FBGA，R2=84针2行微型FBGA，TG=TSOP（第二代），U=μ BGA）；
- -7是存取时间（7=7ns（143MHz））；
AMIR是内部标识号。
以上编号表示金邦千禧条，128MB，TSOP II封装，0.2微米3.3V，Vdd CMOS制造工艺，7ns、143MHz速度。

#### （5）Winbond(华邦)

Winbond(华邦)的显存编号和现代差不多，其编号规格为：W <12> <34> <56> <78>
W代表Winbond；
- <12>代表显存类型，98为SDRAM，94为DDRRAM；
- <34>代表颗粒的容量（08=8Mbits，16=16Mbits，64=64Mbits，12=128Mbits，25=256Mbits）
- <56>代表颗粒的位宽（16=16bit，32=32bit，G6=16bitBGA封装，G2=32bitBGA封装 ）
- <78>代表颗粒的版本号和封装（7代表版本号，常见的版本号为B和H；8代表封装，H为TSOP封装，B为BGA封装，D为LQFP封装）
通过以上就改好了。
好了之后用JTAG刷好CFE，刷好CFE之后用TFTP刷固件。

### 内存颗粒型号识别

 | MB | Samsung 三星 | ETRON钰创 | Zentel力积 | Hynix海力士 | Elpida尔必达 | Nanya南亚 | Micron镁光 | Winbond华邦 | ESMT晶豪 | ISSI | 
 | ------ | ------ | ------ | ------ | ------ | ------ | | ------ | ------ | ------ | ------ | ------ | 
 | | SD-DDR部分 | | | | | | | | | | 
 | 2MB | N/A | EM636165TS-6G | | N/A | N/A | N/A | N/A | W9816G6IH | M12L16161A | IS42S16100F | 
 | 8M | K4S641632N- | EM638165TS-6G | A3V64S39FTP | HY57V641620 E/FT | EDS6416AHTA- | N/A | MT48LC4M16A 2T | W9864G6J | M12L64164A | IS42S16400 F | 
 | 16M | K4S2816320- | EM639165TS-6G | A3V28S40FTP | HY57V1262G F/T R- 60/70 | EDS2816AHTA- | NT5SV8M16FS | MT48LC8M16A 2T | W9812G6J | M12L128168A | IS42S16800 E | 
 | 32MB | K4S561632N- | EM63A165TS-6G | A3V56S40FTP | HY57V2562G F/T R- 60/70C | EDS2516AFTA- | NT5SV16M16B | MT48LC16M16 A2 | W982S06J | M12L2561616A | IS42S16160 B | 
 | | DDR部分 | | | | | | | | | | 
 | 16M | K4H281638L-LCCC | EM6A9160TSA-5GA | 3S28D39FTP | H5DU1262GT R- | EDD1216AJTA- | NT5DS8M16HS | N/A | W9412G6J | M13S1281616 | AIS43R16800 C | 
 | 32M | K4H661638N- | EM6AA160TSB-5G | A3S56D40FTP | H5DU2562GF/ TR- | EDD2516AFTA- | NT5DS16M16C | MT46V16M16P /T | W9425G6J | M13S2561616A | IS43R16160 B | 
 | 64M | K4H511638G/J- | EM6AB160TSD-5G | | H5DU5162EF/ TR- | EDD5116AHTA- | NT5DS32M16B | MT46V32M16FN | | M13S5121632A | IS43R16320 D | 
 | | DDR2部分 | | | | | | | | | | 
 | 32M | K4T56163QI- | EM68A16CWQB- | A3R56E40ABF | | EDE5616AJBG-8E- | NT5TU16M16A | MT47H16M16B/F G | W9725G6JB | M14D2561616 | IS43DR1616 0 | 
 | 64M | K4T51163QI- | EM68B16CWQD-25H | A3R12E40CBF | H5PS5162F/GFR- Y5/S5/S6 | EDE5116AJBG-8E- | NT5TU32M16C | MT47H32M16HR | W9751G6JB | M14D5121632 | IS43DR1632 0 | 
 | 128M | K4T1G164QF- | EM68C16CWVB | A3R1GE40HBF | H5PS1G63EF R- | EDE1116AEB6- | NT5TU64M16G | MT47H64M16HR | W971GG6JB | M14D1G1632A | IS43DR1664 0 | 
 | 256M | | | | | | | | W972GG6JB | | | 
 | | DDR3部分 | | | | | || | | | | 
 | 128MB | K4B1G1646G-BCH9 | EM6GC16EWX/ EM6HC16EW | A3T1GF30ABF | H5TQ1G63B/D FR-H9/H8/PA | | NT5CC64M16D / NT5CB | MT41J64M16JT | W631GG6KB/ W641GG2KB | M15F1G166 4A | | 
 | 256MB | K4B2G1646C-HCH9 | EM6GD16EWX/ EM6HD16EW | | H5TQ2G63BF R- | EDJ2116DEBG | NT5CC128M16 / NT5CB | MT41J128M16HA | W632GG6KB | M15F2G166 4A | | 
 | 512MB | | EM6GE16EWX/ EM6HE16EWXC | | H5TQ4G63AFR | | NT5CC256M16BP / NT5C | | | | 
