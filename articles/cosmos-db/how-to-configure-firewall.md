---
title: Azure Cosmos DB アカウントに IP ファイアウォールを構成する
description: Azure Cosmos DB データベース アカウント上でファイアウォールをサポートするために IP アクセス制御ポリシーを構成する方法について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 26f2131fd62ddc83c2a6d93c4cff557402a88463
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281116"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Azure Cosmos DB で IP ファイアウォールを構成する

ご利用の Azure Cosmos DB アカウントに格納されたデータは、IP ファイアウォールを使用することによりセキュリティで保護することができます。 Azure Cosmos DB では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure Cosmos DB アカウント上で IP ファイアウォールを設定するには、次の方法のいずれかを使用できます。

* Azure portal から
* Azure Resource Manager テンプレートを使用した宣言による
* Azure CLI または Azure PowerShell を介してプログラムで、**ipRangeFilter** プロパティを更新する。

## <a id="configure-ip-policy"></a> Azure portal を使用して IP ファイアウォールを構成する

Azure portal で IP アクセス制御ポリシーを構成するには、Azure Cosmos DB アカウント ページに移動し、ナビゲーション メニューで **[ファイアウォールと仮想ネットワーク]** を選択します。 **[許可するアクセス元]** の値を **[選択されたネットワーク]** に変更し、**[保存]** を選択します。 

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
|米国政府|52.244.48.71|
|その他のすべてのリージョン|104.42.195.92、40.76.54.131、52.176.6.30、52.169.50.45、52.187.184.26|

Azure portal へのアクセスを有効にするには、次のスクリーン ショットに示すように、**[Azure portal からのアクセスを許可する]** を選択します。 

![Azure Portal へのアクセスを有効にする方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>世界中の Azure データ センターまたは Azure 内の他のソースからの要求を許可する

Azure Stream Analytics や Azure Functions などの静的 IP を提供しないサービスから Azure Cosmos DB アカウントにアクセスする場合も、IP ファイアウォールを使用してアクセスを制限できます。 このようなサービスから Azure Cosmos DB アカウントにアクセスできるようにするには、許可された IP アドレスのリストに IP アドレス 0.0.0.0 を追加します。 0.0.0.0 のアドレスにより、Azure データセンターの IP 範囲からご利用の Azure Cosmos DB アカウントへの要求が制限されます。 この設定では、その他の IP 範囲からご利用の Azure Cosmos DB アカウントへのアクセスは許可されません。

> [!NOTE]
> このオプションでは、Azure にデプロイされた他のお客様のサブスクリプションからの要求を含む、Azure からのすべての要求を許可するようにファイアウォールが構成されます。 このオプションで許可される IP のリストは非常に範囲が広くなるので、ファイアウォール ポリシーの有効性が制限されます。 このオプションは、ご利用の要求が静的 IP または仮想ネットワーク内のサブネットから発生しない場合にのみ使用してください。 このオプションを選択すると Azure portal からのアクセスは自動的に許可されるようになります。これは、Azure portal が Azure にデプロイされているからです。

Azure portal へのアクセスを有効にするには、次のスクリーン ショットに示すように、**[パブリック Azure データセンター内からの接続を受け入れる]** を選択します。 

![Azure Portal で [ファイアウォール] ページを開く方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>現在ご利用の IP からの要求

開発が簡単になるように、Azure portal ではクライアント マシンの IP を識別して許可リストに追加することができます。 マシンを実行しているアプリから Azure Cosmos DB アカウントにアクセスすることができます。 

ポータルでは自動的にクライアントの IP アドレスが検出されます。 ご利用のマシンのクライアント IP アドレスである場合や、ご利用のネットワーク ゲートウェイの IP アドレスである場合があります。 運用環境にご利用のワークロードを移行する前に、この IP アドレスを必ず削除してください。 

現在の IP を IP の一覧に追加するには、**[現在の IP を追加する]** を選択します。 次に、**[保存]** を選択します。

![現在の IP のファイアウォール設定を構成する方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>クラウド サービスからの要求

Azure では、Azure Cosmos DB を使用して中間層のサービス ロジックをホストする手段としてクラウド サービスがよく利用されます。 クラウド サービスからご利用の Azure Cosmos DB アカウントにアクセスできるようにするには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、その Azure Cosmos DB アカウントに関連付けられている IP アドレスの許可リストにクラウド サービスのパブリック IP アドレスを追加する必要があります。 これにより、クラウド サービスのすべてのロール インスタンスからその Azure Cosmos DB アカウントに確実にアクセスできるようになります。 

クラウド サービスの IP アドレスは Azure Portal で確認できます。次のスクリーンショットをご覧ください。

![Azure Portal に表示されるクラウド サービスのパブリック IP アドレスを示すスクリーンショット](./media/how-to-configure-firewall/public-ip-addresses.png)

(複数の) ロール インスタンスを追加してご利用のクラウド サービスをスケールアウトした場合、それらのインスタンスは、同じクラウド サービスに属すことになるので、Azure Cosmos DB アカウントへのアクセス権が自動的に割り当てられます。

### <a name="requests-from-virtual-machines"></a>仮想マシンからの要求

Azure Cosmos DB を使用する中間層サービスのホスティングには、[仮想マシン](https://azure.microsoft.com/services/virtual-machines/)または[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使用することもできます。 仮想マシンからのアクセスを許可するようにご利用の Cosmos DB アカウントを構成するには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、仮想マシンや仮想マシン スケール セットのパブリック IP アドレスを、ご利用の Azure Cosmos DB アカウントで許可された IP アドレスの 1 つとして構成する必要があります。 

仮想マシンの IP アドレスは Azure portal で取得できます。次のスクリーンショットを参照してください。

![Azure Portal に表示される仮想マシンのパブリック IP アドレスを示すスクリーンショット](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

このグループに仮想マシン インスタンスを追加した場合、それらのインスタンスにも自動的にご利用の Azure Cosmos DB アカウントへのアクセス権が割り当てられます。

### <a name="requests-from-the-internet"></a>インターネットからの要求

インターネット上のコンピューターからご利用の Azure Cosmos DB アカウントにアクセスするときは、そのコンピューターのクライアント IP アドレスまたは IP アドレス範囲を、ご利用のアカウントの IP アドレスの許可リストに追加する必要があります。

## <a id="configure-ip-firewall-arm"></a>Resource Manager テンプレートを使用して IP ファイアウォールを構成する

ご利用の Azure Cosmos DB アカウントへのアクセス制御を構成するには、Resource Manager テンプレートで、許可される IP 範囲のリストを使用して **ipRangeFilter** 属性を指定します。 たとえば、次の JSON コードをご利用のテンプレートに追加します。

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
       "databaseAccountOfferType": "Standard",
       "name": "[parameters('databaseAccountName')]",
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>Azure CLI を使用して IP アクセス制御ポリシーを構成する

次のコマンドでは、IP アクセス制御を使用して Azure Cosmos DB アカウントを作成する方法が示されています。 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

既存のアカウント用のファイアウォール設定を更新するには、次のコマンドを実行します。

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>IP アクセス制御ポリシーに関する問題のトラブルシューティング

次のオプションを使用して IP アクセス制御ポリシーに関する問題のトラブルシューティングを行うことができます。 

### <a name="azure-portal"></a>Azure ポータル 
ご利用の Azure Cosmos DB アカウントの IP アクセス制御ポリシーを有効にすることにより、IP アドレス範囲の許可リストに入っていないマシンからご利用のアカウントへの要求はすべてブロックされます。 コンテナーの参照やドキュメントのクエリなどのポータルのデータ プレーン操作を有効にするには、ポータルの **[ファイアウォール]** ウィンドウを使用して Azure portal へのアクセスを明示的に許可する必要があります。

### <a name="sdks"></a>SDK 
許可リストに入っていないマシンから SDK を使用して Azure Cosmos DB リソースにアクセスすると、**許可されていません 403** という一般的な応答が返され、詳しい情報は示されません。 ご利用の Azure Cosmos DB アカウントに対する IP 許可リストを確認し、そのアカウントに正しいポリシー構成が確実に適用されるようにします。 

### <a name="source-ips-in-blocked-requests"></a>ブロックされた要求内のソース IP
Azure Cosmos DB アカウントで診断ログを有効にします。 これらのログには個々の要求と応答が表示されます。 ファイアウォールに関連するメッセージは、403 リターン コードを使用してログに記録されます。 これらのメッセージをフィルター処理することにより、ブロックされた要求のソース IP を確認できます。 「[Azure Cosmos DB 診断ログ](logging.md)」を参照してください。

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Azure Cosmos DB 用のサービス エンドポイントが有効にされているサブネットからの要求
Azure Cosmos DB 用のサービス エンドポイントが有効にされている仮想ネットワーク内のサブネットからの要求では、仮想ネットワークおよびサブネットの ID が Azure Cosmos DB アカウントに送信されます。 これらの要求にはソースのパブリック IP がないため、IP フィルターでは拒否されます。 仮想ネットワーク内の特定のサブネットからのアクセスを許可するには、[ご利用の Azure Cosmos DB アカウントへの仮想ネットワークおよびサブネット ベースのアクセスを構成する方法](how-to-configure-vnet-service-endpoint.md)に関するページに概説されているアクセス制御リストを追加します。 ファイアウォール規則が適用されるまで、最大で 15 分かかる場合があります。


## <a name="next-steps"></a>次の手順

ご利用の Azure Cosmos DB アカウントに対して仮想ネットワーク サービス エンドポイントを構成するには、次の記事を参照してください。

* [ご利用の Azure Cosmos DB アカウントへの仮想ネットワークおよびサブネットのアクセス制御](vnet-service-endpoint.md)
* [Azure Cosmos DB アカウント用の仮想ネットワークとサブネット ベースのアクセスを構成する](how-to-configure-vnet-service-endpoint.md)


