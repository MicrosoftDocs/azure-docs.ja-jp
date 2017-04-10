---
title: "Azure CLI を使用してクラシック モードで NSG を作成する方法 | Microsoft Docs"
description: "Azure CLI を使用してクラシック モードで NSG を作成してデプロイする方法について"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 115a1937a4c88ba2b986a40c84b1b759ed5e03b5
ms.lasthandoff: 03/21/2017


---
# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Azure CLI で NSG (クラシック) を作成する方法
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイメント モデルについて説明します。 [リソース マネージャーのデプロイメント モデルで NSG を作成](virtual-networks-create-nsg-arm-cli.md)することもできます。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下の Azure CLI のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行する場合は、まず、 [VNet を作成して](virtual-networks-create-vnet-classic-cli.md)テスト環境を構築します。

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>フロントエンドのサブネットの NSG を作成する方法
上記のシナリオに基づいて **NSG-FrontEnd** という名前の NSG を作成するには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **`azure config mode`** コマンドを実行してクラシック モードに切り替えます。
   
        azure config mode asm
   
    予想される出力:
   
        info:    New mode is asm
3. **`azure network nsg create`** コマンドを実行して NSG を作成します。
   
        azure network nsg create -l uswest -n NSG-FrontEnd
   
    予想される出力:
   
        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK
   
    パラメーター:
   
   * **-l (または --location)**。 NSG が作成される Azure リージョンです。 ここでは、 *westus*です。
   * **-n (または --name)**。 新しい NSG の名前です。 ここでは、 *NSG-FrontEnd*です。
4. **`azure network nsg rule create`** コマンドを実行して、インターネットからポート 3389 (RDP) へのアクセスを許可する規則を作成します。
   
        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   
    予想される出力:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK
   
    パラメーター:
   
   * **-a (または --nsg-name)**。 規則が作成される NSG の名前です。 ここでは、 *NSG-FrontEnd*です。
   * **-n (または --name)**。 新しい規則の名前です。 ここでは、 *rdp-rule*です。
   * **-c (または --action)**。 規則のアクセス レベルです (拒否または許可)。
   * **-p (または --protocol)**。 規則のプロトコル (TCP、UDP、または *) です。
   * **-r (または --type)**。 接続の方向です (受信または送信)。
   * **-y (または --priority)**。 規則の優先度です。
   * **-f (または --source-address-prefix)**。 CIDR または既定のタグを使用する発信元アドレスのプレフィックス。
   * **-o (または --source-port-range)**。 発信元ポート、またはポート範囲です。
   * **-e (または --destination-address-prefix)**。 CIDR または既定のタグを使用する接続先アドレスのプレフィックス。
   * **-u (または --destination-port-range)**。 接続先ポート、またはポート範囲です。
5. **`azure network nsg rule create`** コマンドを実行して、インターネットからポート 80 (HTTP) へのアクセスを許可する規則を作成します。
   
        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
   
    予想される出力:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK
6. **`azure network nsg subnet add`** コマンドを実行して、NSG をフロントエンドのサブネットにリンクします。
   
        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   
    予想される出力:
   
        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>バックエンドのサブネットの NSG を作成する方法
上記のシナリオに基づいて *NSG-BackEnd* という名前の NSG を作成するには、次の手順に従います。

1. **`azure network nsg create`** コマンドを実行して NSG を作成します。
   
        azure network nsg create -l uswest -n NSG-BackEnd
   
    予想される出力:
   
        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK
   
    パラメーター:
   
   * **-l (または --location)**。 NSG が作成される Azure リージョンです。 ここでは、 *westus*です。
   * **-n (または --name)**。 新しい NSG の名前です。 ここでは、 *NSG-FrontEnd*です。
2. **`azure network nsg rule create`** コマンドを実行して、フロントエンドのサブネットからポート 1433 (SQL) へのアクセスを許可する規則を作成します。
   
        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   
    予想される出力:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK
3. **`azure network nsg rule create`** コマンドを実行して、インターネットへのアクセスを拒否する規則を作成します。
   
        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   
    予想される出力:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK
4. **`azure network nsg subnet add`** コマンドを実行して、NSG をバックエンドのサブネットにリンクします。
   
        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
   
    予想される出力:
   
        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK


