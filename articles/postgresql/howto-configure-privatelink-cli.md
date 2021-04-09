---
title: Private Link - Azure CLI - Azure Database for PostgreSQL - 単一サーバー
description: Azure CLI から Azure Database for PostgreSQL 単一サーバー用のプライベート リンクを構成する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8aaebdd37f835201ef549e3f97e0c0b657e4fe9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020128"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>CLI を使用して Azure Database for PostgreSQL 単一サーバー用の Private Link を作成および管理する

プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 この記事では、Azure CLI を使用して Azure Virtual Network 内に VM を作成し、Azure プライベート エンドポイントを含む Azure Database for PostgreSQL 単一サーバーを作成する方法について説明します。

> [!NOTE]
> プライベート リンク機能は、General Purpose または Memory Optimized のいずれかの価格レベルの Azure Database for PostgreSQL サーバーにのみ使用可能です。 データベース サーバーがこれらの価格レベルのいずれであることを確実にします。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。

- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに Azure CLI をローカルにインストールして使用する場合は、このクイック スタートには Azure CLI バージョン 2.0.28 以降を使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソースを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*westeurope* の場所に *myResourceGroup* という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>仮想ネットワークを作成します
[az network vnet create](/cli/azure/network/vnet) を使用して仮想ネットワークを作成します。 この例では、*mySubnet* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>サブネットのプライベート エンドポイント ポリシーを無効にする 
Azure では仮想ネットワーク内のサブネットにリソースがデプロイされるため、プライベート エンドポイントの[ネットワーク ポリシー](../private-link/disable-private-endpoint-network-policy.md)を無効にするようにサブネットを作成または更新する必要があります。 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用して  *mySubnet*  という名前のサブネット構成を更新します。

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>VM の作成 
az vm create を使用して VM を作成します。 メッセージが表示されたら、VM のサインイン資格情報として使用するパスワードを入力します。 この例では、*myVm* という名前の VM を作成します。 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

 VM のパブリック IP アドレスを書き留めます。 このアドレスは、次の手順でインターネットから VM に接続するために使用します。

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 単一サーバーを作成する 
az postgres server create コマンドを使用して Azure Database for PostgreSQL を作成します。 PostgreSQL サーバーの名前は Azure 全体で一意である必要があるため、プレースホルダー値を、上記で使用した独自の一意の値に置き換えることを忘れないでください。 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する 
Virtual Network 内に PostgreSQL サーバーのプライベート エンドポイントを作成します。 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する 
PostgreSQL サーバー ドメイン用のプライベート DNS ゾーンを作成し、Virtual Network との関連付けリンクを作成します。 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> お客様の DNS 設定の FQDN は、構成されている非公開 IP では解決されません。 [こちら](../dns/dns-operations-recordsets-portal.md)で示すように、構成された FQDN の DNS ゾーンを設定する必要があります。

> [!NOTE]
> Azure Database for PostgreSQL と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションに **Microsoft.DBforPostgreSQL** リソース プロバイダーが登録されていることを確認してください。 詳細については、[リソース プロバイダー](../azure-resource-manager/management/resource-providers-and-types.md)に関する記事をご覧ください。

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM *myVm* に接続します。

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. *downloaded.rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>VM から PostgreSQL サーバーにプライベートにアクセスする

1. *myVM* のリモート デスクトップで、PowerShell を開きます。

2. 「 `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`」と入力します。 

   次のようなメッセージが返されます。

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. 使用可能な任意のクライアントを使用して、PostgreSQL サーバーのプライベートリンク接続をテストします。 次の例では [Azure Data Studio](/sql/azure-data-studio/download?view=sql-server-ver15&preserve-view=true) を使用して操作を実行します。

4. **[新しい接続]** で、この情報を入力または選択します。

   | 設定 | 値 |
   | ------- | ----- |
   | サーバーの種類| **[PostgreSQL]** を選択します。|
   | サーバー名| *[mydemopostgresserver.privatelink.postgres.database.azure.com]* を選択します。 |
   | ユーザー名 | PostgreSQL サーバーの作成時に指定されるユーザー名を「username@servername」として入力します。 |
   |Password |PostgreSQL サーバーの作成時に指定したパスワードを入力します。 |
   |SSL|**[必須]** を選択します。|
   ||

5. [接続] を選択します。

6. 左側のメニューでデータベースを参照します。

7. (省略可能) postgreSQL サーバーから情報を作成またはクエリします。

8. myVM へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする 
不要になったら、az group delete を使用して、リソース グループとそのすべてのリソースを削除できます。 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>次のステップ
- [Azure プライベート エンドポイントの概要](../private-link/private-endpoint-overview.md)について学習します。
