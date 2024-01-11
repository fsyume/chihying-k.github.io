---
title: OSPF实验
categories: ospf
tags:
  - 网络
  - ospf
  - 华为认证
description: OSPF实验
abbrlink: 77cdb77b
date:
updated:
sticky:
---



# OSPF实验

> OSPF介绍
>
> 引用自：https://info.support.huawei.com/info-finder/encyclopedia/zh/OSPF.html

## 为什么需要OSPF？

在OSPF出现前，网络上广泛使用RIP（Routing Information Protocol）作为*内部网关协议*。

由于RIP是基于距离矢量算法的路由协议，存在着收敛慢、路由环路、可扩展性差等问题，所以逐渐被OSPF取代

OSPF作为基于链路状态的协议，能够解决RIP所面临的诸多问题。此外，OSPF还有以下优点：

- OSPF采用[组播](https://info.support.huawei.com/info-finder/encyclopedia/zh/组播.html)形式收发报文，这样可以减少对其它不运行OSPF路由器的影响
- OSPF支持无类型域间选路（CIDR）
- OSPF支持对等价路由进行负载分担
- OSPF支持报文加密

由于OSPF具有以上优势，使得OSPF作为优秀的内部网关协议被快速接收并广泛使用

目前，针对IPv4协议使用的是OSPF Version2（RFC 2328），针对IPv6的协议使用的是OSPF Version3（RFC2740）

## OSPF基础概念

### Router ID

如果要运行OSPF协议，必须存在Router ID。Router ID是一个32比特无符号整数，是一台路由器在自治系统中的唯一标识。

Router ID的设定有两种方式：

- 通过命令行手动配置，在实际网络部署中，建议手工配置OSPF的Router ID，因为这关系到协议的稳定。

- 通过协议自动选取。

  如果没有手动配置Router ID，设备会从当前接口的IP地址中自动选取一个作为Router ID。其选取顺序是：

  1. 优先从Loopback地址中选择最大的IP地址作为Router ID。
  2. 如果没有配置Loopback接口，则在接口地址中选取最大的IP地址作为Router ID。

在路由器运行了OSPF并确定了Router ID后，如果该Router ID对应的接口Down或者接口消失（例如执行了**undo interface loopback** *loopback-number*）或者出现更大的IP地址，OSPF将仍然保持原Router ID。只有重新配置系统的Router ID或者OSPF的Router ID，并且重新启动OSPF进程后，才会进行Router ID的重新选取。

### 链路状态

OSPF是一种链路状态协议。可以将链路视为路由器的接口。链路状态是对接口及接口与相邻路由器的关系的描述。例如接口的信息包括接口的IP地址、掩码、所连接的网络的类型、连接的邻居等。所有这些链路状态的集合形成链路状态数据库。

### COST

- OSPF使用cost“开销”作为路由度量值。
- 每一个激活OSPF的接口都有一个cost值。OSPF接口cost=100M/接口带宽，其中100M为OSPF的参考带宽（reference-bandwidth）。
- 一条OSPF路由的cost由该路由从路由的起源一路到达本地的所有入接口cost值的总和。

> 由于默认的参考带宽是100M，这意味着更高带宽的传输介质（高于100M）在OSPF协议中将会计算出一个小于1的分数，这在OSPF协议中是不允许的（会被四舍五入为1）。而现今网络设备很多都是大于100M带宽的接口，这时候路由cost的计算其实就不精确了。所以可以使用**bandwidth-reference**命令修改，但是这条命令要谨慎使用，一旦要配置，则建议全网OSPF路由器都配置。

### 报文类型

**表1-1** 报文类型

| 报文类型                                      | 报文作用                                                     |
| --------------------------------------------- | ------------------------------------------------------------ |
| Hello报文                                     | 周期性发送，用来发现和维持OSPF邻居关系。                     |
| DD报文（Database Description packet）         | 描述本地LSDB（Link State Database）的摘要信息，用于两台设备进行数据库同步。 |
| LSR报文（Link State Request packet）          | 用于向对方请求所需的LSA。设备只有在OSPF邻居双方成功交换DD报文后才会向对方发出LSR报文。 |
| LSU报文（Link State Update packet）           | 用于向对方发送其所需要的LSA。                                |
| LSAck报文（Link State Acknowledgment packet） | 用来对收到的LSA进行确认。                                    |

### LSA类型

**表1-2** LSA类型

| LSA类型                           | LSA作用                                                      |
| --------------------------------- | ------------------------------------------------------------ |
| Router-LSA（Type1）               | 每个设备都会产生，描述了设备的链路状态和开销，在所属的区域内传播。 |
| Network-LSA（Type2）              | 由DR（Designated Router）产生，描述本网段的链路状态，在所属的区域内传播。 |
| Network-summary-LSA（Type3）      | 由ABR产生，描述区域内某个网段的路由，并通告给发布或接收此LSA的非Totally STUB或NSSA区域。例如：ABR同时属于Area0和Area1，Area0内存在网段10.1.1.0，Area1内存在网段11.1.1.0，ABR为Area0生成到网段11.1.1.0的Type3 LSA；ABR为Area1生成到网段10.1.1.0的Type3 LSA，并通告给发布或接收此LSA的非Totally Stub或NSSA区域。 |
| ASBR-summary-LSA（Type4）         | 由ABR产生，描述到ASBR的路由，通告给除ASBR所在区域的其他相关区域。 |
| AS-external-LSA（Type5）          | 由ASBR产生，描述到AS外部的路由，通告到所有的区域（除了STUB区域和NSSA区域）。 |
| NSSA LSA（Type7）                 | 由ASBR产生，描述到AS外部的路由，仅在NSSA区域内传播。         |
| Opaque LSA（Type9/Type10/Type11） | Opaque LSA提供用于OSPF的扩展的通用机制。其中：Type9 LSA仅在接口所在网段范围内传播。用于支持GR的Grace LSA就是Type9 LSA的一种。Type10 LSA在区域内传播。用于支持TE的LSA就是Type10 LSA的一种。Type11 LSA在自治域内传播，目前还没有实际应用的例子。 |

### LSA在各区域中传播的支持情况

**表1-3** LSA在各区域中传播的支持情况

| 区域类型                           | Router-LSA（Type1） | Network-LSA（Type2） | Network-summary-LSA（Type3） | ASBR-summary-LSA（Type4） | AS-external-LSA（Type5） | NSSA LSA（Type7） |
| ---------------------------------- | ------------------- | -------------------- | ---------------------------- | ------------------------- | ------------------------ | ----------------- |
| 普通区域（包括标准区域和骨干区域） | 是                  | 是                   | 是                           | 是                        | 是                       | 否                |
| Stub区域                           | 是                  | 是                   | 是                           | 否                        | 否                       | 否                |
| Totally Stub区域                   | 是                  | 是                   | 否                           | 否                        | 否                       | 否                |
| NSSA区域                           | 是                  | 是                   | 是                           | 否                        | 否                       | 是                |
| Totally NSSA区域                   | 是                  | 是                   | 否                           | 否                        | 否                       | 是                |

### 路由器类型

OSPF协议中常用到的路由器类型如图所示。

![路由器类型](https://download.huawei.com/mdl/image/download?uuid=a1b351c20c5748b9b00f0b1d8277e33e)
*路由器类型*

**表1-4** 路由器类型

| 路由器类型                                   | 含义                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| 区域内路由器（Internal Router）              | 该类设备的所有接口都属于同一个OSPF区域。                     |
| 区域边界路由器ABR（Area Border Router）      | 该类设备可以同时属于两个以上的区域，但其中一个必须是骨干区域。ABR用来连接骨干区域和非骨干区域，它与骨干区域之间既可以是物理连接，也可以是逻辑上的连接。 |
| 骨干路由器（Backbone Router）                | 该类设备至少有一个接口属于骨干区域。所有的ABR和位于Area0的内部设备都是骨干路由器。 |
| 自治系统边界路由器ASBR（AS Boundary Router） | 与其他AS交换路由信息的设备称为ASBR。ASBR并不一定位于AS的边界，它可能是区域内设备，也可能是ABR。只要一台OSPF设备引入了外部路由的信息，它就成为ASBR。 |

### 路由类型

AS区域内和区域间路由描述的是AS内部的网络结构，AS外部路由则描述了应该如何选择到AS以外目的地址的路由。OSPF将引入的AS外部路由分为Type1和Type2两类。

如下表中按优先级从高到低顺序列出了路由类型。

**表1-5** 路由类型

| 路由类型                         | 含义                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| Intra Area                       | 区域内路由。                                                 |
| Inter Area                       | 区域间路由。                                                 |
| 第一类外部路由（Type1 External） | 这类路由的可信程度高一些，所以计算出的外部路由的开销与自治系统内部的路由开销是相当的，并且和OSPF自身路由的开销具有可比性。到第一类外部路由的开销=本设备到相应的ASBR的开销+ASBR到该路由目的地址的开销。 |
| 第二类外部路由（Type2 External） | 这类路由的可信度比较低，所以OSPF协议认为从ASBR到自治系统之外的开销远远大于在自治系统之内到达ASBR的开销。所以，OSPF计算路由开销时只考虑ASBR到自治系统之外的开销，即到第二类外部路由的开销=ASBR到该路由目的地址的开销。 |

### 区域类型

**表1-6** 区域类型

| 区域类型         | 作用                                                         |
| ---------------- | ------------------------------------------------------------ |
| 普通区域         | 缺省情况下，OSPF区域被定义为普通区域。普通区域包括标准区域和骨干区域。标准区域是最通用的区域，它传输区域内路由，区域间路由和外部路由。骨干区域是连接所有其他OSPF区域的中央区域。骨干区域通常用Area 0表示。 |
| STUB区域         | 不允许发布自治系统外部路由，只允许发布区域内路由和区域间的路由。在STUB区域中，路由器的路由表规模和路由信息传递的数量都会大大减少。为了保证到自治系统外的路由可达，由该区域的ABR发布Type3缺省路由传播到区域内，所有到自治系统外部的路由都必须通过ABR才能发布。 |
| Totally STUB区域 | 不允许发布自治系统外部路由和区域间的路由，只允许发布区域内路由。在Totally STUB区域中，路由器的路由表规模和路由信息传递的数量都会大大减少。为了保证到自治系统外和其他区域的路由可达，由该区域的ABR发布Type3缺省路由传播到区域内，所有到自治系统外部和其他区域的路由都必须通过ABR才能发布。 |
| NSSA区域         | NSSA区域允许引入自治系统外部路由，由ASBR发布Type7 LSA通告给本区域，这些Type7 LSA在ABR上转换成Type5 LSA，并且泛洪到整个OSPF域中。NSSA区域同时保留自治系统内的STUB区域的特征。该区域的ABR发布Type7缺省路由传播到区域内，所有域间路由都必须通过ABR才能发布。 |
| Totally NSSA区域 | Totally NSSA区域允许引入自治系统外部路由，由ASBR发布Type7 LSA通告给本区域，这些Type7 LSA在ABR上转换成Type5 LSA，并且泛洪到整个OSPF域中。Totally NSSA区域同时保留自治系统内的Totally STUB Area区域的特征。该区域的ABR发布Type3和Type7缺省路由传播到区域内，所有域间路由都必须通过ABR才能发布。 |

### OSPF支持的网络类型

OSPF根据链路层协议类型，将网络分为如下表所列四种类型。

**表1-7** OSPF网络类型

| 网络类型                                | 含义                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| 广播类型（Broadcast）                   | 当链路层协议是Ethernet、FDDI时，缺省情况下，OSPF认为网络类型是Broadcast。在该类型的网络中：通常以[组播](https://info.support.huawei.com/info-finder/encyclopedia/zh/组播.html)形式发送Hello报文、LSU报文和LSAck报文。其中，224.0.0.5的组播地址为OSPF设备的预留[IP组播](https://info.support.huawei.com/info-finder/encyclopedia/zh/组播.html)地址；224.0.0.6的组播地址为OSPF DR/BDR（ Backup Designated Router）的预留IP组播地址。以单播形式发送DD报文和LSR报文。 |
| NBMA类型（Non-Broadcast Multi-Access）  | 当链路层协议是帧中继、X.25时，缺省情况下，OSPF认为网络类型是NBMA。在该类型的网络中，以单播形式发送协议报文（Hello报文、DD报文、LSR报文、LSU报文、LSAck报文）。 |
| 点到多点P2MP类型（Point-to-Multipoint） | 没有一种链路层协议会被缺省的认为是Point-to-Multipoint类型。点到多点必须是由其他的网络类型强制更改的。常用做法是将非全连通的NBMA改为点到多点的网络。在该类型的网络中：以组播形式（224.0.0.5）发送Hello报文。以单播形式发送其他协议报文（DD报文、LSR报文、LSU报文、LSAck报文）。 |
| 点到点P2P类型（point-to-point）         | 当链路层协议是PPP、HDLC和LAPB时，缺省情况下，OSPF认为网络类型是P2P。在该类型的网络中，以组播形式（224.0.0.5）发送协议报文（Hello报文、DD报文、LSR报文、LSU报文、LSAck报文）。 |

### DR和BDR

在广播网和NBMA网络中，任意两台路由器之间都要传递路由信息。如图所示，网络中有n台路由器，则需要建立n*(n-1)/2个邻接关系。这使得任何一台路由器的路由变化都会导致多次传递，浪费了带宽资源。为解决这一问题，OSPF定义了指定路由器DR和备份指定路由器BDR。通过选举产生DR（Designated Router）后，所有路由器都只将信息发送给DR，由DR将网络链路状态LSA广播出去。除DR和BDR之外的路由器（称为DR Other）之间将不再建立邻接关系，也不再交换任何路由信息，这样就减少了广播网和NBMA网络上各路由器之间邻接关系的数量。

![选举DR前后对比图](https://download.huawei.com/mdl/image/download?uuid=cbb680e5ce9649548861b65001616f40)
*选举DR前后对比图*

如果DR由于某种故障而失效，则网络中的路由器必须重新选举DR，并与新的DR同步。这需要较长的时间，在这段时间内，路由的计算有可能是不正确的。为了能够缩短这个过程，OSPF提出了BDR（Backup Designated Router）的概念。BDR是对DR的一个备份，在选举DR的同时也选举出BDR，BDR也和本网段内的所有路由器建立邻接关系并交换路由信息。当DR失效后，BDR会立即成为DR。由于不需要重新选举，并且邻接关系已建立，所以这个过程非常短暂，这时还需要再重新选举出一个新的BDR，虽然一样需要较长的时间，但并不会影响路由的计算。

DR和BDR不是人为指定的，而是由本网段中所有的路由器共同选举出来的。路由器接口的DR优先级决定了该接口在选举DR、BDR时所具有的资格。本网段内DR优先级大于0的路由器都可作为“候选人”。选举中使用的“选票”就是Hello报文。每台路由器将自己选出的DR写入Hello报文中，发给网段上的其他路由器。当处于同一网段的两台路由器同时宣布自己是DR时，DR优先级高者胜出。如果优先级相等，则Router ID大者胜出。如果一台路由器的优先级为0，则它不会被选举为DR或BDR。

### STUB区域

STUB区域是一些特定的区域，STUB区域的ABR不传播它们接收到的自治系统外部路由，在这些区域中路由器的路由表规模以及路由信息传递的数量都会大大减少。

STUB区域是一种可选的配置属性，但并不是每个区域都符合配置的条件。通常来说，STUB区域位于自治系统的边界，是那些只有一个ABR的非骨干区域。

为保证到自治系统外的路由依旧可达，该区域的ABR将生成一条缺省路由，并发布给STUB区域中的其他非ABR路由器。

配置STUB区域时需要注意下列几点：

- 骨干区域不能配置成STUB区域。
- 如果要将一个区域配置成STUB区域，则该区域中的所有路由器都要配置STUB区域属性。
- STUB区域内不能存在ASBR，即自治系统外部的路由不能在本区域内传播。
- 虚连接不能穿过STUB区域。

### NSSA区域

NSSA（Not-So-Stubby Area）区域是OSPF特殊的区域类型。NSSA区域与STUB区域有许多相似的地方，两者都不传播来自OSPF网络其它区域的外部路由。差别在于STUB区域是不能引入外部路由，NSSA区域能够将自治域外部路由引入并传播到整个OSPF自治域中。

当区域配置为NSSA区域后，为保证到自治系统外的路由可达，NSSA区域的ABR将生成一条缺省路由，并发布给NSSA区域中的其他路由器。

配置NSSA区域时需要注意下列几点：

- 骨干区域不能配置成NSSA区域。
- 如果要将一个区域配置成NSSA区域，则该区域中的所有路由器都要配置NSSA区域属性。
- 虚连接不能穿过NSSA区域。

### 邻居状态机

在OSPF网络中，为了交换路由信息，邻居设备之间首先要建立邻接关系，邻居（Neighbors）关系和邻接（Adjacencies）关系是两个不同的概念。

- 邻居关系：OSPF设备启动后，会通过OSPF接口向外发送Hello报文，收到Hello报文的OSPF设备会检查报文中所定义的参数，如果双方一致就会形成邻居关系，两端设备互为邻居。
- 邻接关系：形成邻居关系后，如果两端设备成功交换DD报文和LSA，才建立邻接关系。

OSPF共有8种状态机，分别是：Down、Attempt、Init、2-way、Exstart、Exchange、Loading、Full。

- Down：邻居会话的初始阶段，表明没有在邻居失效时间间隔内收到来自邻居路由器的Hello数据包。
- Attempt：该状态仅发生在NBMA网络中，表明对端在邻居失效时间间隔（dead interval）超时前仍然没有回复Hello报文。此时路由器依然每发送轮询Hello报文的时间间隔（poll interval）向对端发送Hello报文。
- Init：收到Hello报文后状态为Init。
- 2-way：收到的Hello报文中包含有自己的Router ID，则状态为2-way；如果不需要形成邻接关系则邻居状态机就停留在此状态，否则进入Exstart状态。
- Exstart：开始协商主从关系，并确定DD的序列号，此时状态为Exstart。
- Exchange：主从关系协商完毕后开始交换DD报文，此时状态为Exchange。
- Loading：DD报文交换完成即Exchange done，此时状态为Loading。
- Full：LSR重传列表为空，此时状态为Full。

### OSPF报文认证

OSPF支持报文验证功能，只有通过验证的OSPF报文才能接收，否则将不能正常建立邻居。

路由器支持两种验证方式：

- 区域验证方式
- 接口验证方式

当两种验证方式都存在时，优先使用接口验证方式。

### OSPF路由聚合

路由聚合是指ABR可以将具有相同前缀的路由信息聚合到一起，只发布一条路由到其它区域。

区域间通过路由聚合，可以减少路由信息，从而减小路由表的规模，提高设备的性能。

OSPF有两种路由聚合方式：

- ABR聚合

  ABR向其它区域发送路由信息时，以网段为单位生成Type3 LSA。如果该区域中存在一些连续的网段，则可以通过命令将这些连续的网段聚合成一个网段。这样ABR只发送一条聚合后的LSA，所有属于命令指定的聚合网段范围的LSA将不会再被单独发送出去。

- ASBR聚合

  配置路由聚合后，如果本地设备是自治系统边界路由器ASBR，将对引入的聚合地址范围内的Type5 LSA进行聚合。当配置了NSSA区域时，还要对引入的聚合地址范围内的Type7 LSA进行聚合。

  如果本地设备既是ASBR又是ABR，则对由Type7 LSA转化成的Type5 LSA进行聚合处理。

### OSPF缺省路由

缺省路由是指目的地址和掩码都是0的路由。当设备无精确匹配的路由时，就可以通过缺省路由进行报文转发。由于OSPF路由的分级管理，Type3缺省路由的优先级高于Type5或Type7路由。

OSPF缺省路由通常应用于下面两种情况：

- 由区域边界路由器（ABR）发布Type3缺省Summary LSA，用来指导区域内设备进行区域之间报文的转发。
- 由自治系统边界路由器（ASBR）发布Type5外部缺省ASE LSA，或者Type7外部缺省NSSA LSA，用来指导自治系统（AS）内设备进行自治系统外报文的转发。

OSPF缺省路由的发布原则如下：

- OSPF路由器只有具有对区域外的出口时，才能够发布缺省路由LSA。
- 如果OSPF路由器已经发布了缺省路由LSA，那么不再学习其它路由器发布的相同类型缺省路由。即路由计算时不再计算其它路由器发布的相同类型的缺省路由LSA，但数据库中存有对应LSA。
- 外部缺省路由的发布如果要依赖于其它路由，那么被依赖的路由不能是本OSPF路由域内的路由，即不是本进程OSPF学习到的路由。因为外部缺省路由的作用是用于指导报文的域外转发，而本OSPF路由域的路由的下一跳都指向了域内，不能满足指导报文域外转发的要求。

不同区域缺省路由发布原则如下表所示。

**表1-8** OSPF缺省路由发布原则

| 区域类型         | 作用                                                         |
| ---------------- | ------------------------------------------------------------ |
| 普通区域         | 缺省情况下，普通OSPF区域内的OSPF路由器是不会产生缺省路由的，即使它有缺省路由。当网络中缺省路由通过其他路由进程产生时，路由器必须将缺省路由通告到整个OSPF自治域中。实现方法是在ASBR上手动通过命令进行配置，产生缺省路由。配置完成后，路由器会产生一个缺省ASE LSA（Type5 LSA），并且通告到整个OSPF自治域中。 |
| STUB区域         | STUB区域不允许自治系统外部的路由（Type5 LSA）在区域内传播。区域内的路由器必须通过ABR学到自治系统外部的路由。实现方法是ABR会自动产生一条缺省的Summary LSA（Type3 LSA）通告到整个STUB区域内。这样，到达自治系统的外部路由就可以通过ABR到达。 |
| Totally STUB区域 | Totally STUB区域既不允许自治系统外部的路由（Type5 LSA）在区域内传播，也不允许区域间路由（Type3 LSA）在区域内传播。区域内的路由器必须通过ABR学到自治系统外部和其他区域的路由。实现方法是配置Totally STUB区域后，ABR会自动产生一条缺省的Summary LSA（Type3 LSA）通告到整个STUB区域内。这样，到达自治系统外部的路由和其他区域间的路由都可以通过ABR到达。 |
| NSSA区域         | NSSA区域允许引入通过本区域的ASBR到达的少量外部路由，但不允许其他区域的外部路由ASE LSA（Type5 LSA）在区域内传播。即到达自治系统外部的路由只能通过本区域的ASBR到达。只配置了NSSA区域是不会自动产生缺省路由的。此时，有两种选择：如果希望到达自治系统外部的路由通过该区域的ASBR到达，而其它外部路由通过其它区域出去。此时，ABR会产生一条Type7 LSA的缺省路由，通告到整个NSSA区域内。这样，除了某少部分路由通过NSSA的ASBR到达，其它路由都可以通过NSSA的ABR到达其它区域的ASBR出去。如果希望所有的外部路由只通过本区域NSSA的ASBR到达。则必须在ASBR上手动通过命令进行配置，使ASBR产生一条缺省的NSSA LSA（Type7 LSA），通告到整个NSSA区域内。这样，所有的外部路由就只能通过本区域NSSA的ASBR到达。上面两种情况的区别是：在ABR上无论路由表中是否存在缺省路由0.0.0.0，都会产生Type7 LSA的缺省路由。在ASBR上只有当路由表中存在缺省路由0.0.0.0时，才会产生Type7 LSA的缺省路由。因为缺省路由只是在本NSSA区域内泛洪，并没有泛洪到整个OSPF域中，所以本NSSA区域内的路由器在找不到路由之后可以从该NSSA的ASBR出去，但不能实现其他OSPF域的路由从这个出口出去。Type7 LSA缺省路由不会在ABR上转换成Type5 LSA缺省路由泛洪到整个OSPF域。 |
| Totally NSSA区域 | Totally NSSA区域既不允许其他区域的外部路由ASE LSA（Type5 LSA）在区域内传播，也不允许区域间路由（Type3 LSA）在区域内传播。区域内的路由器必须通过ABR学到其他区域的路由。实现方法是配置Totally NSSA区域后，ABR会自动产生一条缺省的Type3 LSA通告到整个NSSA区域内。这样，其他区域的外部路由和区域间路由都可以通过ABR在区域内传播。 |

### OSPF路由过滤

OSPF支持使用路由策略对路由信息进行过滤。缺省情况下，OSPF不进行路由过滤。

OSPF可以使用的路由策略包括route-policy，访问控制列表（access-list），地址前缀列表（prefix-list）。

OSPF路由过滤可以应用于以下几个方面：

- 路由引入

  OSPF可以引入其它路由协议学习到的路由。在引入时可以通过配置路由策略来过滤路由，只引入满足条件的路由。

- 引入路由发布

  OSPF引入了路由后会向其它邻居发布引入的路由信息。

  可以通过配置过滤规则来过滤向邻居发布的路由信息。该过滤规则只在ASBR上配置才有效。

- 路由学习

  通过配置过滤规则，可以设置OSPF对接收到的区域内、区域间和自治系统外部的路由进行过滤。

  该过滤只作用于路由表项的添加与否，即只有通过过滤的路由才被添加到本地路由表中，但所有的路由仍可以在OSPF路由表中被发布出去。

- 区域间LSA学习

  通过命令可以在ABR上配置对进入本区域的Summary LSA进行过滤。该配置只在ABR上有效（只有ABR才能发布Summary LSA）。

**表1-9** 区域间LSA学习与路由学习的差异

| 区域间LSA学习                 | 路由学习                                                     |
| ----------------------------- | ------------------------------------------------------------ |
| 直接对进入区域的LSA进行过滤。 | 路由学习中的过滤不对LSA进行过滤，只针对LSA计算出来的路由是否添加本地路由表进行过滤。学习到的LSA是完整的。 |

- 区域间LSA发布

  通过命令可以在ABR上配置对本区域出方向的Summary LSA进行过滤。该配置只在ABR上配置有效。

### OSPF多进程

OSPF支持多进程，在同一台路由器上可以运行多个不同的OSPF进程，它们之间互不影响，彼此独立。不同OSPF进程之间的路由交互相当于不同路由协议之间的路由交互。

路由器的一个接口只能属于某一个OSPF进程。

OSPF多进程的一个典型应用就是在VPN场景中PE和CE之间运行OSPF协议，同时VPN骨干网上的IGP也采用OSPF。在PE上，这两个OSPF进程互不影响。

### OSPF RFC1583兼容

RFC1583是OSPFv2协议比较早的版本。

OSPF在计算外部路由时，由于RFC2328和RFC1583的路由计算规则不一致，可能会导致路由环路。为了避免路由环路的发生，RFC2328中提出了RFC1583兼容特性。

- 使能RFC1583兼容后，OSPF采用RFC1583的路由计算规则。
- 不使能RFC1583兼容时，OSPF采用RFC2328的路由计算规则。

OSPF是根据5类LSA来计算外部路由的。RFC1583兼容特性主要用于路由器收到5类LSA后：

- 选择到达产生该LSA的ASBR或该LSA所描述的转发地址（Forwarding Address）的路径；
- 选择到达相同目的地的外部路径。

缺省情况下，OSPF兼容RFC1583。

## OSPF是如何工作的？

OSPF协议路由的计算过程可简单描述如下：

1. 建立邻接关系

   ，过程如下：

   1. 本端设备通过接口向外发送Hello报文与对端设备建立邻居关系。

   2. 两端设备进行主/从关系协商和DD报文交换。

   3. 两端设备通过更新LSA完成链路数据库LSDB的同步。

      此时，邻接关系建立成功。

2. 路由计算

   OSPF采用SPF（Shortest Path First）算法计算路由，可以达到路由快速收敛的目的。



### 建立邻接关系

在上述邻居状态机的变化中，有两处决定是否建立邻接关系：

- 当与邻居的双向通讯初次建立时。
- 当网段中的DR和BDR发生变化时。

OSPF在不同网络类型中，OSPF邻接关系建立的过程不同，分为广播网络，NBMA网络，点到点/点到多点网络。

**在广播网络中建立OSPF****邻接关系**

广播链路邻接关系建立过程如图所示。

在广播网络中，DR、BDR和网段内的每一台路由器都形成邻接关系，但DR other之间只形成邻居关系。

![在广播网络中建立OSPF邻接关系](https://download.huawei.com/mdl/image/download?uuid=091b70fc4f9a43ecbd0e94a7aaed695c)
*在广播网络中建立OSPF邻接关系*



如图所示，在广播网络中建立OSPF邻接关系的过程如下：

1. 建立邻居关系

   1. RouterA的一个连接到广播类型网络的接口上激活了OSPF协议，并发送了一个Hello报文（使用[组播](https://info.support.huawei.com/info-finder/encyclopedia/zh/组播.html)地址224.0.0.5）。此时，RouterA认为自己是DR路由器（DR=1.1.1.1），但不确定邻居是哪台路由器（Neighbors Seen=0）。
   2. RouterB收到RouterA发送的Hello报文后，发送一个Hello报文回应给RouterA，并且在报文中的Neighbors Seen字段中填入RouterA的Router ID（Neighbors Seen=1.1.1.1），表示已收到RouterA的Hello报文，并且宣告DR路由器是RouterB（DR=2.2.2.2），然后RouterB的邻居状态机置为Init。
   3. RouterA收到RouterB回应的Hello报文后，将邻居状态机置为2-way状态，下一步双方开始发送各自的链路状态数据库。

   ![img](https://download.huawei.com/mdl/image/download?uuid=4d039cad0996458293e1ef379d855fed)

   在广播网络中，两个接口状态是DR Other的路由器之间将停留在此步骤。

2. 主/从关系协商、DD报文交换

   1. RouterA首先发送一个DD报文，宣称自己是Master（MS=1），并规定序列号Seq=X。I=1表示这是第一个DD报文，报文中并不包含LSA的摘要，只是为了协商主从关系。M=1说明这不是最后一个报文。

      为了提高发送的效率，RouterA和RouterB首先了解对端数据库中哪些LSA是需要更新的，如果某一条LSA在LSDB中已经存在，就不再需要请求更新了。为了达到这个目的，RouterA和RouterB先发送DD报文，DD报文中包含了对LSDB中LSA的摘要描述（每一条摘要可以惟一标识一条LSA）。为了保证在传输的过程中报文传输的可靠性，在DD报文的发送过程中需要确定双方的主从关系，作为Master的一方定义一个序列号Seq，每发送一个新的DD报文将Seq加一，作为Slave的一方，每次发送DD报文时使用接收到的上一个Master的DD报文中的Seq。

   2. RouterB在收到RouterA的DD报文后，将RouterA的邻居状态机改为Exstart，并且回应了一个DD报文（该报文中同样不包含LSA的摘要信息）。由于RouterB的Router ID较大，所以在报文中RouterB认为自己是Master，并且重新规定了序列号Seq=Y。

   3. RouterA收到报文后，同意了RouterB为Master，并将RouterB的邻居状态机改为Exchange。RouterA使用RouterB的序列号Seq=Y来发送新的DD报文，该报文开始正式地传送LSA的摘要。在报文中RouterA将MS=0，说明自己是Slave。

   4. RouterB收到报文后，将RouterA的邻居状态机改为Exchange，并发送新的DD报文来描述自己的LSA摘要，此时RouterB将报文的序列号改为Seq=Y+1。

      上述过程持续进行，RouterA通过重复RouterB的序列号来确认已收到RouterB的报文。RouterB通过将序列号Seq加1来确认已收到RouterA的报文。当RouterB发送最后一个DD报文时，在报文中写上M=0。

3. LSDB同步（LSA请求、LSA传输、LSA应答）

   1. RouterA收到最后一个DD报文后，发现RouterB的数据库中有许多LSA是自己没有的，将邻居状态机改为Loading状态。此时RouterB也收到了RouterA的最后一个DD报文，但RouterA的LSA，RouterB都已经有了，不需要再请求，所以直接将RouterA的邻居状态机改为Full状态。

   2. RouterA发送LSR报文向RouterB请求更新LSA。RouterB用LSU报文来回应RouterA的请求。RouterA收到后，发送LSAck报文确认。

      上述过程持续到RouterA中的LSA与RouterB的LSA完全同步为止，此时RouterA将RouterB的邻居状态机改为Full状态。当路由器交换完DD报文并更新所有的LSA后，此时邻接关系建立完成。

**在NBMA****网络中建立OSPF****邻接关系**

NBMA网络和广播网络的邻接关系建立过程只在交换DD报文前不一致，如图中的蓝色标记。

在NBMA网络中，所有路由器只与DR和BDR之间形成邻接关系。

![在NBMA网络中建立OSPF邻接关系](https://download.huawei.com/mdl/image/download?uuid=f06293d927c5461bb6691e28633d71f9)
*在NBMA网络中建立OSPF邻接关系*

如图所示，在NBMA网络中建立OSPF邻接关系的过程如下：

1. 建立邻居关系

   1. RouterB向RouterA的一个状态为Down的接口发送Hello报文后，RouterB的邻居状态机置为Attempt。此时，RouterB认为自己是DR路由器（DR=2.2.2.2），但不确定邻居是哪台路由器（Neighbors Seen=0）。
   2. RouterA收到Hello报文后将邻居状态机置为Init，然后再回复一个Hello报文。此时，RouterA同意RouterB是DR路由器（DR=2.2.2.2），并且在Neighbors Seen字段中填入邻居路由器的Router ID（Neighbors Seen=2.2.2.2）。

   ![img](https://download.huawei.com/mdl/image/download?uuid=4d039cad0996458293e1ef379d855fed)

   在NBMA网络中，两个接口状态是DR Other的路由器之间将停留在此步骤。

2. 主/从关系协商、DD报文交换过程同广播网络的邻接关系建立过程。

3. LSDB同步（LSA请求、LSA传输、LSA应答）过程同广播网络的邻接关系建立过程。

**在点到点/****点到多点网络中建立OSPF****邻接关系**

在点到点/点到多点网络中，邻接关系的建立过程和广播网络一样，唯一不同的是不需要选举DR和BDR，DD报文是组播发送的。



### 路由计算

OSPF采用SPF（Shortest Path First）算法计算路由，可以达到路由快速收敛的目的。

OSPF协议使用链路状态通告LSA描述网络拓扑，即有向图。Router LSA描述路由器之间的链接和链路的属性。路由器将LSDB转换成一张带权的有向图，这张图便是对整个网络拓扑结构的真实反映。各个路由器得到的有向图是完全相同的。如图所示。

![由LSDB生成带权有向图](https://download.huawei.com/mdl/image/download?uuid=534098449c804d73a7bfc7aea26d364a)
*由LSDB生成带权有向图*

每台路由器根据有向图，使用SPF算法计算出一棵以自己为根的最短路径树，这棵树给出了到自治系统中各节点的路由。如图所示。

![最小生成树](https://download.huawei.com/mdl/image/download?uuid=d531aadda16c498aae39b0791d2e157c)
*最小生成树*



当OSPF的链路状态数据库LSDB发生改变时，需要重新计算最短路径，如果每次改变都立即计算最短路径，将占用大量资源，并会影响路由器的效率，通过调节SPF的计算间隔时间，可以抑制由于网络频繁变化带来的占用过多资源。缺省情况下，SPF时间间隔为5秒钟。

具体的计算过程如下：

1. 计算区域内路由。

   Router LSA和Network LSA可以精确的描述出整个区域内部的网络拓扑，根据SPF算法，可以计算出到各个路由器的最短路径。根据Router LSA描述的与路由器的网段情况，得到了到达各个网段的具体路径。

   ![img](https://download.huawei.com/mdl/image/download?uuid=4d039cad0996458293e1ef379d855fed)

   在计算过程中，如果有多条等价路由，SPF算法会将所有等价路径都保留在LSDB中。

2. 计算区域外路由。

   从一个区域内部看，相邻区域的路由对应的网段好像是直接连接在ABR上，而到ABR的最短路径已经在上一过程中计算完毕，所以直接检查Network Summary LSA，就可以很容易得到这些网段的最短路径。另外，ASBR也可以看成是连接在ABR上，所以ASBR的最短路径也可以在这个阶段计算出来。

   ![img](https://download.huawei.com/mdl/image/download?uuid=4d039cad0996458293e1ef379d855fed)

   如果进行SPF计算的路由器是ABR，那么只需要检查骨干区域的Network Summary LSA。

3. 计算自治系统外路由。

   由于自治系统外部的路由可以看成是直接连接在ASBR上，而到ASBR的最短路径在上一过程中已经计算完毕，所以逐条检查AS External LSA就可以得到到达各个外部网络的最短路径。
