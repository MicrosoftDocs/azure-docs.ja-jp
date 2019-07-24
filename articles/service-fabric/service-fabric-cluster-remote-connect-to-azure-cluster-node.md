---
title: Azure Service Fabric クラスター ノードへのリモート接続 | Microsoft Docs
description: スケール セット インスタンス (Service Fabric クラスター ノード) にリモートで接続する方法を説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663776"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>仮想マシン スケール セット インスタンスまたはクラスター ノードにリモート接続する
Azure で実行される Service Fabric クラスターでは、定義するクラスター ノードの種類ごとに、[仮想マシンの個別のスケールを設定](service-fabric-cluster-nodetypes.md)します。  特定のスケール セット インスタンス (クラスター ノード) にリモート接続することができます。  単一インスタンス VM とは異なり、スケール セット インスタンスには独自の仮想 IP アドレスがありません。 そのため、特定のインスタンスにリモート接続するために使用できる IP アドレスとポートの検索が難しい場合があります。

特定のインスタンスにリモート接続するために使用できる IP アドレスとポートを検索するには、以下の手順を実行します。

1. リモート デスクトップ プロトコル (RDP) の受信 NAT 規則を取得します。

    通常、クラスターで定義されている各ノード型には、独自の仮想 IP アドレスと専用のロード バランサーがあります。 既定では、ノード型のロード バランサーは、次の形式で名前が付けられます: *LB-{cluster-name}-{node-type}* (例: *LB-mycluster-FrontEnd*)。 
    
    Azure portal のロード バランサー ページで、**[設定]** > **[受信 NAT 規則]** の順に選択します。 

    ![ロード バランサーの受信 NAT 規則](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    次のスクリーン ショットは、FrontEnd という名前のノード型の受信 NAT 規則を示しています。 

    ![ロード バランサーの受信 NAT 規則](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    ノードごとに、**[DESTINATION]** 列に IP アドレス、**[TARGET]** 列にスケール セットのインスタンス、**[SERVICE]** 列にポート番号が表示されます。 リモート接続の場合は、ポートはポート 3389 から始まる昇順で各ノードに割り当てられます。

    受信 NAT 規則は、クラスターの Resource Manager テンプレートの `Microsoft.Network/loadBalancers` セクションでも見つけることができます。
    
2. 受信ポートのノードのターゲット ポートへのマッピングを確認するには、その規則をクリックして **[ターゲット ポート]** の値を調べます。 次のスクリーン ショットは、前の手順の **FrontEnd (インスタンス 1)** ノードの受信 NAT 規則を示しています。 (受信) ポート番号は 3390 ですが、ターゲット ポートはターゲット上の RDP サービスのポートであるポート 3389 にマップされることに注目してください。  

    ![ターゲット ポートのマッピング](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Windows クラスターの場合、ターゲット ポートは既定では、ターゲット ノード上の RDP サービスにマップされるポート 3389 です。 Linux クラスターの場合、ターゲット ポートは Secure Shell (SSH) サービスにマップされるポート 22 です。

3. 特定のノード (スケール セット インスタンス) にリモート接続します。 クラスターまたはその他の構成した資格情報を作成したときに設定したユーザー名とパスワードを使用することができます。 

    次のスクリーンショットでは、リモート デスクトップ接続を使用した、Windows クラスター内の **FrontEnd (インスタンス 1)** ノードへの接続を示しています。
    
    ![リモート デスクトップ接続](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Linux ノード上では、SSH で接続できます (次の例では、簡略化のために同じ IP アドレスとポートを再利用しています)。

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


次の手順については、次の記事を参照してください。
* ["任意の場所にデプロイ" 機能の概要と Azure で管理されるクラスターとの比較](service-fabric-deploy-anywhere.md)に関するページを参照します。
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* 展開後にクラスター VM で [RDP ポートの範囲値を更新する](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* クラスター VM の[管理者ユーザー名とパスワードを変更する](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

