---
title: Private Link - Azure CLI - Azure Database for MariaDB
description: Azure CLI から Azure Database for MariaDB 用のプライベート リンクを構成する方法について説明します
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a8f94c34281adbe274ad70425850e2ade4dc94ea
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833201"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>CLI を使用して Azure Database for MariaDB 用の Private Link を作成および管理する

プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 この記事では、Azure CLI を使用して Azure仮想ネットワーク内に VM を作成し、Azure プライベート エンドポイントを含む Azure Database for MariaDB サーバーを作成する方法について説明します。

> [!NOTE]
> プライベート リンク機能は、General Purpose または Memory Optimized の価格レベルの Azure Database for MariaDB サーバーでのみ使用可能です。 データベース サーバーがこれらの価格レベルのいずれかであることを確認します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。

- [Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-cli.md)。

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
Azure では仮想ネットワーク内のサブネットにリソースがデプロイされるため、プライベート エンドポイントの[ネットワーク ポリシー](../private-link/disable-private-endpoint-network-policy.md)を無効にするようにサブネットを作成または更新する必要があります。 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) を使用して  *mySubnet*  という名前のサブネット構成を更新します。

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

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーを作成する 
az mariadb server create コマンドを使用して Azure Database for MariaDB を作成します。 MariaDB サーバーの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に置き換えることを忘れないでください。 

```azurecli-interactive
# Create a server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> Azure Database for MariaDB と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションに **Microsoft.DBforMariaDB** リソース プロバイダーが登録されていることを確認してください。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

## <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する 
仮想ネットワーク内に MariaDB サーバーのプライベート エンドポイントを作成します。 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMariaDB/servers" --query "id") \    
    --group-id mariadbServer \  
    --connection-name myConnection  
 ```


## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する 
MariaDB サーバー ドメイン用のプライベート DNS ゾーンを作成し、仮想ネットワークとの関連付けリンクを作成します。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> お客様の DNS 設定の FQDN は、構成されている非公開 IP では解決されません。 [こちら](../dns/dns-operations-recordsets-portal.md)で示すように、構成された FQDN の DNS ゾーンを設定する必要があります。

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

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>VM から MariaDB サーバーにプライベートにアクセスする

1.  *myVM* のリモート デスクトップで、PowerShell を開きます。

2. 「 `nslookup mydemoserver.privatelink.mariadb.database.azure.com`」と入力します。 

    次のようなメッセージが返されます。
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. 利用可能な任意のクライアントを使用して、MariaDB サーバーのプライベート リンク接続をテストします。 次の例では、[MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) を使用して操作を行いました。

4. **[新しい接続]** で、この情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 接続名| ご自身で選んだ接続の名前を選択します。|
    | hostname | *mydemoserver.privatelink.mariadb.database.azure.com* を選択します |
    | ユーザー名 | MariaDB サーバーの作成時に指定したユーザー名を *username@servername* として入力します。 |
    | Password | MariaDB サーバーの作成時に指定したパスワードを入力します。 |
    ||

5. **[テスト接続]** または **[OK]** を選択します。

6. (省略可能) 左側のメニューからデータベースを参照して、MariaDB データベースからの情報を作成または照会します

8. myVm へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする 
不要になったら、az group delete を使用して、リソース グループとそのすべてのリソースを削除できます。 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>次のステップ
[Azure プライベート エンドポイントの概要](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)について学習します。

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
