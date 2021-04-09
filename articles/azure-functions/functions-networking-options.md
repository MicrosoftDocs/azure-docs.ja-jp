---
title: Azure Functions のネットワーク オプション
description: Azure Functions で利用可能なすべてのネットワーク オプションの概要。
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: f826c947b1e47c1c996a8e9102492e85adafa326
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215155"
---
# <a name="azure-functions-networking-options"></a>Azure Functions のネットワーク オプション

この記事では、Azure Functions のホスティング オプションで利用できるネットワーク機能について説明します。 以下に示すネットワーク オプションではすべて、インターネットのルーティング可能アドレスを使用せずにリソースにアクセスする機能、または関数アプリへのインターネット アクセスを制限する機能が提供されます。

ホスティング モデルには、さまざまなレベルのネットワーク分離機能があります。 正しいものを選択することで、ネットワーク分離の要件を満たすことができます。

関数アプリは、いくつかの方法でホストできます。

* さまざまなレベルの仮想ネットワーク接続性とスケーリングのオプションを備えた、マルチテナント インフラストラクチャ上で実行されるプラン オプションから選択できます。
    * [従量課金プラン](consumption-plan.md)。負荷に応じて動的なスケーリングが行われ、最小限のネットワークの分離オプションが提供されます。
    * [Premium プラン](functions-premium-plan.md)。やはり動的なスケーリングが行われますが、より包括的なネットワークの分離が提供されます。
    * [App Service プラン](dedicated-plan.md)。固定されたスケールで動作し、Premium プランと同様のネットワークの分離が提供されます。
* [App Service Environment](../app-service/environment/intro.md) で関数を実行できます。 この方法では、関数を仮想ネットワークにデプロイし、完全なネットワーク制御と分離を提供します。

## <a name="matrix-of-networking-features"></a>ネットワーク機能のマトリックス

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>受信アクセス制限

アクセス制限を使用すると、アプリへのアクセスを許可または拒否される IP アドレスの優先順位付きリストを定義できます。 この一覧には、IPv4 と IPv6 のアドレス、または[サービス エンドポイント](#use-service-endpoints)を使用する特定の仮想ネットワーク サブネットを含めることができます。 1 つ以上のエントリがある場合、リストの最後にあるものは暗黙的に "すべて拒否" になります。 IP 制限は、すべての関数ホスティング オプションで有効です。

アクセス制限は、[Premium](functions-premium-plan.md)、[従量課金](consumption-plan.md)、[App Service](dedicated-plan.md) で利用できます。

> [!NOTE]
> ネットワーク制限が適用されると、仮想ネットワーク内からか、または Azure portal へのアクセスに使用しているコンピューターの IP アドレスを [信頼された宛先のリスト] に入れている場合にのみ、デプロイを行うことができます。 ただし、ポータルを使用して関数を管理することもできます。

詳細については、「[Azure App Service の静的なアクセス制限](../app-service/app-service-ip-restrictions.md)」を参照してください。

### <a name="use-service-endpoints"></a>サービス エンドポイントの使用

サービス エンドポイントを使用することで、選択した Azure 仮想ネットワーク サブネットへのアクセスを制限できます。 特定のサブネットへのアクセスを制限するには、種類が **仮想ネットワーク** である制限規則を作成します。 その後、アクセスを許可または拒否するサブスクリプション、仮想ネットワーク、およびサブネットを選択できます。 

選択したサブネットのサービス エンドポイントが Microsoft.Web でまだ有効になっていない場合は、自動的に有効になります。ただし、 **[見つからない Microsoft.Web サービス エンドポイントを無視する]** チェック ボックスをオンにしていない場合に限ります。 サービス エンドポイントをアプリで有効にしてサブネットでは有効にしないというシナリオは、主としてサブネット上でそれらを有効にするためのアクセス許可があるかどうかに依存します。 

サブネット上でサービス エンドポイントを有効にするために他のユーザーが必要な場合は、 **[見つからない Microsoft.Web サービス エンドポイントを無視する]** チェック ボックスをオンにします。 アプリは、サービス エンドポイントが後からサブネット上で有効にされることを想定して構成されます。 

![種類として仮想ネットワークが選択された [IP 制限の追加] ウィンドウのスクリーンショット。](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

App Service Environment で実行されているアプリへのアクセスを制限するために、サービス エンドポイントを使うことはできません。 アプリが App Service Environment 内にあるときは、IP アクセス規則を適用することでアプリへのアクセスを制御できます。 

サービス エンドポイントを設定する方法については、[Azure Functions のプライベート サイト アクセスの設定](functions-create-private-site-access.md)に関するページ参照してください。

## <a name="private-endpoint-connections"></a>プライベート エンドポイント接続

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

ストレージやサービス バスなどのプライベート エンドポイント接続を持つその他のサービスを呼び出すには、必ず[プライベート エンドポイントへの送信呼び出し](#private-endpoints)を行うようにアプリを構成してください。

## <a name="virtual-network-integration"></a>仮想ネットワークの統合

仮想ネットワーク統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできます。
Azure Functions では、2 種類の仮想ネットワーク統合がサポートされています。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions の仮想ネットワーク統合では、App Service Web アプリと共有インフラストラクチャを使用します。 2 種類の仮想ネットワーク統合の詳細については、次を参照してください。

* [リージョンでの仮想ネットワーク統合](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [ゲートウェイが必要な仮想ネットワーク統合](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

仮想ネットワーク統合の設定方法については、[関数アプリを Azure 仮想ネットワークに統合する](functions-create-vnet.md)方法に関するページを参照してください。

## <a name="regional-virtual-network-integration"></a>リージョンでの仮想ネットワーク統合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>サービス エンドポイントのセキュリティで保護されたリソースに接続する

より高度なセキュリティを実現するために、サービス エンドポイントを使用して、仮想ネットワークに対する Azure サービス数を制限することができます。 次に、関数アプリをその仮想ネットワークと統合して、リソースにアクセスする必要があります。 この構成は、仮想ネットワークの統合をサポートするすべての計画でサポートされています。

詳細については、「[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)」を参照してください。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>お使いのストレージ アカウントを仮想ネットワークに制限する 

関数アプリを作成するときは、BLOB、Queue、および Table Storage をサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 このストレージ アカウントは、サービス エンドポイントまたはプライベート エンドポイントで保護されているものに置き換えることができます。 この機能は現在、Standard および Premium が含まれている仮想ネットワークでサポートされているすべての SKU に対して機能します。ただし、仮想ネットワークが Premium SKU に対してのみ使用可能なフレックス スタンプは除きます。 プライベート ネットワークに制限されたストレージ アカウントを使用して関数を設定するには、次のようにします。

1. サービス エンドポイントが有効になっていないストレージ アカウントを使用して関数を作成します。
1. ご使用の仮想ネットワークに接続するように関数を構成します。
1. 別のストレージ アカウントを作成または構成します。  これがサービス エンドポイントで保護され、関数に接続されるストレージ アカウントになります。
1. セキュリティで保護されたストレージ アカウントに[ファイル共有を作成](../storage/files/storage-how-to-create-file-share.md#create-file-share)します。
1. ストレージ アカウントのサービス エンドポイントまたはプライベート エンドポイントを有効にします。  
    * プライベート エンドポイント接続を使用する場合、ストレージ アカウントには、`file` と `blob` のサブリソース用のプライベート エンドポイントが必要です。  Durable Functions のような特定の機能を使用する場合は、プライベート エンドポイント接続を介して `queue` と `table` にアクセスできる必要もあります。
    * サービス エンドポイントを使用する場合は、ストレージ アカウントに対して関数アプリ専用のサブネットを有効にします。
1. 関数アプリのストレージ アカウントから、セキュリティで保護されたストレージ アカウントとファイル共有に、ファイルと BLOB の内容をコピーします。
1. このストレージ アカウントの接続文字列をコピーします。
1. 関数アプリの **[構成]** の下の **[アプリケーションの設定]** を次のように更新します。
    - `AzureWebJobsStorage` をセキュリティで保護されたストレージ アカウントの接続文字列にします。
    - `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` をセキュリティで保護されたストレージ アカウントの接続文字列にします。
    - `WEBSITE_CONTENTSHARE` をセキュリティで保護されたストレージ アカウントで作成されたファイル共有の名前にします。
    - 名前が `WEBSITE_CONTENTOVERVNET` で値が `1` の新しい設定を作成します。
    - ストレージ アカウントでプライベート エンドポイント接続が使用されている場合は、次の設定を確認または追加します。
        - `WEBSITE_VNET_ROUTE_ALL` (値 `1`)
        - `WEBSITE_DNS_SERVER` (値 `168.63.129.16`) 
1. アプリケーションの設定を保存します。  

関数アプリが再起動され、セキュリティで保護されたストレージ アカウントに接続されるようになります。

## <a name="use-key-vault-references"></a>Key Vault 参照を使用する

Azure Key Vault 参照を使用すると、コードの変更を必要とせずに、Azure Functions アプリケーションで Azure Key Vault のシークレットを使用することができます。 Azure Key Vault は、アクセス ポリシーと監査履歴を完全制御する、一元化されたシークレット管理を提供するサービスです。

現在、お使いのキー コンテナーがサービス エンドポイントでセキュリティ保護されている場合、[Key Vault 参照](../app-service/app-service-key-vault-references.md)は機能しません。 仮想ネットワーク統合を使用してキー コンテナーに接続するには、アプリケーション コードで Key Vault を呼び出す必要があります。

## <a name="virtual-network-triggers-non-http"></a>仮想ネットワーク トリガー (非 HTTP)

現時点では、次の 2 つの方法のいずれかで、仮想ネットワーク内から非 HTTP トリガー関数を使用できます。

+ Premium プランで関数アプリを実行し、仮想ネットワーク トリガーのサポートを有効にする。
+ App Service プランまたは App Service Environment で関数アプリを実行する。

### <a name="premium-plan-with-virtual-network-triggers"></a>仮想ネットワーク トリガーを使用した Premium プラン

Premium プランを実行する場合は、仮想ネットワーク内で実行されているサービスに非 HTTP トリガー関数を接続できます。 これを行うには、関数アプリの仮想ネットワーク トリガーのサポートを有効にする必要があります。 **[Runtime Scale Monitoring]\(ランタイム スケールの監視\)** の設定は、[Azure portal](https://portal.azure.com) の **[構成]**  >  **[関数のランタイム設定]** にあります。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

次の Azure CLI コマンドを使用して、仮想ネットワーク トリガーを有効にすることもできます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> 仮想ネットワーク トリガーを有効にすると、アプリケーションのパフォーマンスに影響することがあります。これは、App Service プランのインスタンスがトリガーを監視して、スケーリングのタイミングを判断する必要があるためです。 この影響は非常に小さい場合がほとんどです。

仮想ネットワーク トリガーは、バージョン 2.x 以降の Functions ランタイムでサポートされています。 次の非 HTTP トリガーの種類がサポートされています。

| 拡張機能 | 最小バージョン |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 以降 |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 以降|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 以降|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 以降|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 以降|

> [!IMPORTANT]
> 仮想ネットワーク トリガーのサポートを有効にすると、上記の表に示されたトリガーの種類のみが、アプリケーションで動的にスケーリングされます。 表に示されていないトリガーも使用できますが、事前ウォーミングされたインスタンス数を超えてスケーリングされることはありません。 トリガーの全一覧については、[トリガーとバインド](./functions-triggers-bindings.md#supported-bindings)に関する記事を参照してください。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>仮想ネットワーク トリガーを使用した App Service プランと App Service Environment

関数アプリを App Service プランまたは App Service Environment のいずれかで実行する場合は、非 HTTP トリガー関数を使用できます。 関数が正しくトリガーされるようにするには、トリガー接続で定義されているリソースにアクセスできる仮想ネットワークに接続する必要があります。

たとえば、仮想ネットワークからのみトラフィックを受け入れるように Azure Cosmos DB を構成するとします。 この場合、その仮想ネットワークとの仮想ネットワーク統合を提供する App Service プランで関数アプリをデプロイする必要があります。 統合により、該当の Azure Cosmos DB リソースによって関数がトリガーされるようになります。

## <a name="hybrid-connections"></a>ハイブリッド接続

[ハイブリッド接続](../azure-relay/relay-hybrid-connections-protocol.md)は、他のネットワークのアプリケーション リソースにアクセスするために使用できる Azure Relay の機能です。 アプリからアプリケーション エンドポイントにアクセスできます。 アプリケーションにアクセスするために使用することはできません。 ハイブリッド接続は、Windows で実行されている、従量課金プラン以外のすべての関数に使用できます。

Azure Functions で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 つまり、TCP リッスン ポートにアクセスしている限り、任意のオペレーティング システムの任意のアプリケーションがハイブリッド接続のエンドポイントになることができます。 ハイブリッド接続機能では、アプリケーション プロトコルやアクセス先は認識されません。 ネットワーク アクセスを提供するだけです。

詳細については、[ハイブリッド接続に関する App Service ドキュメント](../app-service/app-service-hybrid-connections.md)を参照してください。 これらの同じ構成手順を Azure Functions に使用できます。

>[!IMPORTANT]
> ハイブリッド接続は、Windows プランでのみサポートされています。 Linux はサポートされていません。

## <a name="outbound-ip-restrictions"></a>送信 IP の制限

送信 IP の制限は、Premium プラン、App Service プラン、App Service Environment で利用できます。 App Service Environment が展開されている仮想ネットワークの送信制限を構成することができます。

Premium プランまたは App Service プランの関数アプリを仮想ネットワークと統合した場合でも、アプリは既定でインターネットへの送信呼び出しを行うことができます。 アプリケーション設定 `WEBSITE_VNET_ROUTE_ALL=1` を追加することで、ネットワーク セキュリティ グループ ルールをトラフィックの制限に使用できる仮想ネットワークに、送信トラフィックがすべて送信されます。

## <a name="automation"></a>オートメーション
次の API では、リージョンでの仮想ネットワーク統合をプログラミングで管理できます。

+ **Azure CLI**:[`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) コマンドを使用し、リージョンでの仮想ネットワーク統合を追加、一覧表示、または削除します。  
+ **ARM テンプレート**:リージョンでの仮想ネットワーク統合は、Azure Resource Manager テンプレートを使用することで有効にできます。 完全な例については、[こちらの関数クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/) ページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ

ネットワークと Azure Functions の詳細については、以下を参照してください。

* [仮想ネットワーク統合の概要に関するチュートリアル](./functions-create-vnet.md)
* [関数のネットワークに関する FAQ](./functions-networking-faq.md)
* [仮想ネットワーク統合と App Service/Functions に関する詳細情報](../app-service/web-sites-integrate-with-vnet.md)
* [Azure の仮想ネットワークに関する詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
* [ハイブリッド接続を使用して、ファイアウォールを変更せずに個々のオンプレミス リソースに接続する](../app-service/app-service-hybrid-connections.md)
