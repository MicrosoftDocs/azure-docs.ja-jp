---
title: Azure CLI を使用して Azure プライベート エンドポイントを作成する | Microsoft Docs
description: Azure プライベート エンドポイントについて学習します。
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 30994133b19c4f59ae9e8be26caffe14348638f6
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219374"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Azure CLI を使用してプライベート エンドポイントを作成する
プライベート エンドポイントは、Azure の Private Link の基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 このクイック スタートでは、Azure CLI を使用して、仮想ネットワーク上の VM と、プライベート エンドポイントを含む SQL Database サーバーを作成する方法を説明します。 その後、VM にアクセスし、Private Link リソース (この例ではプライベート Azure SQL Database サーバー) に安全にアクセスできます。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに Azure CLI をローカルにインストールして使用する場合は、このクイック スタートには Azure CLI バージョン 2.0.28 以降を使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソースを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループを場所 *westcentralus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
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
Azure では仮想ネットワーク内のサブネットにリソースがデプロイされるため、プライベート エンドポイントのネットワーク ポリシーを無効にするようにサブネットを作成または更新する必要があります。 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) を使用して  *mySubnet**  という名前のサブネット構成を更新します。

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>VM の作成 
az vm create を使用して VM を作成します。 メッセージが表示されたら、VM のサインイン資格情報として使用するパスワードを入力します。 この例では、 *myVm* という名前の VM を作成します。 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 VM のパブリック IP アドレスを書き留めます。 このアドレスは、次の手順でインターネットから VM に接続するために使用します。

## <a name="create-a-sql-database-server"></a>SQL Database サーバーを作成する 
az sql server create コマンドを使用して SQL Database サーバーを作成します。 SQL Server の名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に置き換えることを忘れないでください。 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

SQL Server ID は  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` に似ています。次の手順で SQL Server ID を使用します。 

## <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する 
仮想ネットワークに SQL Database サーバーのプライベート エンドポイントを作成します。 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する 
SQL Database サーバー ドメイン用のプライベート DNS ゾーンを作成し、仮想ネットワークに対する関連付けリンクを作成します。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM *myVm* に接続します。

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. ダウンロードした .rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。  

## <a name="access-sql-database-server-privately-from-the-vm"></a>VM から SQL Database サーバーにプライベートにアクセスする

このセクションでは、プライベート エンドポイントを使用して VM から SQL Database サーバーに接続します。

 1.  *myVM* のリモート デスクトップで、PowerShell を開きます。
 2. nslookup myserver.database.windows.net を入力します。 次のようなメッセージを受け取ります。 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. SQL Server Management Studio をインストールします。 
 4. [サーバーに接続] で、次の情報を入力または選択します。サーバーの種類:[データベース エンジン] を選択します。
 サーバー名:myserver.database.windows.net を選択します。ユーザー名:作成時に指定したユーザー名を入力します。
 Password (パスワード):作成時に指定したパスワードを入力します。
 パスワードを保存する:[はい] を選択します。
 
 5.  **[接続]** を選択します。
 6. 左側のメニューで **[データベース]** を参照します。
 7. (省略可能) 情報を作成するか、*mydatabase* に対して情報のクエリを実行します。
 8. *myVm* へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ 
不要になったら、az group delete を使用して、リソース グループとそのすべてのリソースを削除できます。 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>次の手順
- [Azure Private Link](private-link-overview.md) の詳細
 
