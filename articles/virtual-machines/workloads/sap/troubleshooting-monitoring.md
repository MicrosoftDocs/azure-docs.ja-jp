---
title: SAP HANA on Azure (L インスタンス) の監視 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) を監視します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 246b9398824597ec337ee9e9ea3dc24267311f60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669592"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure (L インスタンス) の監視方法

SAP HANA on Azure (L インスタンス) に、その他の IaaS デプロイとの違いはありません。OS とアプリケーションが何を行っているかと、アプリケーションがどのように以下のリソースを使用しているかを監視する必要があります。

- CPU
- メモリ
- ネットワークの帯域幅
- ディスク領域

Azure Virtual Machines で、上に挙げたリソース クラスが十分であるか、不足しているかを把握する必要があります。 各クラスの詳細は次のとおりです。

**CPU リソースの使用量:** メモリに保存されているデータの操作に使用できる十分な CPU リソースを確保するために、HANA に対する特定のワークロード用に SAP が定義した比率が強制されます。 しかし、HANA がクエリを実行し、インデックスが見つからないなどの問題のために大量の CPU を使用する場合もあります。 つまり、HANA L インスタンス ユニットによる CPU リソースの使用量と、特定の HANA サービスによって使用されている CPU リソースを監視する必要があります。

**メモリの使用量:** HANA 内部からだけでなく、ユニット上の HANA の外部からも監視することが重要です。 HANA 内部では、HANA に割り当てられているメモリをデータがどのように使用しているかを監視して、SAP の必要サイズのガイドライン内に収まるようにします。 追加でインストールした非 HANA ソフトウェアがメモリを過剰に使用して HANA とメモリを奪い合っていないことを確認するために、L インスタンス レベルのメモリ使用量を監視する場合もあります。

**ネットワーク帯域幅:** Azure VNet ゲートウェイは Azure VNet に移動するデータの帯域幅による制限を受けるので、VNet 内のすべての Azure VM が受信するデータを監視することは、選択した Azure ゲートウェイ SKU の上限にどのくらい近づいているかを判断するうえで役に立ちます。 HANA L インスタンス ユニットでは、着信および発信ネットワーク トラフィックを監視することと、処理量の推移を追跡することにも意味があります。

**ディスク領域:** ディスク領域の使用量は、通常、時間の経過と共に増加します。 最も一般的な原因は、データ量の増加、トランザクション ログ バックアップの実行、トレース ファイルの保存、およびストレージ スナップショットの実行です。 そのため、ディスク領域の使用量を監視し、HANA L インスタンス ユニットに関連付けられているディスク領域を管理することが重要です。

HANA L インスタンスの **Type II SKU** の場合、サーバーには事前に読み込まれたシステム診断ツールが付属しています。 システムの正常性チェックを実行するために、これらの診断ツールを利用できます。 正常性チェックのログ ファイルを /var/log/health_check に生成するには、次のコマンドを実行します。
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Microsoft サポート チームと協力して問題のトラブルシューティングを行う場合は、これらの診断ツールを使用してログ ファイルを提供するように求められることもあります。 次のコマンドを使用して、ファイルを圧縮することができます。
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**次の手順**

- 「[SAP HANA on Azure (L インスタンス) の監視方法](./hana-monitor-troubleshoot.md)」を参照してください。
