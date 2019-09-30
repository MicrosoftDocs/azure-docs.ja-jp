---
title: Azure クラシック CLI を使用してネットワーク セキュリティ グループ (クラシック) を作成する | Microsoft Docs
description: Azure クラシック CLI を使用してネットワーク セキュリティ グループ (クラシック) を作成し、展開する方法を説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: be63cdc3df5752f73b21bb5adc5fffaa364e7f43
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056719"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Azure クラシック CLI を使用してネットワーク セキュリティ グループ (クラシック) を作成する
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイ モデルについて説明します。 [Resource Manager デプロイ モデルで NSG を作成](tutorial-filter-network-traffic-cli.md)することもできます。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下の Azure CLI コマンドの例では、シナリオに基づいて単純な環境が既に作成されていものとします。 このドキュメントに表示されているコマンドを実行する場合は、まず、 [VNet を作成して](virtual-networks-create-vnet-classic-cli.md)テスト環境を構築します。

## <a name="create-an-nsg-for-the-front-end-subnet"></a>フロントエンドのサブネットの NSG を作成する

1. Azure CLI の使用経験がない場合は、「[Azure CLI のインストールと構成](/cli/azure/install-cli-version-1.0)」をご覧ください。
2. クラシック モードに切り替えます。

    ```azurecli
    azure config mode asm
    ```   

3. NSG を作成します。
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. インターネットからポート 3389 (RDP) へのアクセスを許可するセキュリティ規則を作成します。
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. インターネットからポート 80 (HTTP) へのアクセスを許可する規則を作成します。
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. NSG をフロントエンドのサブネットに関連付けます。
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>バックエンドのサブネットの NSG を作成します。

1. NSG を作成します。
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. フロントエンドのサブネットからポート 1433 (SQL) へのアクセスを許可する規則を作成します。
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. インターネットへのアクセスを禁止する規則を作成します。
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. NSG をバックエンドのサブネットに関連付けます。
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```