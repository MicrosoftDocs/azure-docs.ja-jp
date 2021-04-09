---
title: Linux のパフォーマンス ツール
titleSuffix: Azure Kubernetes Service
description: Linux のパフォーマンス ツールを使用して、Azure Kubernetes Service (AKS) を使用するときに発生する一般的な問題をトラブルシューティングおよび解決する方法について説明します。
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90982029"
---
# <a name="linux-performance-troubleshooting"></a>Linux のパフォーマンスのトラブルシューティング

Linux マシンでのリソースの枯渇は一般的な問題であり、さまざまな症状で現れることがあります。 このドキュメントでは、そのような問題の診断に役立つツールの概要を提供します。

それらのツールの多くは、ローリング出力を生成する間隔を受け入れます。 通常、この出力形式により、パターンをより簡単に発見できます。 受け入れられる場合、サンプル呼び出しには `[interval]` が含まれます。

これらのツールの多くには、広範な履歴と豊富な構成オプションがあります。 このページでは、一般的な問題を強調するために、単純な呼び出しのサブセットのみを提供します。 個々のツールのリファレンス ドキュメントが常に正規の情報源となります。 それらのドキュメントは、ここで提供されているものよりもはるかに詳細なものになります。

## <a name="guidance"></a>ガイダンス

パフォーマンス問題を調査する場合は、系統的に行ってください。 2 つの一般的な方法は、USE (Utilization、Saturation、Errors - 使用率、飽和状態、エラー) と RED (Rate、Errors、Duration - 比率、エラー、期間) です。 通常、RED は、要求に基づく監視のサービス コンテキストで使用されます。 USE は、通常、リソースの監視に使用されます。つまり、マシン内のリソースごとに、使用率、飽和状態、およびエラーを監視します。 どのマシンでも 4 つの主要な種類のリソースは、CPU、メモリ、ディスク、およびネットワークです。 これらのいずれかのリソースで、使用率、飽和状態、またはエラーの比率が高い場合は、システムに問題がある可能性があることを示しています。 問題がある場合は、根本原因を調査します。つまり、ディスクの IO 待機時間が長いのはなぜか? ディスクまたは仮想マシンの SKU で調整が行われているか? どのプロセスがデバイスへの書き込みを行っているか? そしてどのファイルに対してか?

それらを診断するための一般的な問題と指標のいくつかの例を次に示します。
- IOPS 調整: iostat を使用して、デバイスごとの IOPS を測定します。 個々のディスクが制限を超えていないこと、および全ディスクの合計が仮想マシンの上限未満であることを確認します。
- 帯域幅調整: IOPS には iostat を使用しますが、読み取り/書き込みのスループットを測定します。 デバイスごとと合計の両方のスループットが帯域幅の上限未満であることを確認します。
- SNAT の枯渇: これは、SAR でのアクティブ (送信) 接続数の多さとして現れる可能性があります。 
- パケット損失: これは、送受信カウントに対する TCP 再送信カウントを介してプロキシで測定できます。 `sar` と `netstat` は両方ともこの情報を表示できます。

## <a name="general"></a>全般

これらのツールは汎用的なものであり、基本的なシステム情報に対応しています。 これらは、さらなる調査を行うための出発点として適しています。

### <a name="uptime"></a>uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

uptime は、システムの稼働時間と、1、5、15 分間における負荷の平均値を提供します。 これらの負荷の平均値は、作業を行っている、または無停電作業が完了するのを待機しているスレッドにほぼ対応します。 絶対的な意味では、これらの数値は解釈が難しい場合がありますが、長い時間をかけて測定することで、有益な情報をもたらす可能性があります。

- 1 分間の平均値 > 5 分間の平均値は、負荷が増加していることを意味します。
- 1 分間の平均値 < 5 分間の平均値は、負荷が減少していることを意味します。

また、uptime によって、情報を利用できない理由が明らかになることもあります (ユーザーがマシンにアクセスできる前に、問題が自然に、または再起動によって解決している可能性があります)。

負荷の平均値が、使用可能な CPU スレッド数よりも大きい場合は、特定のワークロードでパフォーマンスの問題が発生していることを示している可能性があります。

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg はカーネル バッファーをダンプします。 OOMKill のようなイベントは、カーネル バッファーにエントリを追加します。 dmesg のログで OOMKill やその他のリソース枯渇メッセージが検出された場合は、問題を示す強い指標となります。

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top` は、現在のシステム状態の広範な概要を提供します。 ヘッダーは、いくつかの有用な集計情報を提供します。

- タスクの状態: 実行中、休止中、停止。
- CPU 使用率。この場合は、ほとんどがアイドル時間を示しています。
- 合計、空き、使用されているシステム メモリ。

`top` は、存続時間が短いプロセスを見逃す可能性がありますが、`htop` や `atop` のような代替手段は、これらの欠点を一部修正しながら、同様のインターフェイスを提供します。

## <a name="cpu"></a>CPU

これらのツールは、CPU 使用率情報を提供します。 これは、パターンを見つけるのが容易なローリング出力で特に役立ちます。

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat` は、CPU スレッドごとに分類して、類似した CPU 情報を上部に出力します。 すべてのコアを一度に確認すると、非常に不均衡な CPU 使用率 (たとえば、1 つのスレッド アプリケーションで 1 つのコアが 100% の使用率で使用されている場合など) の検出に役立つ場合があります。 この問題は、システム内のすべての CPU で集計を行った場合、発見がより難しくなる可能性があります。

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` は、`mpstat` および `top` と同様の情報を提供し、CPU (r 列) で待機しているプロセスの数、メモリ統計、および各作業状態で費やされた CPU 時間の割合を列挙します。

## <a name="memory"></a>メモリ

メモリは非常に重要ですが、追跡するのが容易なリソースです。`vmstat` などの一部のツールでは、CPU とメモリの両方を報告できます。 ただし、`free` のようなツールもクイック デバッグに役立ちます。

### <a name="free"></a>制限なし

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` は、合計メモリと、使用済みおよび空きメモリに関する基本情報を示します。 `vmstat` は、ローリング出力を提供できるため、基本的なメモリ分析を行う場合にも、より役立ちます。

## <a name="disk"></a>ディスク

これらのツールは、ディスク IOPS、待機キュー、および合計スループットを測定します。 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat` は、ディスク使用率に関する深い分析情報を提供します。 この呼び出しでは、`-x` (拡張統計)、`-y` (ブート以降のシステム平均を表示する初期出力をスキップする)、`1 1` (1 秒間のインターバルが必要で、1 つの出力ブロック後に終了することを指定する) が渡されます。 

`iostat` は、多くの有用な統計情報を提供します。

- `r/s` と `w/s` は、1 秒あたりの読み取り数と 1 秒あたりの書き込み数です。 これらの値の合計が IOPS です。
- `rkB/s` と `wkB/s` は、1 秒あたりの読み取り/書き込みのキロバイト数です。 これらの値の合計がスループットです。
- `await` は、キューに入れられた要求の平均 IO 待機時間 (ミリ秒単位) です。
- `avgqu-sz` は、指定されたインターバルの平均キュー サイズです。

Azure VM 上:

- 個々のブロック デバイスの `r/s` と `w/s` の合計が、そのディスクの SKU 制限を超えることはできません。
- 個々のブロック デバイスの `rkB/s` と `wkB/s` の合計が、そのディスクの SKU 制限を超えることはできません。
- すべてのブロック デバイスの `r/s` と `w/s` の合計が、VM SKU の制限を超えることはできません。
- すべてのブロック デバイスの `rkB/s` と wkB/s の合計が、VM SKU の制限を超えることはできません。

OS ディスクは、その容量に対応する最小 SKU のマネージド ディスクとしてカウントされることに注意してください。 たとえば、1024 GB の OS ディスクは、P30 ディスクに対応します。 エフェメラル OS ディスクと一時ディスクには個別のディスク制限はありません。それらは、完全な VM 制限によってのみ制限されます。

await または avgqu-sz のゼロ以外の値も、IO の競合を示す良い指標です。

## <a name="network"></a>ネットワーク

これらのツールは、スループット、送信エラー、使用率などのネットワーク統計を測定します。 より深い分析により、輻輳や破棄されたパケットに関する詳細な TCP 統計があきらかになる場合があります。

### <a name="sar"></a>sar

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar` は、幅広い分析を行うための強力なツールです。 この例では、そのネットワーク統計の測定機能を使用しますが、CPU とメモリの消費量の測定についても同様に強力です。 この例は、`-n` フラグを指定して `sar` を呼び出して、`DEV` (ネットワーク デバイス) キーワードを指定し、デバイスごとのネットワーク スループットを表示します。

- `rxKb/s` と `txKb/s` の合計は、指定したデバイスの合計スループットです。 この値が、プロビジョニングされた Azure NIC の上限を超えると、マシン上のワークロードのネットワーク待機時間が長くなります。
- `%ifutil` は、指定したデバイスの使用率を測定します。 この値が 100% に近づくにつれて、ワークロードのネットワーク待機時間が長くなります。

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

この `sar` の呼び出しは、`TCP,ETCP` のキーワードを使用して TCP 接続を調べます。 最後の行の 3 番目の列である "retrans" は、1 秒あたりの TCP 再送信の回数です。 このフィールドの値が大きい場合は、ネットワーク接続が信頼できないことを示しています。 1 番目と 3 番目の行の "active" は、ローカル デバイスからの接続を意味し、"remote" は受信接続を示しています。  Azure での一般的な問題として、SNAT ポートの枯渇があります。この検出には `sar` が役立ちます。 SNAT ポートの枯渇は、高い "active" 値として現れます。これは、この問題が、ローカルで開始された送信 TCP 接続の比率が高いことが原因であるためです。

`sar` はインターバルを取ると、ローリング出力を表示し、次に呼び出しからの平均結果を含む出力の最終行を表示します。

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat` は、さまざまなネットワーク統計をイントロスペクションできます。ここでは、概要出力を指定して呼び出されています。 ここには、問題によっては非常に役立つフィールドがあります。 TCP セクションにある役立つフィールドの 1 つは "failed connection attempts" (失敗した接続試行回数) です。 これは、SNAT ポートの枯渇、または送信接続の実行に関連したその他の問題を示している可能性があります。 再送信されたセグメント (これも TCP セクションにあります) の高い比率は、パケット配信の問題を示している可能性があります。 
