---
title: PowerShell を使用してネットワーク セキュリティ グループ (クラシック) を作成する | Microsoft Docs
description: PowerShell を使ってネットワーク セキュリティ グループ (クラシック) を作成し、展開する方法を説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5ef80ccd01c9c6979fd95d161d97d0dfaab58b24
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056668"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>PowerShell を使用してネットワーク セキュリティ グループ (クラシック) を作成する
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイ モデルについて説明します。 [Resource Manager デプロイ モデルで NSG を作成](tutorial-filter-network-traffic.md)することもできます。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下の PowerShell のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行する場合は、まず、 [VNet を作成して](virtual-networks-create-vnet-classic-netcfg-ps.md)テスト環境を構築します。

## <a name="create-an-nsg-for-the-front-end-subnet"></a>フロントエンドのサブネットの NSG を作成する

1. Azure PowerShell を初めて使う場合は、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページをご覧ください。

2. *NSG-FrontEnd* という名前のネットワーク セキュリティ グループを作成します。

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. インターネットからポート 3389 へのアクセスを許可するセキュリティ規則を作成します。

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. インターネットからポート 80 へのアクセスを許可するセキュリティ規則を作成します。

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>バックエンドのサブネットの NSG を作成します。

1. *NSG-BackEnd* という名前のネットワーク セキュリティ グループを作成します。
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. フロントエンドのサブネットからポート 1433 (SQL Server で使われる既定のポート) へのアクセスを許可するセキュリティ規則を作成します。
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. サブネットからインターネットへのアクセスをブロックするセキュリティ規則を作成します。
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```