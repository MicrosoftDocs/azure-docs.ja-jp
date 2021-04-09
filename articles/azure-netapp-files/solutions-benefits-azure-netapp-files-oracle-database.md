---
title: Oracle Database での Azure NetApp Files 利用のメリット | Microsoft Docs
description: テクノロジーについて説明し、Oracle Direct NFS (dNFS) と従来の NFS クライアントのパフォーマンスを比較します。 Azure NetApp Files で dNFS を使用する利点を示します。
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932500"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Oracle Database での Azure NetApp Files 利用のメリット

Oracle Direct NFS (dNFS) を使用すると、オペレーティング システム独自の NFS ドライバーよりも高いパフォーマンスを実現できます。 この記事では、このテクノロジーについて説明し、dNFS と従来の NFS クライアント (カーネル NFS) のパフォーマンスを比較します。 また、Azure NetApp Files で dNFS を使用する利点と使いやすさについても説明します。  

## <a name="how-oracle-direct-nfs-works"></a>Oracle Direct NFS のしくみ

次の概要では、Oracle Direct NFS のしくみについて簡単に説明します。

* Oracle Direct NFS は、オペレーティング システムのバッファー キャッシュをバイパスします。 データはユーザー空間に 1 回だけキャッシュされるため、メモリ コピーのオーバーヘッドがなくなります。  

* 従来の NFS クライアントでは、次に示すように、単一のネットワーク フローが使用されます。    

    ![単一のネットワーク フローを使用する従来の NFS クライアント](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS では、複数のネットワーク フロー間でネットワーク トラフィックを負荷分散することでパフォーマンスがさらに向上します。 テストの結果、次に示すように、650 の異なるネットワーク接続が Oracle Database によって動的に確立されました。  

    ![Oracle Direct NFS によるパフォーマンスの向上](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Direct NFS に関する Oracle FAQ](http://www.orafaq.com/wiki/Direct_NFS) のページには、Oracle dNFS が最適化された NFS クライアントであることが示されています。 これは、TCP/IP 経由でアクセス可能な、NAS ストレージ デバイス上にある NFS ストレージへの高速でスケーラブルなアクセスを提供します。 dNFS は、主に DAS または SAN ストレージで使用される ASM と同様に、データベース カーネルに組み込まれています。 そのため、"*ガイドラインとして、NAS ストレージを実装する場合は dNFS を使用し、SAN ストレージを実装する場合は ASM を使用します*"。

dNFS は、Oracle 18c の既定のオプションです。

dNFS は、Oracle Database 11g 以降で使用できます。 次の図は、dNFS とネイティブ NFS を比較したものです。 dNFS を使用する場合、Azure 仮想マシン上で実行される Oracle データベースは、ネイティブ NFS クライアントよりも多くの I/O を実行できます。

![Oracle と Azure NetApp Files の dNFS とネイティブ NFS の比較](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

2 つのコマンドを実行してデータベースを再起動することで、dNFS を有効または無効にできます。

有効にするには:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

無効にするには:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Oracle Direct NFS と組み合わせた Azure NetApp Files

Azure NetApp Files サービスを使用すると、Oracle dNFS のパフォーマンスを向上させることができます。 このサービスを使用すると、アプリケーションのパフォーマンスを完全に制御できます。 非常に要求の厳しいアプリケーションにも対応できます。 Oracle dNFS と Azure NetApp Files の組み合わせは、ワークロードに大きなメリットをもたらします。

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files を使用したソリューション アーキテクチャ](azure-netapp-files-solution-architectures.md)
- [Azure での Oracle アプリケーションおよびソリューションの概要](../virtual-machines/workloads/oracle/oracle-overview.md)