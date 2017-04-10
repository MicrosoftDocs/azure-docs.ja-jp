---
title: "SQL AlwaysOn のロード バランサーの構成 |Microsoft Docs"
description: "SQL AlwaysOn と連携するようにロード バランサーを構成し、PowerShell を利用して SQL の実装用にロード バランサーを作成する方法"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 68aad6253f185d53fdd7f11c8660c7287ef12655
ms.lasthandoff: 01/11/2017

---

# <a name="configure-load-balancer-for-sql-always-on"></a>SQL AlwaysOn のロード バランサーの構成

SQL Server AlwaysOn 可用性グループは、ILB と共に実行できるようになりました。 可用性グループは、高可用性と障害復旧を目的とした SQL Server の主要なソリューションです。 可用性グループ リスナーによって、クライアント アプリケーションは、構成されているレプリカの数に関係なく、プライマリ レプリカにシームレスに接続できます。

リスナー (DNS) 名は負荷分散された IP アドレスにマップされ、Azure の Load Balancer によって、着信トラフィックがレプリカ セットのプライマリ サーバーのみに送られます。

SQL Server AlwaysOn (リスナー) エンドポイントでは、ILB のサポートを使用できます。 リスナーのアクセスを制御できるため、Virtual Network (VNet) の特定のサブネットから負荷分散された IP アドレスを選択できるようになりました。

リスナーで ILB を使用することで、SQL サーバー エンドポイント (例: Server=tcp:ListenerName,1433;Database=DatabaseName) には次の場所からのみアクセスできます。

* 同じ仮想ネットワーク内のサービスと VM
* 接続されたオンプレミス ネットワークのサービスと VM
* 相互接続された VNet のサービスと VM

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

図 1 - インターネットに接続するロード バランサーで構成された SQL AlwaysOn

## <a name="add-internal-load-balancer-to-the-service"></a>サービスに内部ロード バランサーを追加する

1. 次の例では、"Subnet-1" という名前のサブネットを含む仮想ネットワークを構成します。

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 各 VM で ILB の負荷分散エンドポイントを追加する

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    上の例では、sqlsvc1 および sqlsvc2 という 2 つの VM がクラウド サービス "Sqlsvc" で実行されます。 `DirectServerReturn` スイッチで ILB を作成したら、負荷分散されたエンドポイントを ILB に追加して、SQL が可用性グループのリスナーを構成できるようにします。

SQL AlwaysOn の詳細については、「[Azure の AlwaysOn 可用性グループに使用する内部ロード バランサーの構成](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)」をご覧ください。

## <a name="see-also"></a>関連項目
[インターネットに接続するロード バランサーの構成の開始](load-balancer-get-started-internet-arm-ps.md)

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

