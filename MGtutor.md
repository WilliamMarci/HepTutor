# MG tutor


## 基本使用 Basic

### 初始设置 Setup

Requirements:
- python 2.7 or 3.7 (or higher)
- gfortran/gcc 4.6 or higher

> [!WARNING]
> 注意Python的版本不要太高了， 在python 3.12很多功能都还是实验性的
>

我们需要下载Madgraph: [Madgraph 2.9.23](https://launchpad.net/mg5amcnlo/lts/2.9.x/+download/MG5_aMC_v2.9.23.tar.gz)(长期稳定版) [Madgraph 3.6.2](http://launchpad.net/madgraph5/3.0/3.6.x/+download/MG5_aMC_v3.6.2.tar.gz)(比较新的版本)
```shell
wget https://launchpad.net/mg5amcnlo/lts/2.9.x/+download/MG5_aMC_v2.9.23.tar.gz
tar -zxf MG5_aMC_v2.9.23.tar.gz
cd MG5_aMC_v2_9_23
```

解压之后进入其目录, 应该能看到如下的文件结构
```
MG5_aMC_v2_9_23
├── aloha
├── bin         #程序在这里
├── HELAS       #这里有fortran的
├── input
├── INSTALL
├── LICENSE -> madgraph/LICENSE
├── madgraph
├── MadSpin     #MadSpin Onshell 衰变
├── mg5decay
├── models      #模型放在这里
├── PLUGIN
├── proc_card.dat
├── README
├── Template
├── tests
├── UpdateNotes.txt
├── vendor
└── VERSION
```

然后运行
```shell
./bin/mg5_aMC
```
稍等一会儿，此时我们就可以开始使用Madgraph了, 下面我们来介绍一些基本的命令.

### 导入模型 生成事件

我们在启动Madgraph时会默认导入`sm`模型, 也就是标准模型, 但是我们可以使用`import model`命令来导入其他的模型, 例如
```
import model sm-lepton_masses
```
具体是看`models`目录下有哪些模型。如果我们需要加入新的模型, 也只需要将解压其放入`models`目录下即可。

在Madgraph中, 生成事件的命令是`generate`, 其基本格式为
```
generate <process> [<options>]
```
process使用我们刚才导入的模型中的粒子，比如说在sm模型里就有基本粒子`e+ e- mu+ mu-`, 也有定义好的“多粒子” `p = g u c d s u~ c~ d~ s~` (质子包含夸克/反夸克/胶子)。举个例，我们要生成质子-质子对撞产生ZZ玻色子对的过程：
```
generate p p > z z
```
要生成更复杂的过程可以使用级联或者指定衰变

**指定衰变**：用逗号分隔
```
generate p p > z z, z > e+ e-, z > mu+ mu-
```
**级联**：用`>`分隔
```
generate p p > z z > e+ e- e+ e-
```
有的时候还可以需要`()`来指定衰变的粒子。你可以使用`define`命令来定义“多粒子”，例如
```
define l = l+ l-
```
然后我们就可以使用`l`来代替`l+ l-`所有的正负轻子了。

生成完毕后，导出到：
```
output <name>
```
就能看到目录里新增了`<name>`目录， 使用：
```
display diagrams
```
就能看到生成的费曼图。

> [!NOTE]
> 要退出Madgraph, 可以使用`exit`命令。

### 运行 Run

在生成事件后，我们可以使用`launch`命令来运行事件生成器。运行的基本格式为：
```
launch <name>
```
这时能看到一些选项，在基本教程里我们先使用默认的设置运行，输入`0`，`0`之后，就可以等待运行完毕了。

> [!NOTE]
> 这个时候如果使用的是桌面版的Linux发行版或者MacOS， 就会弹出一个浏览器窗口展示现在的运行情况。

运行完毕后，在终端可以看到截面(Cross-section)输出
```
  === Results Summary for run: run_01 tag: tag_1 ===
     Cross-section :   0.04215 +- 0.0001264 pb
     Nb of events :  10000
```
退出看看文件结构：
```
MC_pp_ZZ_4l
├── bin
├── Cards               #这里是运行使用的卡片，记载了所有的参数
├── crossx.html
├── Events              #产生好的事件
│   └── run_01
│       ├── run_01_tag_1_banner.txt     # 本次运行的参数
│       └── unweighted_events.lhe.gz    # 压缩后的事件文件(LHE格式)
├── HTML
├── index.html
├── lib
├── madevent.tar.gz
├── MGMEVersion.txt
├── README
├── README.systematics
├── Source
├── SubProcesses
└── TemplateVersion.txt
```
每次运行结束后可以得到`lhe`文件，实际上就是一个压缩的xml文件，这里可以直接使用文本编辑器打开，里面包含了事件的所有信息。使用`gunzip`程序可以解压缩, 然后`vim`打开后往下翻，我们可以看到：
```xml
<event>
 8      1 +4.2149000e-02 9.55760200e+01 7.54677100e-03 1.29465300e-01
        2 -1    0    0  501    0 +0.0000000000e+00 +0.0000000000e+00 +9.5506381905e+02 9.5506381905e+02 0.0000000000e+00 0.0000e+00 -1.0000e+00
       -2 -1    0    0    0  501 -0.0000000000e+00 -0.0000000000e+00 -9.7341460225e+00 9.7341460225e+00 0.0000000000e+00 0.0000e+00 1.0000e+00
       23  2    1    2    0    0 +1.9016549906e+01 -2.2443146867e+01 +6.0561627854e+02 6.1189576124e+02 8.2340887686e+01 0.0000e+00 0.0000e+00
       23  2    1    2    0    0 -1.9016549906e+01 +2.2443146867e+01 +3.3971339449e+02 3.5290220383e+02 9.0936522149e+01 0.0000e+00 0.0000e+00
      -11  1    3    3    0    0 +3.4426239469e+00 +2.7678653334e+01 +1.0893651140e+02 1.1245053591e+02 0.0000000000e+00 0.0000e+00 -1.0000e+00
       11  1    3    3    0    0 +1.5573925959e+01 -5.0121800201e+01 +4.9667976714e+02 4.9944522534e+02 0.0000000000e+00 0.0000e+00 1.0000e+00
      -13  1    4    4    0    0 -6.5675098563e-01 -3.2043554550e+01 +6.8034929322e+01 7.5206198667e+01 0.0000000000e+00 0.0000e+00 1.0000e+00
       13  1    4    4    0    0 -1.8359798920e+01 +5.4486701417e+01 +2.7167846516e+02 2.7769600516e+02 0.0000000000e+00 0.0000e+00 -1.0000e+00
<mgrwt>
<rscale>  0 0.93473034E+02</rscale>
<asrwt>0</asrwt>
<pdfrwt beam="1">  1        2 0.14693282E+00 0.95576017E+02</pdfrwt>
<pdfrwt beam="2">  1       -2 0.14975617E-02 0.95576017E+02</pdfrwt>
<totfact> 0.30349347E+04</totfact>
</mgrwt> 
</event>
```
这就是一个事例，从左往右的顺序是：
```
|PID|Statues|Mother1|Mother2|Color1|Color2|Px|Py|Pz|E|M|...
```
我们需要关心的是：
- PID: 粒子ID, 具体的含义可以参考[PDG](https://pdg.lbl.gov/2007/reviews/montecarlorpp.pdf)的手册
- Statues: 粒子状态, 1表示末态粒子, 2表示中间粒子, -1表示初态粒子
- Px, Py, Pz, E: 粒子的四动量
- M: 粒子的不变质量

那么接下来我们就可以开始更进一步的分析了。


## 更详细的 Advanced

### 运行卡片 Run_Card

### 和CERN ROOT一起用：ExRootAnalysis

### MadSpin: 在MadGraph中衰变粒子

### Delphes：探测器重建

## 分析事例