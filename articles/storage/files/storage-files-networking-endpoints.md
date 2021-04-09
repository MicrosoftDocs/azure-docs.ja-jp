---
title: Azure Files ネットワーク エンドポイントの構成 | Microsoft Docs
description: Azure File ネットワーク エンドポイントを構成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 079d7aa9b654a318c7269a41605c3e146b08f127
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621333"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files ネットワーク エンドポイントの構成

Azure Files では、Azure ファイル共有にアクセスするための次の主な 2 種類のエンドポイントが提供されます。 
- パブリック エンドポイント。パブリック IP アドレスを持ち、世界中のどこからでもアクセスできます。
- プライベート エンドポイント。仮想ネットワーク内に存在し、その仮想ネットワークのアドレス空間内からのプライベート IP アドレスを持ちます。

パブリックおよびプライベート エンドポイントは、Azure ストレージ アカウントに存在します。 ストレージ アカウントは、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。

この記事では、Azure ファイル共有に直接アクセスするためのストレージ アカウントのエンドポイントを構成する方法について説明します。 このドキュメントに記載されている詳細のほとんどは、Azure File Sync とストレージ アカウントのパブリック エンドポイントおよびプライベート エンドポイントとの相互運用方法にも適用されますが、Azure File Sync のデプロイに対するネットワークに関する考慮事項の詳細については、[Azure File Sync プロキシとファイアウォールの設定の構成](storage-sync-files-firewall-and-proxy.md)に関する記事を参照してください。

このハウツー ガイドを読む前に、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を読むことをお勧めします。

## <a name="prerequisites"></a>前提条件

- この記事では、既に Azure サブスクリプションが作成されていることを前提としています。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
- この記事では、オンプレミスから接続するストレージ アカウントに Azure ファイル共有が既に作成されていることを前提としています。 Azure ファイル共有を作成する方法については、「[Azure ファイル共有を作成する](storage-how-to-create-file-share.md)」をご覧ください。
- Azure PowerShell を使用する場合は、[最新バージョンをインストールしてください](/powershell/azure/install-az-ps)。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](/cli/azure/install-azure-cli)。

## <a name="endpoint-configurations"></a>エンドポイント構成

ストレージ アカウントへのネットワーク アクセスを制限するようにエンドポイントを構成できます。 ストレージ アカウントへのアクセスを仮想ネットワークに制限するには、次の 2 つの方法があります。

- [ストレージ アカウントに 1 つ以上のプライベート エンドポイントを作成](#create-a-private-endpoint)し、パブリック エンドポイントへのアクセスをすべて制限します。 これで、そのストレージ アカウント内の Azure ファイル共有にアクセスできるのは、特定の仮想ネットワーク内から送信されたトラフィックだけになります。
- [パブリック エンドポイントを 1 つ以上の仮想ネットワークに制限します](#restrict-public-endpoint-access)。 これは、"*サービス エンドポイント*" と呼ばれる仮想ネットワークの機能を使用して実現できます。 サービス エンドポイントを使用してストレージ アカウントへのトラフィックを制限する場合、そのストレージ アカウントには引き続きパブリック IP アドレス経由でアクセスしますが、アクセスは構成で指定されている場所からのみ可能です。

### <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

ストレージ アカウント用のプライベート エンドポイントを作成すると、次の Azure リソースがデプロイされます。

- **プライベート エンドポイント**: ストレージ アカウントのプライベート エンドポイントを表す Azure リソース。 これは、ストレージ アカウントとネットワーク インターフェイスを接続するリソースと考えることができます。
- **ネットワーク インターフェイス (NIC)** : 指定された仮想ネットワークおよびサブネット内のプライベート IP アドレスを保持するネットワーク インターフェイス。 これは、仮想マシンをデプロイするとデプロイされるリソースとまったく同じものですが、VM に割り当てられるのではなく、プライベート エンドポイントによって所有されます。
- **プライベート DNS ゾーン**: 以前にこの仮想ネットワークに対してプライベート エンドポイントをデプロイしたことがない場合は、新しいプライベート DNS ゾーンが仮想ネットワークにデプロイされます。 この DNS ゾーン内のストレージ アカウントに対しては、DNS A レコードも作成されます。 この仮想ネットワークにプライベート エンドポイントを既にデプロイしてある場合は、ストレージ アカウントに対する新しい A レコードが、既存の DNS ゾーンに追加されます。 DNS ゾーンのデプロイは省略できますが、強くお勧めします。また、AD サービス プリンシパルまたは FileREST API を使用して Azure ファイル共有をマウントする場合は、必須です。

> [!Note]  
> この記事では、Azure パブリック リージョンを対象に、ストレージ アカウントの DNS サフィックス `core.windows.net` を使用しています。 この解説は、Azure ソブリン クラウド (Azure US Government クラウドや Azure China クラウドなど) にも当てはまります。ご利用の環境の適切なサフィックスに置き換えてください。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>接続を検証する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ご使用の仮想ネットワーク内に仮想マシンがある場合、または「[Azure Files の DNS 転送の構成](storage-files-networking-dns.md)」で説明されているような DNS 転送を構成してある場合は、PowerShell、コマンド ライン、またはターミナル (Windows、Linux、または macOS で動作) から次のコマンドを実行して、ご使用のプライベート エンドポイントが正しく設定されていることをテストできます。 `<storage-account-name>` を適切なストレージ アカウント名に置き換える必要があります。

```
nslookup <storage-account-name>.file.core.windows.net
```

すべてが正常に動作した場合は、次のような出力が表示されます。`192.168.0.5` は、仮想ネットワーク内のプライベート エンドポイントのプライベート IP アドレスです (Windows の場合に表示される出力)。

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ご使用の仮想ネットワーク内に仮想マシンがある場合、または「[Azure Files の DNS 転送の構成](storage-files-networking-dns.md)」で説明されているような DNS 転送を構成してある場合は、次のコマンドを使用して、ご使用のプライベート エンドポイントが正しく設定されていることをテストできます。

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

すべてが正常に動作した場合は、次のような出力が表示されます。`192.168.0.5` は、仮想ネットワーク内のプライベート エンドポイントのプライベート IP アドレスです。

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ご使用の仮想ネットワーク内に仮想マシンがある場合、または「[Azure Files の DNS 転送の構成](storage-files-networking-dns.md)」で説明されているような DNS 転送を構成してある場合は、次のコマンドを使用して、ご使用のプライベート エンドポイントが正しく設定されていることをテストできます。

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

すべてが正常に動作した場合は、次のような出力が表示されます。`192.168.0.5` は、仮想ネットワーク内のプライベート エンドポイントのプライベート IP アドレスです。 ファイル共有をマウントするには、`privatelink` パスではなく、storageaccount.file.core.windows.net を引き続き使用する必要があります。

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>パブリック エンドポイント アクセスを制限する

パブリック エンドポイント アクセスを制限するには、まず、パブリック エンドポイントへの一般的なアクセスを無効にする必要があります。 パブリック エンドポイントへのアクセスを無効にしても、プライベート エンドポイントには影響しません。 パブリック エンドポイントが無効になったら、そこに引き続きアクセスできる特定のネットワークまたは IP アドレスを選択できます。 一般に、ストレージ アカウントのほとんどのファイアウォール ポリシーではネットワーク アクセスが 1 つ以上の仮想ネットワークに制限されます。

#### <a name="disable-access-to-the-public-endpoint"></a>パブリック エンドポイントへのアクセスを無効にする

パブリック エンドポイントへのアクセスが無効にされている場合でも、そのプライベート エンドポイントを介してストレージ アカウントにアクセスできます。 それ以外の場合は、[特に許可されているソース](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)からのものでない限り、ストレージ アカウントのパブリック エンドポイントへの有効な要求は拒否されます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>パブリック エンドポイントへのアクセスを特定の仮想ネットワークに制限する

ストレージ アカウントを特定の仮想ネットワークに制限すると、指定した仮想ネットワーク内からパブリック エンドポイントへの要求が許可されます。 これは、"*サービス エンドポイント*" と呼ばれる仮想ネットワークの機能を使用して実現できます。 これは、プライベート エンドポイントの有無に関係なく使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>関連項目

- [Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)
- [Azure Files の DNS 転送の構成](storage-files-networking-dns.md)
- [Azure Files 用のサイト間 VPN の構成](storage-files-configure-s2s-vpn.md)