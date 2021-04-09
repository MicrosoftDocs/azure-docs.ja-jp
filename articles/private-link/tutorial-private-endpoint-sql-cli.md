---
title: 'チュートリアル: Azure プライベート エンドポイントを使用して Azure SQL サーバーに接続する - Azure CLI'
description: このチュートリアルでは、Azure CLI を使用して、Azure SQL サーバーとプライベート エンドポイントを作成する方法について説明します
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: a5562c5f40a321f5737fea73f6d7964b402953cb
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889214"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>チュートリアル: Azure プライベート エンドポイントを使用して Azure SQL サーバーに接続する - Azure CLI

Azure プライベート エンドポイントは、Azure における Private Link の基本的な構成要素です。 仮想マシン (VM) などの Azure リソースと Private Link リソースとの非公開での通信が可能になります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークと bastion ホストを作成します。
> * 仮想マシンを作成します。
> * Azure SQL サーバーとプライベート エンドポイントを作成します。
> * SQL サーバー プライベート エンドポイントへの接続をテストします。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure portal にサインインし、`az login` を実行して、ご利用のサブスクリプションがアクティブであることを確認します。
* `az --version` を実行して、ターミナルまたはコマンド ウィンドウの Azure CLI のバージョンを確認します。 最新バージョンについては、[最新のリリース ノート](/cli/azure/release-notes-azure-cli?tabs=azure-cli)を参照してください。
  * 最新バージョンを使用していない場合は、[オペレーティング システムまたはプラットフォーム用のインストール ガイド](/cli/azure/install-azure-cli)に従ってインストールを更新します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、次のようにリソース グループを作成します。

* 名前は **CreateSQLEndpointTutorial-rg** にします。 
* 場所は **eastus** にします。

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>仮想ネットワークと bastion ホストの作成

このセクションでは、仮想ネットワーク、サブネット、bastion ホストを作成します。 

bastion ホストは、プライベート エンドポイントをテストする目的で、仮想マシンに安全に接続するために使用されます。

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* アドレス プレフィックスは **10.0.0.0/16** にします。
* サブネットの名前は **myBackendSubnet** にします。
* サブネット プレフィックスは **10.0.0.0/24** にします。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。
* 場所は **eastus** にします。

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用してサブネットを更新し、プライベート エンドポイントのプライベート エンドポイント ネットワーク ポリシーを無効にします。

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、bastion ホストのパブリック IP アドレスを作成します。

* **myBastionIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使用して、bastion サブネットを作成します。

* 名前は **AzureBastionSubnet** にします。
* アドレス プレフィックスは **10.0.1.0/24** にします。
* 仮想ネットワークは **myVNet** にします。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) を使用して、bastion ホストを作成します。

* 名前: **myBastionHost**
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。
* パブリック IP **myBastionIP** に関連付けます。
* 仮想ネットワーク **myVNet** に関連付けます。
* 場所は **eastus** にします。

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

## <a name="create-test-virtual-machine"></a>テスト用の仮想マシンを作成する

このセクションでは、プライベート エンドポイントのテストに使用する仮想マシンを作成します。

 [az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 メッセージが表示されたら、VM の資格情報として使用するパスワードを入力します。

* 名前は **myVM** にします。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。
* ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* サーバー イメージは **Win2019Datacenter** にします。

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>Azure SQL サーバーを作成する

このセクションでは、SQL サーバーおよびデータベースを作成します。

[az sql server create](/cli/azure/sql/server#az_sql_server_create) を使用して、SQL サーバーを作成します。

* **\<sql-server-name>** を一意のサーバー名に置き換えます。
* **\<your-password>** をパスワードに置き換えます。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。
* リージョンは **eastus** にします。

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

[az sql db create](/cli/azure/sql/db#az_sql_db_create) を使用してデータベースを作成します。

* 名前は **myDataBase** にします。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。
* **\<sql-server-name>** を一意のサーバー名に置き換えます。

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

このセクションでは、プライベート エンドポイントを作成します。

[az sql server list](/cli/azure/sql/server#az_sql_server_list) を使用して、SQL サーバーのリソース ID をシェル変数に格納します。

[az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) を使用して、エンドポイントと接続を作成します。

* 名前は **myPrivateEndpoint** にします。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* 接続名は **myConnection** にします。

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する

このセクションでは、[az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create) を使用してプライベート DNS ゾーンを作成し、構成します。  

[az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) を使用して、DNS ゾーンへの仮想ネットワーク リンクを作成します。

[az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create) を使用して、DNS ゾーン グループを作成します。

* ゾーン名は **privatelink.database.windows.net** にします。
* 仮想ネットワークは **myVNet** にします。
* リソース グループは **CreateSQLEndpointTutorial-rg** にします。
* DNS リンク名は **myDNSLink** にします。
* **myPrivateEndpoint** に関連付けます。
* ゾーン グループ名は **MyZoneGroup** にします。

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>プライベート エンドポイントへの接続のテスト

このセクションでは、前の手順で作成した仮想マシンを使用し、プライベート エンドポイントを通じて SQL サーバーに接続します。

1. [Azure ポータル](https://portal.azure.com) 
 
2. 左側のナビゲーション ペインで **[リソース グループ]** を選択します。

3. **[CreateSQLEndpointTutorial-rg]** を選択します。

4. **[myVM]** を選択します。

5. **myVM** の [概要] ページで **[接続]** 、 **[Bastion]** の順に選択します。

6. 青色の **[Bastion を使用する]** ボタンを選択します。

7. 仮想マシンの作成時に入力したユーザー名とパスワードを入力します。

8. 接続後にサーバーで Windows PowerShell を開きます。

9. 「`nslookup <sqlserver-name>.database.windows.net`」と入力します。 **\<sqlserver-name>** は、前のステップで作成した SQL サーバーの名前に置き換えます。  以下に表示されるようなメッセージが返されます。

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    SQL サーバー名に対応する **10.0.0.5** というプライベート IP アドレスが返されます。  このアドレスは、先ほど作成した仮想ネットワークのサブネット内に存在します。


10. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) を **myVM** にインストールします。

11. **SQL Server Management Studio** を開きます。

12. **[サーバーに接続]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | サーバーの種類 | **データベース エンジン** を選択します。|
    | サーバー名 | 「 **\<sql-server-name>.database.windows.net**」と入力します |
    | 認証 | **[SQL Server 認証]** を選択します。 |
    | ユーザー名 | サーバー作成中に入力したユーザー名を入力します |
    | Password | サーバー作成中に入力したパスワードを入力します |
    | パスワードを保存する | **[はい]** を選択します。 |

13. **[接続]** を選択します。

14. 左側のメニューでデータベースを参照します。

15. (省略可能) 情報を作成するか、**mysqldatabase** に対して情報のクエリを実行します。

16. **myVM** への bastion 接続を閉じます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする 

プライベート エンドポイント、SQL サーバー、VM を使い終えたら、リソース グループとそこに含まれるすべてのリソースを削除します。 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下を作成しました。

* 仮想ネットワークと bastion ホスト。
* 仮想マシン。
* Azure SQL サーバーとプライベート エンドポイント。

仮想マシンを使用して、プライベート エンドポイントを介した SQL サーバーへの接続を安全にテストしました。

次のステップとして、**Azure SQL データベースへのプライベート接続を使用する Web アプリ** のアーキテクチャ シナリオを確認します。このシナリオでは、仮想ネットワークの外部にある Web アプリケーションをデータベースのプライベート エンドポイントに接続します。
> [!div class="nextstepaction"]
> [Azure SQL データベースへのプライベート接続を使用する Web アプリ](/azure/architecture/example-scenario/private-web-app/private-web-app)