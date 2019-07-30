---
title: Azure NetApp Files のパフォーマンス ベンチマーク | Microsoft Docs
description: ボリューム レベルでの Azure NetApp Files のパフォーマンス ベンチマーク テストの結果について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449504"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Azure NetApp Files のパフォーマンス ベンチマーク

この記事では、ボリューム レベルでの Azure NetApp Files のパフォーマンス ベンチマーク テストの結果について説明します。 

## <a name="sample-application-used-for-the-tests"></a>テストに使用するサンプル アプリケーション

パフォーマンス テストは、Azure NetApp Files を使用してサンプル アプリケーションで実行しました。 このアプリケーションには次のような特徴があります。 

* クラウド用に構築された Linux ベースのアプリケーションです。
* 必要に応じてコンピューティング能力を向上させるために、仮想マシン (VM) を追加して直線的にスケーリングすることができます。
* データ レイクへの迅速なアクセスが必要です。
* その I/O パターンはランダムの場合もあればシーケンシャルな場合もあります。 
    * ランダム パターンでは、大量の I/O に対して低待機時間が必要です。 
    * シーケンシャル パターンでは、大量の帯域幅が必要です。 

## <a name="about-the-workload-generator"></a>ワークロード ジェネレーターについて

結果は Vdbench 概要ファイルから得られます。 [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) は、ストレージ パフォーマンス検証用のディスク I/O ワークロードを生成するコマンドライン ユーティリティです。 使用するクライアント サーバー構成はスケーラブルです。  これには、単一の混合マスター/クライアントと 14 の専用のクライアント VM が含まれます。

## <a name="about-the-tests"></a>テストについて

テストは、サンプル アプリケーションが持つ可能性のある制限と、その制限に達するまでの応答時間を特定するように設計されました。  

次のテストが実行されました。 

* 100% 8 KiB ランダム読み取り
* 100% 8 KiB ランダム書き込み
* 100% 64 KiB シーケンシャル読み取り
* 100% 64 KiB シーケンシャル書き込み
* 50% 64 KiB シーケンシャル読み取り、50% 64 KiB シーケンシャル書き込み
* 50% 8 KiB ランダム読み取り、50% 8 KiB ランダム書き込み

## <a name="bandwidth"></a>帯域幅

Azure NetApp Files では、複数の[サービス レベル](azure-netapp-files-service-levels.md)が用意されています。 各サービス レベルでは、プロビジョニングされた容量 (ボリューム クォータ) の TiB ごとの帯域幅の量が異なります。 ボリュームの帯域幅の制限は、サービス レベルとボリューム クォータの組み合わせに基づいてプロビジョニングされます。 帯域幅の制限は、実現する実際のスループット量を決定する際の唯一の要因です。  

現在、テストの際にワークロードが単一ボリュームに対して達成した最高のスループットは 4,500 MiB です。  Premium サービス レベルの場合、70.31 TiB のボリューム クォータで、このスループットを実現するのに十分な帯域幅が次の計算に従ってプロビジョニングされます。 

![帯域幅の数式](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![クォータとサービス レベル](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>スループット集中型のワークロード

スループット テストでは、Vdbench と 12xD32s V3 ストレージ VM の組み合わせが使用されました。 テストのサンプル ボリュームでは、次のスループットの数値が達成されました。

![スループットのテスト](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O 集中型のワークロード

I/O テストでは、Vdbench と 12xD32s V3 ストレージ VM の組み合わせが使用されました。 テストのサンプル ボリュームでは、次の I/O の数値が達成されました。

![I/O テスト](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

テスト VM と Azure NetApp Files ボリュームとの距離は、I/O パフォーマンスに影響を与えます。  次のグラフは、2 つの異なる VM セットについて、IOPS の曲線と応答の待機時間の曲線と比較したものです。  VM の 1 つのセットは Azure NetApp Files の近くに置かれており、もう 1 つのセットはそれよりさらに離れた場所にあります。  より遠くにある VM のセットの待機時間の増大は、所与のレベルの並列処理で達成される IOPS の量に影響を与えます。  それにもかかわらず、以下に示すように、ボリュームに対する読み取りは 300,000 IOPS を超える可能性があります。 

![待機時間の検討](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>まとめ

待機時間の影響を受けやすいワークロード (データベース) の応答時間は、1 ミリ秒を示しています。 トランザクション パフォーマンスは、単一ボリュームの場合、300k IOPS を超えています。

スループットの影響を受けやすいアプリケーション (ストリーミングおよびイメージング用) のスループットは、4.5 GiB/s を示しています。

## <a name="example-scripts"></a>スクリプトの例

次のスクリプト例は、デモ目的でのみ提供されています。  運用目的で使用されるものではありません。  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
