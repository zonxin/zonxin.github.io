---
layout: post
title: "Wireless Communicaitons Letters(EA)"
categories: 无线通信 论文笔记
---

**Blind Modulation Classification for Alamouti STBC System with Transmission Impairments**

调制分类算法，使用时空编码(STBC)

+ Modulation classification(调制分类)
+ Alamouti (AL) STBC

**Joint Transceiver Design for Non-Concurrent MIMO Two-way AF Relaying**  

+ The idea of flow-aggregation has recently been extended to cellular systems
    where such concurrent data exchange between a user and a base station (BS)
    does not normally occur\[4-6\].

![ncTWR protocal]({{ "/image/papers/Rohit2015Joint-1.jpg" | prepend: site.baseurl }})

TUE需要发送数据给BS，BS要发送数据给TUE，假设需要通过中继节点（采用AF方式转发)，使用 MIMO
本文假设RUE接收不到TUE发送的信号，白化了MIMO中的有色噪声（因为太复杂前人都没有做）

还有一些问题：

+ THP (Tomlison-harashima precoding)
+ MIMO

**On Capacity and Association Area Characterization In Small Cell-Based Multi-Tier Networks**

两层网络中的情况下微基站和基站作用范围的讨论，同时考虑了大尺度衰落和小尺度衰落

**A Waterfilling Algorithm for Multiple Access Point Connectivity with Constrained Backhaul Network**

手机可以同时收到多个基站的信号时，Backhaul网络中功率的分配算法

**Prediction of Erlang-2 Distributed Primary User Traffic for Dynamic Spectrum Access**

认知无线电，预测primary user(PU)的信道使用时间。Erlang（厄兰）-2分布

**Two-Level Distributed Opportunistic Scheduling in DF Relay Networks**

在两跳无线网络中，节点竞争的问题。当探测到信道条件不好的时候即使赢得了竞争也不发送数据。
信道较差，在相同发送功率的情况下误码率会比较高。

**Performance Analysis of Synchronous Duty-Cycled MAC Protocols**

Duty-Cycling appears as a promising solution to reduce energy comsumption in wireless 
sensor networks.

**Mode Combination in an Ideal Wireless OAM-MIMO Multiplexing System**

利用电磁波(光子)的自旋角动量存储信息（太高端！）

**Outage Probability of Ad Hoc Networks with Wireless Information and Power Transfer**

1. integrated SWIPT enables information and power to be extracted from the same
   signal transmitted by a base station (BS) 
1. closed-loop SWIPT utilizes downlink from BS to users for power transfer (PT)
   and uplink for information transmission (IT) 
1. decoupled SWIPT overlays traditional cellular networks with special power
   beacons (PBs), which do not require a backhaul link, to provide dedicated PT

decoupled SWIPT 更适合在微小区中使用

**Wireless Networks With RF Energy Harvesting: A Contemporary Survey**

OFDM with index modulation (OFDM-IM) is a novel OFDM scheme which transmits the
information not only by the M-ary signal constellations, but also by the
indices of the subcarriers, which are activated according to the corresponding
information bits.

