---
title: Azure Storage でのプライベート エンドポイントの使用 | Microsoft Docs
description: 仮想ネットワークからストレージ アカウントへのセキュリティで保護されたアクセスのためのプライベート エンドポイントの概要。
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fff92057bc9812a5ef1488a46ed469382ad3ace3
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806883"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Azure Storage でのプライベート エンドポイントの使用 (プレビュー)

Azure Storage アカウントの[プライベート エンドポイント](../../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のクライアントは[プライベート リンク](../../private-link/private-link-overview.md)を介してデータに安全にアクセスできるようになります。 プライベート エンドポイントは、ストレージ アカウント サービスの VNet アドレス空間からの IP アドレスを使用します。 VNet 上のクライアントとストレージ アカウントの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由することで、パブリック インターネットからの露出を排除します。

ストレージ アカウントにプライベート エンドポイントを使用すると、次のことが可能になります。
- ストレージ サービスのパブリック エンドポイント上のすべての接続をブロックするようにストレージ ファイアウォールを構成することにより、ストレージ アカウントをセキュリティで保護します。
- VNnet からのデータの流出をブロックできるようにすることで、仮想ネットワーク (VNet) のセキュリティを強化します。
- [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoutes](../../expressroute/expressroute-locations.md) とプライベート ピアリングを使用して VNet に接続するオンプレミス ネットワークからストレージ アカウントに安全に接続します。

## <a name="conceptual-overview"></a>概念的概要
![Azure Storage のプライベート エンドポイントの概要](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

プライベート エンドポイントは、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) (VNet) 内の Azure サービス用の特別なネットワーク インターフェイスです。 ストレージ アカウントのプライベート エンドポイントを作成すると、VNet 上のクライアントとストレージ間の安全な接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。 プライベート エンドポイントとストレージ サービス間の接続には、セキュリティで保護されたプライベート リンクが使用されます。

VNet 内のアプリケーションは、プライベート エンドポイント経由でストレージ サービスにシームレスに接続できます。その際、**使用される接続文字列と承認メカニズムは、それを経由しない場合と同じものになります**。 プライベート エンドポイントは、ストレージ アカウントでサポートされているすべてのプロトコル (REST や SMB を含む) で使用できます。

プライベート エンドポイントは、[サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)を使用するサブネットに作成できます。 そのため、サブネット内のクライアントは、プライベート エンドポイントを使用して 1 つのストレージ アカウントに接続し、サービス エンドポイントを使用して他のユーザーにアクセスできます。

お使いの VNet でストレージ サービス用プライベート エンドポイントを作成すると、承認を得るために同意要求がストレージア カウント所有者に送信されます。 プライベート エンドポイントの作成を要求しているユーザーがストレージ アカウントの所有者でもある場合、この同意要求は自動的に承認されます。

ストレージ アカウントの所有者は、[Azure portal](https://portal.azure.com) でストレージ アカウントの *[プライベート エンドポイント]* タブを使用して、同意要求とプライベート エンドポイントを管理できます。

> [!TIP]
> ストレージ アカウントへのアクセスを、プライベート エンドポイント経由のみに制限する場合は、パブリック エンドポイント経由のアクセスを拒否または制御するようにストレージ ファイアウォールを構成します。

既定でパブリック エンドポイント経由のアクセスを拒否するように[ストレージ ファイアウォールを構成する](storage-network-security.md#change-the-default-network-access-rule)ことにより、VNet からの接続のみを受け入れるようにストレージ アカウントをセキュリティで保護することができます。 ストレージ ファイアウォールはパブリック エンドポイント経由のアクセスのみを制御するため、プライベート エンドポイントを持つ VNet からのトラフィックを許可するファイアウォール規則は必要ありません。 代わりに、プライベート エンドポイントは、ストレージ サービスへのアクセスをサブネットに許可するための同意フローに依存します。

### <a name="private-endpoints-for-storage-service"></a>ストレージ サービスのプライベート エンドポイント

プライベート エンドポイントを作成するときは、ストレージ アカウントと、その接続先のストレージ サービスを指定する必要があります。 アクセスする必要があるストレージ アカウント内のストレージ サービス (つまり、[BLOB](../blobs/storage-blobs-overview.md)、[Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)、[Files](../files/storage-files-introduction.md)、[キュー](../queues/storage-queues-introduction.md)、[Table](../tables/table-storage-overview.md)、または[静的な Web サイト](../blobs/storage-blob-static-website.md)) ごとに別々のプライベート エンドポイントが必要です。

> [!TIP]
> RA-GRS アカウントでの読み取りパフォーマンスを向上させるために、ストレージ サービスのセカンダリ インスタンスに対して別個のプライベート エンドポイントを作成します。

[読み取りアクセス geo 冗長ストレージ アカウント](storage-redundancy-grs.md#read-access-geo-redundant-storage)での読み取りを可能にするには、サービスのプライマリ インスタンスとセカンダリ インスタンスの両方に、別々のプライベート エンドポイントが必要です。 **フェールオーバー**用のセカンダリ インスタンスのプライベート エンドポイントを作成する必要はありません。 プライベート エンドポイントは、フェールオーバー後、新しいプライマリ インスタンスに自動的に接続されます。

#### <a name="resources"></a>リソース

ストレージ アカウントのプライベート エンドポイントを作成する方法の詳細情報については、次の記事を参照してください。

- [Azure portal のストレージ アカウント エクスペリエンスから非公開でストレージ アカウントに接続する](../../private-link/create-private-endpoint-storage-portal.md)
- [Azure portal でプライベート リンク センターを使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-portal.md)
- [Azure CLI を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-cli.md)
- [Azure PowerShell を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>プライベート エンドポイントへの接続

プライベート エンドポイントを使用する VNet 上のクライアントは、パブリック エンドポイントに接続するクライアントと同じ接続文字列をストレージ アカウントに対して使用する必要があります。 プライベート リンク経由の VNet からストレージ アカウントへの接続を自動的にルーティングするために、DNS 解決に依存しています。

> [!IMPORTANT]
> プライベート エンドポイントを利用しない場合と同じ接続文字列を使用して、プライベート エンドポイントを利用してストレージ アカウントに接続します。 "*privatelink*" サブドメイン URL を使用してストレージ アカウントに接続しないでください。

既定では、VNet に接続されている[プライベート DNS ゾーン](../../dns/private-dns-overview.md)が作成され、プライベート エンドポイントに必要な更新も行われます。 ただし、独自の DNS サーバーを使用している場合は、DNS 構成に追加の変更が必要になることがあります。 以下の [DNS の変更](#dns-changes-for-private-endpoints)に関するセクションで、プライベート エンドポイントに必要な更新について説明しています。

## <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更

プライベート エンドポイントを作成すると、ストレージ アカウントの DNS CNAME リソース レコードは、プレフィックス "*privatelink*" を持つサブドメイン内のエイリアスに更新されます。 既定では、"*privatelink*" サブドメインに対応する[プライベート DNS ゾーン](../../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントを持つ VNet の外部からストレージ エンドポイント URL を解決すると、ストレージ サービスのパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、ストレージ エンドポイント URL はプライベート エンドポイントの IP アドレスに解決されます。

上の図の例のように、プライベート エンドポイントをホストしている VNet の外部から解決されると、ストレージ アカウント "StorageAccountA" の DNS リソース レコードは次のようになります。

| 名前                                                  | 種類  | 値                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<ストレージ サービスのパブリック エンドポイント\>                   |
| \<ストレージ サービスのパブリック エンドポイント\>                   | A     | \<ストレージ サービスのパブリック IP アドレス\>                 |

既に説明したように、ストレージ ファイアウォールを使用して、VNet の外部のクライアントによるパブリック エンドポイント経由のアクセスを拒否または制御することができます。

StorageAccountA の DNS リソース レコードは、プライベート エンドポイントをホストしている VNet 内のクライアントによって解決されると、次のようになります。

| 名前                                                  | 種類  | 値                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

この方法を使用すると、プライベート エンドポイントをホストしている VNet 上のクライアントと、VNet の外部のクライアントから**同じ接続文字列を使用して**ストレージ アカウントにアクセスできます。

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントで、ストレージ アカウント エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して "*StorageAccountA.privatelink.blob.core.windows.net*" の A レコードを構成する必要があります。

> [!TIP]
> カスタムまたはオンプレミスの DNS サーバーを使用している場合は、"privatelink" サブドメインのストレージ アカウント名をプライベート エンドポイントの IP アドレスに解決するように DNS サーバーを構成する必要があります。 これを行うには、VNet のプライベート DNS ゾーンに "privatelink" サブドメインを委任するか、DNS サーバーで DNS ゾーンを構成し、DNS A レコードを追加します。

ストレージ サービスのプライベート エンドポイントに推奨される DNS ゾーン名は次のとおりです。

| ストレージ サービス        | ゾーン名                            |
| :--------------------- | :----------------------------------- |
| Blob service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| File service           | `privatelink.file.core.windows.net`  |
| Queue サービス          | `privatelink.queue.core.windows.net` |
| Table service          | `privatelink.table.core.windows.net` |
| 静的な Web サイト        | `privatelink.web.core.windows.net`   |

#### <a name="resources"></a>リソース

プライベート エンドポイントをサポートするように独自の DNS サーバーを構成する方法の詳細については、次の記事を参照してください。

- [Azure 仮想ネットワーク内のリソースの名前解決](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [プライベート エンドポイントの DNS 構成](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="known-issues"></a>既知の問題

### <a name="copy-blob-support"></a>Copy Blob のサポート

プレビュー期間中、ソース ストレージ アカウントがファイアウォールによって保護されている場合、プライベート エンドポイント経由でアクセスされるストレージ アカウントに対して発行される [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) コマンドはサポートされません。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>プライベート エンドポイントがある VNet 内のクライアントに対するストレージ アクセスの制約

既存のプライベート エンドポイントを持つ VNet 内のクライアントには、プライベート エンドポイントを持つ他のストレージ アカウントにアクセスするときに制約があります。 たとえば、VNet N1 に、ストレージ アカウント A1 のプライベート エンドポイント (たとえば、BLOB サービス用) があるとします。 ストレージ アカウント A2 が VNet N2 に BLOB サービス用のプライベート エンドポイントを持っている場合は、VNet N1 のクライアントも、プライベート エンドポイントを使用してアカウント A2 の BLOB サービスにアクセスする必要があります。 ストレージ アカウント A2 が BLOB サービス用のプライベート エンドポイントを持っていない場合、VNet N1 のクライアントは、プライベート エンドポイントを使用せずに BLOB サービスにアクセスできます。

この制約は、アカウント A2 がプライベート エンドポイントを作成するときに行われる DNS 変更の結果です。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>プライベート エンドポイントがあるサブネットのネットワーク セキュリティ グループ規則

現時点では、プライベート エンドポイントを持つサブネットに対して[ネットワーク セキュリティ グループ](../../virtual-network/security-overview.md) (NSG) 規則を構成することはできません。 この問題の限定的な回避策として、ソース サブネットでプライベート エンドポイントのアクセス規則を実装できます。ただし、この方法では、管理オーバーヘッドが高くなる可能性があります。
