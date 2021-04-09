---
title: Azure Cosmos DB アカウントに IP ファイアウォールを構成する
description: Azure Cosmos アカウントでファイアウォールをサポートするために IP アクセス制御ポリシーを構成する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: a7796b70d4d32e7023fbc88086a737dd76ae7723
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122716"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Azure Cosmos DB で IP ファイアウォールを構成する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

アカウントに保存されているデータを保護するために、Azure Cosmos DB では、強固なハッシュベースのメッセージ認証コード (HMAC) を利用したシークレット ベースの認可モデルをサポートしています。 さらに、Azure Cosmos DB では、受信ファイアウォールをサポートするための IP ベースのアクセス制御に対応しています。 このモデルは、従来型データベース システムのファイアウォール規則に似ていますが、アカウントのセキュリティ水準がさらに高くなっています。 ファイアウォールを利用して、承認されているコンピューターのグループやクラウド サービスからのみアクセスできるように Azure Cosmos アカウントを構成することができます。 ただし、承認されているコンピューターのグループやサービスから Azure Cosmos データベースに格納されているデータにアクセスするためには、呼び出し側が有効な承認トークンを提示する必要がある点は変わりません。

## <a name="ip-access-control"></a><a id="ip-access-control-overview"></a>IP アクセス制御

既定では、有効な承認トークンと共に要求が送信されれば、Azure Cosmos アカウントにインターネットからアクセスすることができます。 IP ポリシー ベースのアクセス制御を構成するためには、特定の Azure Cosmos アカウントにアクセスするクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR (Classless Inter-Domain Routing) 形式でユーザーが指定する必要があります。 この構成を適用すると、この許可リストにないマシンを発信元とするすべての要求で、403 (禁止) 応答が受信されます。 IP ファイアウォールを使用している場合は、Azure portal からアカウントへのアクセスを許可することをお勧めします。 データ エクスプローラーの使用を許可するため、また、Azure portal に表示されるアカウントのメトリックを取得するために、アクセスが必要になります。 データ エクスプローラーを使用する場合は、Azure portal にアカウントへのアクセスを許可するだけでなく、ファイアウォール規則に現在の IP アドレスを追加するようファイアウォール設定を更新する必要もあります。 ファイアウォールの変更が反映されるまでに最大 15 分かかる場合があり、この期間中はファイアウォールの動作が不安定になる場合があります。

IP ベースのファイアウォールと、サブネットおよび VNET のアクセス制御を組み合わせることができます。 これらを組み合わせることで、パブリック IP を保持する任意のソースへのアクセスや、VNET 内の特定のサブネットからのアクセスを制限できます。 サブネットおよび VNET ベースのアクセス制御に関する詳細については、[仮想ネットワークから Azure Cosmos DB リソースへのアクセス](./how-to-configure-vnet-service-endpoint.md)に関するページを参照してください。

端的に言えば、Azure Cosmos アカウントにアクセスするには、常に認証トークンが必要になります。 IP ファイアウォールおよび VNET アクセス制御リスト (ACL) が設定されていない場合は、認証トークンで Azure Cosmos アカウントにアクセスできます。 IP ファイアウォールまたは VNET ACL、あるいはその両方が Azure Cosmos アカウントに設定された後は、指定したソースから送信された要求 (認証トークンを備えている) のみが、有効な応答を取得します。 

ご利用の Azure Cosmos DB アカウントに格納されたデータは、IP ファイアウォールを使用することによりセキュリティで保護することができます。 Azure Cosmos DB では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure Cosmos DB アカウント上で IP ファイアウォールを設定するには、次の方法のいずれかを使用できます。

* Azure portal から
* Azure Resource Manager テンプレートを使用した宣言による
* Azure CLI または Azure PowerShell を介してプログラムで、**ipRangeFilter** プロパティを更新する。

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Azure portal を使用して IP ファイアウォールを構成する

Azure portal で IP アクセス制御ポリシーを構成するには、Azure Cosmos DB アカウント ページに移動し、ナビゲーション メニューで **[ファイアウォールと仮想ネットワーク]** を選択します。 **[許可するアクセス元]** の値を **[選択されたネットワーク]** に変更し、 **[保存]** を選択します。

![Azure Portal で [ファイアウォール] ページを開く方法を示すスクリーンショット](./media/how-to-configure-firewall/azure-portal-firewall.png)

IP アクセス制御を有効にすると、Azure portal で IP アドレス、IP アドレスの範囲、およびスイッチを指定する機能を使用できるようになります。 スイッチを使用して、他の Azure サービスと Azure portal にアクセスすることができます。 以下のセクションでは、これらのスイッチの詳細について説明します。

> [!NOTE]
> ご利用の Azure Cosmos DB アカウントの IP アクセス制御ポリシーを有効にすると、IP アドレス範囲の許可リストに入っていないマシンからご利用の Azure Cosmos DB アカウントへの要求はすべてブロックされます。 アクセス制御の整合性を確保するために、ポータルから Azure Cosmos DB リソースを参照することもブロックされます。

### <a name="allow-requests-from-the-azure-portal"></a>Azure portal からの要求を許可する

IP アクセス制御ポリシーをプログラムで有効にする場合は、アクセスを維持するために、Azure Portal の IP アドレスを **ipRangeFilter** プロパティに追加する必要があります。 ポータルの IP アドレスは次のとおりです。

|リージョン|IP アドレス|
|------|----------|
|ドイツ|51.4.229.218|
|中国|139.217.8.252|
|US Gov|52.244.48.71|
|その他のすべてのリージョン|104.42.195.92、40.76.54.131、52.176.6.30、52.169.50.45、52.187.184.26|

次のスクリーン ショットに示すように、 **[Azure portal からのアクセスを許可する]** を選択することで、Azure portal へのアクセス要求を有効にできます。

![Azure Portal へのアクセスを有効にする方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>世界中の Azure データ センターまたは Azure 内の他のソースからの要求を許可する

Azure Stream Analytics や Azure Functions などの静的 IP を提供しないサービスから Azure Cosmos DB アカウントにアクセスする場合も、IP ファイアウォールを使用してアクセスを制限できます。 次のスクリーン ショットに示すように、 **[Accept connections from within Azure datacenters]\(Azure データセンター内からの接続を受け入れる\)** を選択することで、Azure 内の他のソースからのアクセスを有効にできます。

![Azure データセンターからの接続を受け入れる方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-azure-services.png)

このオプションを有効にすると、IP アドレス `0.0.0.0` が、許可される IP アドレスの一覧に追加されます。 `0.0.0.0` の IP アドレスにより、Azure データセンターの IP 範囲からご利用の Azure Cosmos DB アカウントへの要求が制限されます。 この設定では、その他の IP 範囲からご利用の Azure Cosmos DB アカウントへのアクセスは許可されません。

> [!NOTE]
> このオプションでは、Azure にデプロイされた他のお客様のサブスクリプションからの要求を含む、Azure からのすべての要求を許可するようにファイアウォールが構成されます。 このオプションで許可される IP のリストは非常に範囲が広くなるので、ファイアウォール ポリシーの有効性が制限されます。 このオプションは、ご利用の要求が静的 IP または仮想ネットワーク内のサブネットから発生しない場合にのみ使用してください。 このオプションを選択すると Azure portal からのアクセスは自動的に許可されるようになります。これは、Azure portal が Azure にデプロイされているからです。

### <a name="requests-from-your-current-ip"></a>現在ご利用の IP からの要求

開発が簡単になるように、Azure portal ではクライアント マシンの IP を識別して許可リストに追加することができます。 マシンを実行しているアプリから Azure Cosmos DB アカウントにアクセスすることができます。

ポータルでは自動的にクライアントの IP アドレスが検出されます。 ご利用のマシンのクライアント IP アドレスである場合や、ご利用のネットワーク ゲートウェイの IP アドレスである場合があります。 運用環境にご利用のワークロードを移行する前に、この IP アドレスを必ず削除してください。

現在の IP を IP の一覧に追加するには、 **[現在の IP を追加する]** を選択します。 次に、 **[保存]** を選択します。

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="現在の IP のファイアウォール設定を構成する方法を示すスクリーンショット":::

### <a name="requests-from-cloud-services"></a>クラウド サービスからの要求

Azure では、Azure Cosmos DB を使用して中間層のサービス ロジックをホストする手段としてクラウド サービスがよく利用されます。 クラウド サービスからご利用の Azure Cosmos DB アカウントにアクセスできるようにするには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、その Azure Cosmos DB アカウントに関連付けられている IP アドレスの許可リストにクラウド サービスのパブリック IP アドレスを追加する必要があります。 これにより、クラウド サービスのすべてのロール インスタンスからその Azure Cosmos DB アカウントに確実にアクセスできるようになります。

クラウド サービスの IP アドレスは Azure Portal で確認できます。次のスクリーンショットをご覧ください。

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Azure portal に表示されるクラウド サービスのパブリック IP アドレスを示すスクリーンショット":::

(複数の) ロール インスタンスを追加してご利用のクラウド サービスをスケールアウトした場合、それらのインスタンスは、同じクラウド サービスに属すことになるので、Azure Cosmos DB アカウントへのアクセス権が自動的に割り当てられます。

### <a name="requests-from-virtual-machines"></a>仮想マシンからの要求

Azure Cosmos DB を使用する中間層サービスのホスティングには、[仮想マシン](https://azure.microsoft.com/services/virtual-machines/)または[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)を使用することもできます。 仮想マシンからのアクセスを許可するようにご利用の Cosmos DB アカウントを構成するには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、仮想マシンや仮想マシン スケール セットのパブリック IP アドレスを、ご利用の Azure Cosmos DB アカウントに許可されている IP アドレスの 1 つとして構成する必要があります。

仮想マシンの IP アドレスは Azure portal で取得できます。次のスクリーンショットを参照してください。

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Azure portal に表示される仮想マシンのパブリック IP アドレスを示すスクリーンショット":::

このグループに仮想マシン インスタンスを追加した場合、それらのインスタンスにも自動的にご利用の Azure Cosmos DB アカウントへのアクセス権が割り当てられます。

### <a name="requests-from-the-internet"></a>インターネットからの要求

インターネット上のコンピューターからご利用の Azure Cosmos DB アカウントにアクセスするときは、そのコンピューターのクライアント IP アドレスまたは IP アドレス範囲を、ご利用のアカウントの IP アドレスの許可リストに追加する必要があります。

### <a name="add-outbound-rules-to-the-firewall"></a>ファイアウォールにアウトバウンド規則を追加する

ファイアウォール設定に追加する送信 IP 範囲の現在の一覧にアクセスするには、[Azure IP 範囲とサービス タグのダウンロード](https://www.microsoft.com/download/details.aspx?id=56519)に関するページを参照してください。

リストを自動化する方法については、「[Service Tag Discovery API を使用する (パブリック プレビュー)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)」を参照してください。

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Resource Manager テンプレートを使用して IP ファイアウォールを構成する

ご利用の Azure Cosmos DB アカウントへのアクセス制御を構成するには、Resource Manager テンプレートで、許可される IP 範囲の配列を使用して **ipRules** プロパティを指定します。 既にデプロイされている Cosmos アカウントに IP ファイアウォールを構成する場合には、配列 `locations` が現在デプロイされているものと一致していることを確認します。 配列 `locations` とそれ以外のプロパティを同時に変更することはできません。 Azure Cosmos DB 用の Azure Resource Manager テンプレートの詳細とサンプルについては、「[Azure Cosmos DB の Azure Resource Manager テンプレート](./templates-samples-sql.md)」を参照してください。

> [!IMPORTANT]
> **ipRules** プロパティは、API バージョン 2020-04-01 で導入されました。 以前のバージョンでは、コンマ区切りの IP アドレスのリストである **ipRangeFilter** プロパティが公開されていました。

次の例は、API バージョン 2020-04-01 以降で **ipRules** プロパティを公開する方法を示しています。

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

2020-04-01 より前の API バージョンでの同じ例を次に示します。

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Azure CLI を使用して IP アクセス制御ポリシーを構成する

次のコマンドでは、IP アクセス制御を使用して Azure Cosmos DB アカウントを作成する方法が示されています。

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>PowerShell を使用して IP アクセス制御ポリシーを構成する

次のスクリプトでは、IP アクセス制御を使用して Azure Cosmos DB アカウントを作成する方法が示されています。

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>IP アクセス制御ポリシーに関する問題のトラブルシューティング

次のオプションを使用して IP アクセス制御ポリシーに関する問題のトラブルシューティングを行うことができます。

### <a name="azure-portal"></a>Azure portal

ご利用の Azure Cosmos DB アカウントの IP アクセス制御ポリシーを有効にすることにより、IP アドレス範囲の許可リストに入っていないマシンからご利用のアカウントへの要求はすべてブロックされます。 コンテナーの参照やドキュメントのクエリなどのポータルのデータ プレーン操作を有効にするには、ポータルの **[ファイアウォール]** ウィンドウを使用して Azure portal へのアクセスを明示的に許可する必要があります。

### <a name="sdks"></a>SDK

許可リストに入っていないマシンから SDK を使用して Azure Cosmos DB リソースにアクセスすると、**許可されていません 403** という一般的な応答が返され、詳しい情報は示されません。 ご利用の Azure Cosmos DB アカウントに対する IP 許可リストを確認し、そのアカウントに正しいポリシー構成が確実に適用されるようにします。

### <a name="source-ips-in-blocked-requests"></a>ブロックされた要求内のソース IP

Azure Cosmos DB アカウントで診断ログを有効にします。 これらのログには個々の要求と応答が表示されます。 ファイアウォールに関連するメッセージは、403 リターン コードを使用してログに記録されます。 これらのメッセージをフィルター処理することにより、ブロックされた要求のソース IP を確認できます。 「[Azure Cosmos DB 診断ログ](./monitor-cosmos-db.md)」を参照してください。

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Azure Cosmos DB 用のサービス エンドポイントが有効にされているサブネットからの要求

Azure Cosmos DB 用のサービス エンドポイントが有効にされている仮想ネットワーク内のサブネットからの要求では、仮想ネットワークおよびサブネットの ID が Azure Cosmos DB アカウントに送信されます。 これらの要求にはソースのパブリック IP がないため、IP フィルターでは拒否されます。 仮想ネットワーク内の特定のサブネットからのアクセスを許可するには、[ご利用の Azure Cosmos DB アカウントへの仮想ネットワークおよびサブネット ベースのアクセスを構成する方法](how-to-configure-vnet-service-endpoint.md)に関するページに概説されているアクセス制御リストを追加します。 ファイアウォール規則が適用されるまでに最大 15 分かかる場合があり、この期間中はファイアウォールの動作が不安定になる場合があります。

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>許可されたアドレスのリスト内のプライベート IP アドレス

プライベート IP アドレスを含む許可されたアドレスのリストを使用すると、Azure Cosmos アカウントの作成または更新は失敗します。 リストにプライベート IP アドレスが指定されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

ご利用の Azure Cosmos DB アカウントに対して仮想ネットワーク サービス エンドポイントを構成するには、次の記事を参照してください。

* [ご利用の Azure Cosmos DB アカウントへの仮想ネットワークおよびサブネットのアクセス制御](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos DB アカウント用の仮想ネットワークとサブネット ベースのアクセスを構成する](how-to-configure-vnet-service-endpoint.md)