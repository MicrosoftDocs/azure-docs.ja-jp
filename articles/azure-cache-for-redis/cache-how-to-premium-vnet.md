---
title: 仮想ネットワークの構成 - Premium レベルの Azure Cache for Redis インスタンス
description: Premium レベルの Azure Cache for Redis インスタンスに対する仮想ネットワーク サポートを作成および管理する方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375274"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Premium Azure Cache for Redis インスタンスに対する仮想ネットワーク サポートの構成

[Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) のデプロイにより、セキュリティと分離が強化されると共に、サブネット、アクセス制御ポリシーや、アクセスをさらに制限する他の機能も提供されます。 仮想ネットワークを使用して Azure Cache for Redis インスタンスを構成する場合、パブリックにアドレスを指定することはできないため、仮想ネットワーク内の仮想マシンとアプリケーションからしかアクセスできません。 この記事では、Premium レベルの Azure Cache for Redis インスタンスに対する仮想ネットワークのサポートを構成する方法について説明します。

> [!NOTE]
> Azure Cache for Redis では、クラシック デプロイ モデルと Azure Resource Manager 仮想ネットワークの両方がサポートされています。
> 

## <a name="set-up-virtual-network-support"></a>仮想ネットワーク サポートの設定

Virtual Network (VNet) のサポートは、キャッシュの作成中に **[New Azure Cache for Redis]\(新しい Azure Cache for Redis\)** ペインで構成します。

1. Premium レベルのキャッシュを作成するには、[Azure portal](https://portal.azure.com) にサインインし、 **[リソースの作成]** を選択します。 キャッシュは、Azure portal だけでなく、Resource Manager テンプレート、PowerShell、または Azure CLI を使用して作成することもできます。 Azure Cache for Redis インスタンスを作成する方法の詳細については、「[キャッシュの作成](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)」を参照してください。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="[リソースの作成] を示すスクリーンショット。":::
   
1. **[新規]** ページで、 **[データベース]** を選択します。 次に **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="[Azure Cache for Redis] の選択を示すスクリーンショット。":::

1. **[新規 Redis Cache]** ページで、新しい Premium レベルのキャッシュの設定を構成します。
   
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 |
   | **サブスクリプション** | ドロップダウン リストからサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 |
   | **リソース グループ** | ドロップダウン リストからリソース グループを選択するか、 **[新規作成]** を選択して新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
   | **場所** | ドロップダウン リストから場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **キャッシュの種類** |Premium レベルの機能を構成するには、ドロップダウン リストから Premium レベルのキャッシュを選択します。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |

1. **[ネットワーク]** タブを選択するか、ページの下部にある **[ネットワーク]** ボタンを選択します。

1. **[ネットワーク]** タブで、接続方法として **[仮想ネットワーク]** を選択します。 新しい仮想ネットワークを使用するには、まず [Azure portal を使用した仮想ネットワークの作成](../virtual-network/manage-virtual-network.md#create-a-virtual-network)に関するページまたは「[Azure portal を使用した仮想ネットワーク (クラシック) の作成](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)」の手順に従ってそれを作成します。 次に、 **[New Azure Cache for Redis]\(新しい Azure Cache for Redis\)** ペインに戻り、Premium レベルのキャッシュを作成して構成します。

   > [!IMPORTANT]
   > Azure Cache for Redis を Resource Manager 仮想ネットワークにデプロイする場合、キャッシュは、Azure Cache for Redis インスタンス以外のリソースを含まない専用サブネット内に存在する必要があります。 Azure Cache for Redis インスタンスを、他のリソースを含む Resource Manager 仮想ネットワークのサブネットにデプロイしようとすると、そのデプロイは失敗します。
   > 
   > 

   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtual Network** | ドロップダウン リストから仮想ネットワークを選択します。 | ご自分のキャッシュと同じサブスクリプションと場所に存在する仮想ネットワークを選択します。 |
   | **サブネット** | ドロップダウン リストからサブネットを選択します。 | サブネットのアドレスの範囲は CIDR 表記である必要があります (例: 192.168.1.0/24)。 仮想ネットワークのアドレス空間に含まれている必要があります。 |
   | **静的 IP アドレス** | (省略可能) 静的 IP アドレスを入力します。 | 静的 IP アドレスを指定しなかった場合は、IP アドレスが自動的に選択されます。 |

   > [!IMPORTANT]
   > Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Azure 仮想ネットワーク インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 Azure Cache for Redis インスタンスは、シャードごとに 2 つの IP アドレスと、ロード バランサー用に 1 つの追加 IP アドレスを使用します。 クラスター化されていないキャッシュは、1 つのシャードを持つと見なされます。
   > 

1. ページの下部にある **[次へ: 詳細]** タブを選択するか、ページの下部にある **[次へ: 詳細]** ボタンを選択します。

1. Premium レベルのキャッシュ インスタンスの **[詳細]** タブで、非 TLS ポート、クラスタリング、およびデータ永続化の設定を構成します。

1. ページの下部にある **[次へ: タグ]** タブを選択するか、ページの下部にある **[次へ: タグ]** ボタンを選択します。

1. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** タブが表示され、Azure によって構成が検証されます。

1. 緑色の **検証に成功** のメッセージが表示された後、 **[作成]** を選択します。

キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。 キャッシュが作成されたら、 **[リソース]** メニューの **[仮想ネットワーク]** を選択することで、仮想ネットワークの構成を表示できます。

![仮想ネットワーク][redis-cache-vnet-info]

仮想ネットワークの使用時に Azure Cache for Redis インスタンスに接続するには、次の例に示すように、接続文字列でキャッシュのホスト名を指定します。

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Azure Cache for Redis 仮想ネットワークに関する FAQ

次の一覧は、Azure Cache for Redis のスケーリングに関するよく寄せられる質問への回答です。

* Azure Cache for Redis と仮想ネットワークの誤った構成に関してよく見られる問題を教えてください
* [仮想ネットワークで自分のキャッシュの動作を確認するにはどうすればよいですか?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* 仮想ネットワークで自分の Azure Cache for Redis インスタンスに接続しようとすると、リモート証明書が無効であるというエラーが表示されるのはなぜですか?
* [Standard または Basic キャッシュで仮想ネットワークを使用できますか?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Azure Cache for Redis インスタンスの作成が失敗するサブネットと成功するサブネットがあるのはなぜですか?
* [サブネット アドレス空間の要件には何がありますか](#what-are-the-subnet-address-space-requirements)
* [キャッシュが仮想ネットワークでホストされている場合、すべてのキャッシュ機能が動作しますか?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Azure Cache for Redis と仮想ネットワークの誤った構成に関してよく見られる問題を教えてください

Azure Cache for Redis が仮想ネットワークでホストされている場合は、以下の表に含まれるポートが使用されます。

>[!IMPORTANT]
>以下の表に含まれるポートがブロックされている場合、キャッシュが正常に動作しない可能性があります。 仮想ネットワークで Azure Cache for Redis を使用する場合の構成の誤りに関する最も一般的な問題は、これらのポートのうち 1 つ以上がブロックされていることです。
> 

- [送信ポートの要件](#outbound-port-requirements)
- [受信ポートの要件](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>送信ポートの要件

送信ポートには 9 個の要件があります。 これらの範囲内の送信要求は、キャッシュが機能するために必要な他のサービスに送信されるか、またはノード間通信のために Redis サブネットの内部に送信されます。 geo レプリケーションの場合は、プライマリ キャッシュとレプリカ キャッシュのサブネット間の通信のための追加の送信要件が存在します。

| Port | Direction | トランスポート プロトコル | 目的 | ローカル IP | リモート IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |送信 |TCP |Azure Storage/PKI (インターネット) に対する Redis の依存関係 | (Redis サブネット) |* <sup>4</sup> |
| 443 | 送信 | TCP | Azure Key Vault と Azure Monitor に対する Redis の依存関係 | (Redis サブネット) | AzureKeyVault、AzureMonitor <sup>1</sup> |
| 53 |送信 |TCP/UDP |DNS (インターネット/仮想ネットワーク) に対する Redis の依存関係 | (Redis サブネット) | 168.63.129.16 および 169.254.169.254 <sup>2</sup> およびサブネットのカスタム DNS サーバー <sup>3</sup> |
| 8443 |送信 |TCP |Redis の内部通信 | (Redis サブネット) | (Redis サブネット) |
| 10221-10231 |送信 |TCP |Redis の内部通信 | (Redis サブネット) | (Redis サブネット) |
| 20226 |送信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |
| 13000-13999 |送信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |
| 15000-15999 |送信 |TCP |Redis および geo レプリケーションの内部通信 | (Redis サブネット) |(Redis サブネット) (geo レプリカ ピア サブネット) |
| 6379-6380 |送信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |

<sup>1</sup> Resource Manager ネットワーク セキュリティ グループ (NSG) でサービス タグ AzureKeyVault と AzureMonitor を使用できます。

<sup>2</sup> Microsoft が所有するこれらの IP アドレスは、Azure DNS を提供するホスト VM をアドレス指定するために使用されます。

<sup>3</sup> この情報は、カスタム DNS サーバーのないサブネット、またはカスタム DNS を無視する新しい Redis キャッシュには必要ありません。

<sup>4</sup> 詳細については、「[仮想ネットワーク接続に関するその他の要件](#additional-virtual-network-connectivity-requirements)」を参照してください。

#### <a name="geo-replication-peer-port-requirements"></a>geo レプリケーション ピア ポートの要件

Azure 仮想ネットワーク内のキャッシュ間で geo レプリケーションを使用している場合は、両方のキャッシュに対して、受信 "*および*" 送信方向の両方で、サブネット全体に対してポート 15000-15999 のブロックを解除します。 この構成を使用すると、将来 geo フェールオーバーが発生しても、サブネット内のすべてのレプリカ コンポーネントが相互に直接通信できます。

#### <a name="inbound-port-requirements"></a>受信ポートの要件

受信ポートの範囲には、8 個の要件があります。 これらの範囲の受信要件は、同じ仮想ネットワークでホストされている他のサービスからの受信、または Redis サブネット通信への内部の要件です。

| Port | Direction | トランスポート プロトコル | 目的 | ローカル IP | リモート IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |受信 |TCP |Redis へのクライアント通信、Azure 負荷分散 | (Redis サブネット) | (Redis サブネット)、(クライアント サブネット)、AzureLoadBalancer <sup>1</sup> |
| 8443 |受信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |
| 8500 |受信 |TCP/UDP |Azure 負荷分散 | (Redis サブネット) | AzureLoadBalancer |
| 10221-10231 |受信 |TCP |Redis クラスターへのクライアント通信、Redis の内部通信 | (Redis サブネット) |(Redis サブネット)、AzureLoadBalancer、(クライアント サブネット) |
| 13000-13999 |受信 |TCP |Redis クラスターへのクライアント通信、Azure 負荷分散 | (Redis サブネット) | (Redis サブネット)、(クライアント サブネット)、AzureLoadBalancer |
| 15000-15999 |受信 |TCP |Redis クラスター、Azure 負荷分散、geo レプリケーションへのクライアント通信 | (Redis サブネット) | (Redis サブネット)、(クライアント サブネット)、AzureLoadBalancer、(geo レプリカ ピア サブネット) |
| 16001 |受信 |TCP/UDP |Azure 負荷分散 | (Redis サブネット) | AzureLoadBalancer |
| 20226 |受信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |

<sup>1</sup> NSG 規則を作成するために、サービス タグ AzureLoadBalancer (Resource Manager の場合) または AZURE_LOADBALANCER (クラシック デプロイ モデルの場合) を使用できます。

#### <a name="additional-virtual-network-connectivity-requirements"></a>仮想ネットワーク接続に関するその他の要件

Azure Cache for Redis のネットワーク接続要件には、仮想ネットワークで最初から満たされていないものがある可能性があります。 仮想ネットワーク内で使用したときに正常に動作させるには、Azure Cache for Redis に次の項目すべてが必要になります。

* 世界各国の Azure Storage エンドポイントに対する発信ネットワーク接続 Azure Cache for Redis インスタンスと同じリージョン内にあるエンドポイントと、"*他の*" Azure リージョン内にあるストレージ エンドポイントが含まれます。 Azure Storage エンドポイントは、次の DNS ドメインで解決されます: *table.core.windows.net*、*blob.core.windows.net*、*queue.core.windows.net*、*file.core.windows.net*。
* *ocsp.digicert.com*、*crl4.digicert.com*、*ocsp.msocsp.com*、*mscrl.microsoft.com*、*crl3.digicert.com*、*cacerts.digicert.com*、*oneocsp.microsoft.com*、*crl.microsoft.com* への送信ネットワーク接続。 この接続は、TLS/SSL 機能をサポートするために必要です。
* 仮想ネットワークの DNS 構成は、前述したすべてのエンドポイントとドメインを解決できるようにする必要があります。 これらの DNS 要件を満たすには、仮想ネットワークの有効な DNS インフラストラクチャを構成し、保守します。
* 以下の DNS ドメインで解決される次の Azure Monitoring エンドポイントに対する発信ネットワーク接続: *shoebox2-black.shoebox2.metrics.nsatc.net*、*north-prod2.prod2.metrics.nsatc.net*、*azglobal-black.azglobal.metrics.nsatc.net*、*shoebox2-red.shoebox2.metrics.nsatc.net*、*east-prod2.prod2.metrics.nsatc.net*、*azglobal-red.azglobal.metrics.nsatc.net*、*shoebox3.prod.microsoftmetrics.com*、*shoebox3-red.prod.microsoftmetrics.com*、および *shoebox3-black.prod.microsoftmetrics.com*。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>仮想ネットワークで自分のキャッシュの動作を確認するにはどうすればよいですか?

>[!IMPORTANT]
>仮想ネットワークでホストされている Azure Cache for Redis インスタンスに接続する場合、キャッシュのクライアントは同じ仮想ネットワーク内か、同じ Azure リージョン内で仮想ネットワーク ピアリングが有効になっている仮想ネットワーク内に存在する必要があります。 グローバル仮想ネットワーク ピアリングは現在サポートされていません。 この要件は、すべてのテスト アプリケーションや診断 ping ツールに当てはまります。 クライアント アプリケーションがどこでホストされているかに関係なく、クライアントのネットワーク トラフィックが Azure Cache for Redis インスタンスに到達できるよう NSG または他のネットワーク層を構成する必要があります。
>

前のセクションで説明したようにポート要件を構成した後、次の手順に従って、キャッシュが動作していることを確認できます。

- すべてのキャッシュ ノードを[再起動](cache-administration.md#reboot)します。 「[受信ポートの要件](cache-how-to-premium-vnet.md#inbound-port-requirements)」と「[送信ポートの要件](cache-how-to-premium-vnet.md#outbound-port-requirements)」に記載されているように、必要なすべてのキャッシュ依存関係に到達できない場合、キャッシュは正常に再起動できません。
- キャッシュ ノードが再起動したら (Azure portal のキャッシュの状態で報告されます)、次のテストを実行できます。
  - [tcping](https://www.elifulkerson.com/projects/tcping.php) を使って、キャッシュと同じ仮想ネットワーク内にあるコンピューターから、ポート 6380 を使用してキャッシュ エンドポイントを ping します。 例:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    `tcping` ツールからポートが開いていることがレポートされる場合、キャッシュは仮想ネットワーク内のクライアントからの接続に使用できます。

  - キャッシュに接続してキャッシュのいくつかの項目を追加および取得するテスト キャッシュ クライアント (StackExchange.Redis を使ったシンプルなコンソール アプリケーションにできます) を作成することでテストする方法もあります。 キャッシュと同じ仮想ネットワーク内にある VM にサンプル クライアント アプリケーションをインストールします。 次に、それを実行して、キャッシュへの接続を確認します。


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>仮想ネットワークで自分の Azure Cache for Redis インスタンスに接続しようとすると、リモート証明書が無効であるというエラーが表示されるのはなぜですか?

仮想ネットワークで Azure Cache for Redis インスタンスに接続しようとすると、次のような証明書の検証エラーが表示されます。

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

IP アドレスでホストに接続していることが原因である可能性があります。 ホスト名を使用することをお勧めします。 つまり、次の文字列を使用してください。

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

次の接続文字列のような IP アドレスは使用しないでください。

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

DNS 名を解決できない場合、StackExchange.Redis クライアントによって指定される `sslHost` のような構成オプションが、クライアント ライブラリに含まれている場合があります。 このオプションによって、証明書の検証に使用されるホスト名をオーバーライドできます。 例:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Standard または Basic キャッシュで仮想ネットワークを使用できますか?

仮想ネットワークは、Premium レベルのキャッシュでのみ使用できます。

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Azure Cache for Redis インスタンスの作成が失敗するサブネットと成功するサブネットがあるのはなぜですか?

Azure Cache for Redis インスタンスを仮想ネットワークにデプロイする場合、キャッシュは、他のリソースの種類が含まれない専用サブネット内に配置する必要があります。 Azure Cache for Redis インスタンスを、他のリソース (Azure Application Gateway インスタンスや送信 NAT など) が含まれる Resource Manager 仮想ネットワーク サブネットにデプロイしようとすると、そのデプロイは通常失敗します。 新しい Azure Cache for Redis インスタンスを作成する前に、他の種類の既存のリソースを削除する必要があります。

また、十分な数の使用可能な IP アドレスがサブネット内にある必要があります。

### <a name="what-are-the-subnet-address-space-requirements"></a>サブネット アドレス空間の要件には何がありますか

Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure 仮想ネットワーク インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 Azure Cache for Redis インスタンスでは、クラスター シャードごとに 2 つの IP アドレスと、存在する場合追加のレプリカ用に追加 IP アドレスが使用されます。 ロード バランサー用に、追加 IP アドレスが 1 つ使用されます。 クラスター化されていないキャッシュは、1 つのシャードを持つと見なされます。

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>キャッシュが仮想ネットワークでホストされている場合、すべてのキャッシュ機能が動作しますか?

キャッシュが仮想ネットワークの一部である場合は、仮想ネットワーク内のクライアントだけがキャッシュにアクセスできます。 そのため、次のキャッシュ管理機能は現時点では動作しません。

* **Redis コンソール**:Redis コンソールはローカル ブラウザーで実行されます。これは通常、仮想ネットワークに接続されていない開発者用コンピューター上にあるため、キャッシュに接続できません。

## <a name="use-expressroute-with-azure-cache-for-redis"></a>ExpressRoute と Azure Cache for Redis の使用

お客様は、[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回線を各自の仮想ネットワーク インフラストラクチャに接続することができます。 こうすることで、オンプレミスのネットワークを Azure に拡張できます。

既定では、新たに作成した ExpressRoute 回線では、仮想ネットワークでの強制トンネリング (デフォルト ルート 0.0.0.0/0 のアドバタイズ) は実行されません。 その結果、送信インターネット接続は仮想ネットワークから直接許可されます。 クライアント アプリケーションでは、Azure Cache for Redis インスタンスを含む他の Azure エンドポイントに接続できます。

お客様の一般的な構成では、強制トンネリング (デフォルト ルートのアドバタイズ) が使用され、送信インターネット トラフィックを強制的にオンプレミスにフローさせます。 このトラフィック フローでは、送信トラフィックがオンプレミスでブロックされると、Azure Cache for Redis との接続が切断されます。そのため、Azure Cache for Redis インスタンスがその依存関係と通信できなくなります。

解決策は、Azure Cache for Redis インスタンスを含むサブネット上で 1 つ以上のユーザー定義ルート (UDR) を定義することです。 UDR は、既定のルートに優先するサブネット固有のルートを定義します。

可能な場合、次の構成を使用します。

* ExpressRoute 構成によって 0.0.0.0/0 をアドバタイズし、既定でオンプレミスの全送信トラフィックを強制的にトンネリングします。
* Azure Cache for Redis インスタンスを含むサブネットに適用される UDR では、0.0.0.0/0 と、パブリック インターネットへの TCP/IP トラフィック用に動作するルートを定義します。 たとえば、次ホップの種類を *internet* に設定します。

これらの手順を組み合わせた結果として、サブネットレベルの UDR は ExpressRoute 強制トンネリングよりも優先されるので、Azure Cache for Redis インスタンスからの送信インターネット アクセスを確保できます。

ExpressRoute を使用してオンプレミス アプリケーションから Azure Cache for Redis インスタンスに接続することは、パフォーマンス上の理由から、一般的な使用シナリオではありません。 最適なパフォーマンスを得るには、Azure Cache for Redis クライアントが Azure Cache for Redis インスタンスと同じリージョンにある必要があります。

>[!IMPORTANT]
>ExpressRoute 構成でアドバタイズされたルートよりも優先するには、UDR に定義されているルートを詳細にする *必要があります* 。 以下の例では、0.0.0.0/0 という広域なアドレス範囲を使用しているので、より具体的なアドレスの範囲を使用するルート アドバタイズによって誤ってオーバーライドされる可能性があります。

>[!WARNING]
>Azure Cache for Redis は、"*パブリック ピアリング パスからプライベート ピアリング パスにルートを正しくクロスアドバタイズしていない*" ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、大規模な Microsoft Azure の IP アドレス範囲について Microsoft からルート アドバタイズを受信します。 これらのアドレス範囲がプライベート ピアリング パスで誤ってクロスアドバタイズされている場合、Azure Cache for Redis インスタンスのサブネットからのすべての発信ネットワーク パケットは、誤って顧客のオンプレミス ネットワーク インフラストラクチャに強制的にトンネリングされます。 このネットワーク フローでは、Azure Cache for Redis が機能しません。 この問題を解決するには、パブリック ピアリング パスからプライベート ピアリング パスへのルートのクロスアドバタイズを停止します。

UDR の背景情報については、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。

ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cache for Redis の機能について

* [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
