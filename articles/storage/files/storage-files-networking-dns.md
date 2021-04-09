---
title: Azure Files の DNS 転送の構成 | Microsoft Docs
description: Azure Files の DNS 転送を構成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94630194"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Azure Files の DNS 転送の構成
Azure Files では、ファイル共有を含むストレージ アカウントのプライベート エンドポイントを作成することができます。 プライベート エンドポイントは、多くの異なるアプリケーションで役立ちますが、プライベート ピアリングを使用する VPN または ExpressRoute 接続を使って、オンプレミス ネットワークから Azure ファイル共有に接続する場合に特に便利です。 

ストレージ アカウントへの接続がネットワーク トンネルを経由するようにするには、ストレージ アカウントの完全修飾ドメイン名 (FQDN) がプライベート エンドポイントのプライベート IP アドレスに解決される必要があります。 これを実現するには、ストレージ エンドポイント サフィックス (パブリック クラウド リージョンの`core.windows.net`) を、仮想ネットワーク内からアクセスできる Azure プライベート DNS サービスに転送する必要があります。 このガイドでは、ストレージ アカウントのプライベート エンドポイント IP アドレスに適切に解決されるように、DNS 転送を設定して構成する方法を示します。

この記事で説明されている手順を行う前に、「[Azure Files のデプロイの計画](storage-files-planning.md)」および「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」をお読みいただくよう強くお勧めします。

## <a name="overview"></a>概要
Azure Files では、Azure ファイル共有にアクセスするための次の主な 2 種類のエンドポイントが提供されます。 
- パブリック エンドポイント。パブリック IP アドレスを持ち、世界中のどこからでもアクセスできます。
- プライベート エンドポイント。仮想ネットワーク内に存在し、その仮想ネットワークのアドレス空間内からのプライベート IP アドレスを持ちます。

パブリックおよびプライベート エンドポイントは、Azure ストレージ アカウントに存在します。 ストレージ アカウントは、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。

すべてのストレージ アカウントに、完全修飾ドメイン名 (FQDN) があります。 パブリック クラウド リージョンの場合、この FQDN では `storageaccount.file.core.windows.net` パターンに従います。ここで、`storageaccount` はストレージ アカウントの名前です。 SMB を使用してワークステーションに共有をマウントするなど、この名前に対して要求を行うと、オペレーティング システムでは DNS 参照を実行し、完全修飾ドメイン名を、(SMB 要求を送信するために使用できる) IP アドレスに解決させます。

既定では、`storageaccount.file.core.windows.net` はパブリック エンドポイントの IP アドレスに解決されます。 ストレージ アカウントのパブリック エンドポイントは、他の多くのストレージ アカウントのパブリック エンドポイントをホストする Azure ストレージ クラスターでホストされます。 プライベート エンドポイントを作成すると、プライベート DNS ゾーンは、追加された仮想ネットワークにリンクされます。その場合、ストレージ アカウントのプライベート エンドポイントのプライベート IP アドレス用の A レコード エントリに `storageaccount.file.core.windows.net` をマップする CNAME レコードを使用します。 これにより、仮想ネットワーク内で `storageaccount.file.core.windows.net` FQDN を使用して、プライベート エンドポイントの IP アドレスに解決させることができます。

ここでの究極の目標は、VPN や ExpressRoute 接続などのネットワーク トンネルを使用してオンプレミスからストレージ アカウント内でホストされている Azure ファイル共有にアクセスすることであるため、Azure Files サービスに対して行われた要求を Azure プライベート DNS サービスに転送するようにオンプレミス DNS サーバーを構成する必要があります。 これを実現するには、Azure 仮想ネットワーク内でホストされている DNS サーバーに対する、`*.core.windows.net` (または米国政府、ドイツ、あるいは中国の国内クラウド用の適切なストレージ エンドポイント サフィックス) の "*条件付き転送*" を設定する必要があります。 その後、この DNS サーバーでは、Azure のプライベート DNS サービスに要求を再帰的に転送します。これにより、ストレージ アカウントの完全修飾ドメイン名が適切なプライベート IP アドレスに解決されるようになります。

Azure Files の DNS 転送を構成するには、要求を転送するために DNS サーバーをホストする仮想マシンを実行する必要があります。しかし、これは、仮想ネットワーク内でホストされているすべての Azure ファイル共有に対する 1 回限りの手順です。 また、これは、Azure Files に対する排他的な要件ではありません。オンプレミスからアクセスするプライベート エンドポイントをサポートする次のすべての Azure サービスでは、このガイドで構成する DNS 転送を利用することができます: Azure Blob storage、SQL Azure、Cosmos DB など。 

このガイドでは、Azure ストレージ エンドポイントの DNS 転送を構成する手順を示します。したがって、Azure Files だけでなく、他のすべての Azure ストレージ サービス (Azure Blob storage、Azure Table storage、Azure Queue storage など) に対する DNS 名前解決要求が、Azure のプライベート DNS サービスに転送されます。 必要に応じて、他の Azure サービスのエンドポイントをさらに追加することもできます。 オンプレミス DNS サーバーへの DNS の再転送も構成されます。これにより、仮想ネットワーク内のクラウド リソース (DFS-R サーバーなど) でオンプレミスのマシン名を解決できるようになります。 

## <a name="prerequisites"></a>前提条件
Azure Files への DNS 転送を設定する前に、次の手順を完了しておく必要があります。

- マウントする Azure ファイル共有を含むストレージ アカウント。 ストレージ アカウントと Azure ファイル共有を作成する方法については、「[Azure ファイル共有を作成する](storage-how-to-create-file-share.md)」を参照してください。
- ストレージ アカウントのプライベート エンドポイント。 Azure Files のプライベート エンドポイントを作成する方法については、「[プライベート エンドポイントを作成する](storage-files-networking-endpoints.md#create-a-private-endpoint)」を参照してください。
- [最新バージョン](/powershell/azure/install-az-ps)の Azure PowerShell モジュール。

> [!Important]  
> このガイドでは、オンプレミス環境の Windows Server 内で DNS サーバーを使用することを前提としています。 このガイドで説明されているすべての手順は、Windows DNS Server だけでなく、どの DNS サーバーでも使用できます。

## <a name="manually-configuring-dns-forwarding"></a>DNS 転送の手動構成
Azure 仮想ネットワーク内に DNS サーバーが既に配置されている場合、または組織で使用される任意の方法で独自の仮想マシンを DNS サーバーに単にデプロイする場合は、組み込みの DNS サーバーの PowerShell コマンドレットを使用して、DNS を手動で構成することができます。

オンプレミスの DNS サーバーで、`Add-DnsServerConditionalForwarderZone` を使用して条件付きフォワーダーを作成します。 この条件付きフォワーダーは、トラフィックを効果的に正しく Azure に転送するために、オンプレミスのすべての DNS サーバーにデプロイする必要があります。 `<azure-dns-server-ip>` は、必ず、実際の環境に適した IP アドレスに置き換えてください。

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Azure 仮想ネットワーク内の DNS サーバーでは、ストレージ アカウント DNS ゾーンに対する要求が Azure プライベート DNS サービスに送られるように、フォワーダーを配置する必要もあります。その場合、予約済み IP アドレスの `168.63.129.16` に置き換えられます (別の PowerShell セッション内でコマンドを実行する場合は、必ず、`$storageAccountEndpoint` を設定してください)。

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Azure Files ハイブリッド モジュールを使用する DNS 転送の構成
DNS 転送の構成を可能な限り簡単にするために、Azure Files ハイブリッド モジュールでは自動化が提供されています。 このモジュールで DNS を操作するために提供されているコマンドレットは、Azure 仮想ネットワークに DNS サーバーをデプロイし、オンプレミスの DNS サーバーを更新して転送するのに役立ちます。 

Azure Files ハイブリッド モジュールを使用したことがない場合は、まず、ワークステーションにインストールする必要があります。 [最新バージョン](https://github.com/Azure-Samples/azure-files-samples/releases)の Azure Files ハイブリッド PowerShell モジュールをダウンロードします。

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

DNS 転送ソリューションのデプロイには、2 つの手順があります。要求の転送先となる Azure サービスを定義する DNS 転送ルール セットの作成と、DNS フォワーダーの実際のデプロイです。 

次の例では、要求をストレージ アカウントに転送します。これには、Azure Files、Azure Blob storage、Azure Table storage、および Azure Queue storage への要求が含まれます。 必要に応じて、`New-AzDnsForwardingRuleSet` コマンドレットの `-AzureEndpoints` パラメーターを使用して、ルールに追加の Azure サービスの転送を追加できます。 忘れずに、`<virtual-network-resource-group>`、`<virtual-network-name>`、および `<subnet-name>` を実際の環境の適切な値に置き換えてください。

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

さらに、いくつかの追加パラメーターの指定が役立つ、あるいは必要な場合もあります。

| パラメーター名 | Type | 説明 |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | 既定では、DNS サーバーは、仮想ネットワークと同じリソース グループにデプロイされます。 これが望ましくない場合は、このパラメーターを使用して、デプロイ先の代替リソース グループを選択できます。 |
| `DnsForwarderRootName` | `string` | 既定では、Azure にデプロイされる DNS サーバーの名前は `DnsFwder-*` となります。このアスタリスクは、反復子によって設定されます。 このパラメーターでは、その名前のルート (つまり、`DnsFwder`) を変更します。 |
| `VmTemporaryPassword` | `SecureString` | 既定では、ドメインに参加する前に VM の一時的な既定のアカウントに対して、ランダム パスワードが選択されます。 ドメインに参加した後、既定のアカウントは無効になります。 |
| `DomainToJoin` | `string` | 参加する DNS VM を参加させるドメイン。 既定では、このドメインは、コマンドレットを実行するコンピューターのドメインに基づいて選択されます。 |
| `DnsForwarderRedundancyCount` | `int` | 仮想ネットワーク用にデプロイする DNS VM の数。 既定では、`New-AzDnsForwarder` によって、Azure 仮想ネットワーク内の 2 つの DNS サーバーを、可用性セットにデプロイして冗長性を確保します。 この数値は、必要に応じて変更できます。 |
| `OnPremDnsHostNames` | `HashSet<string>` | フォワーダーを作成するために手動で指定されたオンプレミスの DNS ホスト名の一覧。 このパラメーターは、DNS 名が手動で指定されたさまざまなクライアントがある場合など、すべてのオンプレミス DNS サーバーにフォワーダーを適用しない場合に役立ちます。 |
| `Credential` | `PSCredential` | DNS サーバーを更新するときに使用する資格情報。 これは、ログインに使用したユーザー アカウントに、DNS 設定を変更するアクセス許可がない場合に役立ちます。 |
| `SkipParentDomain` | `SwitchParameter` | 既定では、DNS フォワーダーは、ご利用の環境内に存在する最上位レベルのドメインに適用されます。 たとえば、`northamerica.corp.contoso.com` が `corp.contoso.com` の子ドメインである場合、`corp.contoso.com` に関連付けられている DNS サーバーに対してフォワーダーが作成されます。 このパラメーターを指定すると、`northamerica.corp.contoso.com` にフォワーダーが作成されます。 |

## <a name="confirm-dns-forwarders"></a>DNS フォワーダーを確認する
DNS フォワーダーが正しく適用されているかどうかをテストする前に、`Clear-DnsClientCache` を使用して、ローカル ワークステーションの DNS キャッシュをクリアすることをお勧めします。 ストレージ アカウントの完全修飾ドメイン名を正しく解決できるかどうかをテストするには、`Resolve-DnsName` または `nslookup` を使用します。

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

名前解決に成功した場合、解決済みの IP アドレスがストレージ アカウントの IP アドレスと一致しているはずです。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

VPN または ExpressRoute 接続を既に設定している場合は、`Test-NetConnection` を使用して、ストレージ アカウントに正しく TCP 接続できることを確認することもできます。

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>関連項目
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)
- [Azure Files ネットワーク エンドポイントの構成](storage-files-networking-endpoints.md)