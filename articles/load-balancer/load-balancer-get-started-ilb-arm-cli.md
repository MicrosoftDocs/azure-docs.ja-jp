---
title: "内部ロード バランサーの作成 - Azure CLI | Microsoft Docs"
description: "リソース マネージャーで Azure CLI を使用して、内部ロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 128b91c685b5f7e494a69ca5b04165a0ee7cbb78
ms.lasthandoff: 03/21/2017

---

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Azure CLI を使用した内部ロード バランサーの作成

> [!div class="op_single_selector"]
> * [[アクセス ポリシー]](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](load-balancer-get-started-ilb-classic-cli.md)ではなくこのモデルをお勧めします。

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Azure CLI を使用したソリューションのデプロイ

次の手順では、CLI で Azure Resource Manager を使用して、インターネットに接続するロード バランサーを作成する方法を示します。 Azure Resource Manager では、それぞれのリソースは個別に作成され構成された後、リソースを作成するためにまとめられます。

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

* **フロントエンド IP 構成**: 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。
* **バックエンド アドレス プール**: ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。
* **負荷分散規則**: ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートにマッピングする規則が含まれます。
* **受信 NAT 規則**: ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。
* **プローブ**: バックエンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

詳細については、「 [Azure Resource Manager によるロード バランサーのサポート](load-balancer-arm.md)」を参照してください。

## <a name="set-up-cli-to-use-resource-manager"></a>Resource Manager を使用するための CLI のセットアップ

1. Azure CLI の使用経験がない場合は、「 [Azure CLI をインストールして構成する](../cli-install-nodejs.md)」を参照してください。 手順に従い、Azure アカウントとサブスクリプションを選択する段階まで進めます。
2. 次のように、 **azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

    ```azurecli
    azure config mode arm
    ```

    予想される出力:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>内部ロード バランサーの作成手順

1. Azure にサインインします。

    ```azurecli
    azure login
    ```

    メッセージが表示されたら、Azure の資格情報を入力します。

2. コマンド ツールを Azure Resource Manager モードに変更します。

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure Resource Manager のすべてのリソースは、リソース グループに関連付けられます。 まだ作成していない場合は、リソース グループを作成してください。

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>内部ロード バランサー セットの作成

1. 内部ロード バランサーを作成します。

    次のシナリオでは、"nrprg" という名前のリソース グループが米国東部リージョンで作成されています。

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > 仮想ネットワークや仮想ネットワーク サブネットなどの内部ロード バランサーのすべてのリソースは同じリソース グループ、同じリージョンにある必要があります。

2. 内部ロード バランサーのフロントエンド IP アドレスを作成します。

    使用する IP アドレスは、仮想ネットワークのサブネットの範囲内にある必要があります。

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. バックエンド アドレス プールを作成します。

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    フロントエンド IP アドレスとバックエンド アドレス プールを定義した後に、ロード バランサー規則や受信 NAT 規則を作成し、また正常性プローブをカスタマイズすることができます。

4. 内部ロード バランサーのロード バランサー規則を作成します。

    前の手順に従うと、フロントエンド プールでポート 1433 をリッスンし、負荷分散されたネットワーク トラフィックをバックエンド アドレス プールにポート 1433 を使用して送信する、ロード バランサー規則がコマンドにより作成されます。

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. 受信 NAT 規則を作成します。

    受信 NAT 規則は、ロード バランサー内で特定の仮想マシン インスタンスに移動するエンドポイントの作成に使用されます。 前の手順では、リモート デスクトップ用の&2; つの NAT 規則を作成しました。

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. ロード バランサーの正常性プローブを作成します。

    正常性プローブは、すべての仮想マシン インスタンスを確認し、ネットワーク トラフィックを送信できるかどうかを確認します。 プローブのチェックで失敗した仮想マシン インスタンスは、オンラインに戻り、プローブ チェックにより正常と判定されるまで、ロード バランサーから削除されます。

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > Microsoft Azure Platform は、さまざまな管理シナリオに静的でパブリックにルーティング可能な IPv4 アドレスを使用します。 IP アドレスは 168.63.129.16 です。 この IP アドレスはファイアウォールによってブロックされないように設定しておく必要があります。ブロックされると、予期しない動作が発生する可能性があるためです。
    > Azure 内部負荷分散については、この IP アドレスはロード バランサーからの監視プローブによって使用され、負荷分散セットでの仮想マシンの正常性状態が判別されます。 ネットワーク セキュリティ グループが、内部負荷分散セットで Azure の仮想マシンへのトラフィックを制限するために使用されている場合、または仮想ネットワーク サブネットに適用されている場合、168.63.129.16 からのトラフィックを許可するネットワーク セキュリティの規則が追加されていることを確認します。

## <a name="create-nics"></a>NIC の作成

NIC を作成し (あるいは、既存の NIC を変更し)、それを NAT 規則、ロード バランサー規則、プローブに関連付ける必要があります。

1. *lb-nic1-be* という名前の NIC を作成し、それを *rdp1* NAT 規則と *beilb* バックエンド アドレス プールに関連付けます。

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
    ```

    予想される出力:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. *lb-nic2-be* という名前の NIC を作成し、それを *rdp2* NAT 規則と *beilb* バックエンド アドレス プールに関連付けます。

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. *DB1* という名前の仮想マシンを作成し、それを *lb-nic1-be* という名前の NIC に関連付けます。 *web1nrp* と呼ばれるストレージ アカウントが次のコマンドを実行する前に作成されます。

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > ロード バランサーの VM は、同じ可用性セットに含まれている必要があります。 可用性セットを作成するには、 `azure availset create` を使用します。

4. *DB2* という名前の仮想マシン (VM) を作成し、それを *lb-nic2-be* という名前の NIC に関連付けます。 *web1nrp* と呼ばれるストレージ アカウントが次のコマンドを実行する前に作成されました。

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>ロード バランサーの削除

ロード バランサーを削除するには、次のコマンドを使用します。

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>次のステップ

[ソース IP アフィニティを使用したロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)


