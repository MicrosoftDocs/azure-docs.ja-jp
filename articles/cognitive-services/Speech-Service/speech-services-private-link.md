---
title: Speech Services でプライベート エンドポイントを使用する方法
titleSuffix: Azure Cognitive Services
description: Azure Private Link で提供されるプライベート エンドポイントでの Speech Services の使用方法について説明します
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: c9af0cda14261e8eab7f1ecc05c50a289d7ddfdb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99559649"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>プライベート エンドポイント経由で Speech Services を使用する

[Azure Private Link](../../private-link/private-link-overview.md) を利用すると、[プライベート エンドポイント](../../private-link/private-endpoint-overview.md)を使用して Azure のサービスに接続できます。 プライベート エンドポイントは、特定の[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)およびサブネット内でのみアクセス可能なプライベート IP アドレスです。

この記事では、Azure Cognitive Services の Speech Services で Private Link とプライベート エンドポイントを設定して使用する方法について説明します。

> [!NOTE]
> 先に進む前に、[Cognitive Services で仮想ネットワークを使用する方法](../cognitive-services-virtual-networks.md)を確認してください。

この記事では、[後でプライベート エンドポイントを削除しても引き続き Speech リソースを使用する方法](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)についても説明します。

## <a name="create-a-custom-domain-name"></a>カスタム ドメイン名を作成する

プライベート エンドポイントを使用するには、[Cognitive Services のカスタム サブドメイン名](../cognitive-services-custom-subdomains.md)が必要です。 次の指示のようにして、Speech リソース用に 1 つ作成します。

> [!WARNING]
> カスタム ドメイン名が有効になっている Speech リソースでは、Speech Services とのやり取りに別の方法が使用されます。 [プライベート エンドポイントが有効になっている](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled)シナリオと、[プライベート エンドポイントが有効になって "*いない*"](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints) シナリオの両方に対して、アプリケーションのコードを調整することが必要になる場合があります。
>
> カスタム ドメイン名を有効にした場合、この操作を[元に戻すことはできません](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)。 [リージョン名](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)に戻る唯一の方法は、新しい Speech リソースを作成することです。
>
> [Speech Studio](https://speech.microsoft.com/) を使用して作成された多数のカスタム モデルおよびプロジェクトが Speech リソースに関連付けられている場合は、運用環境で使用されているリソースを変更する前に、テスト リソースで構成を試してみることを強くお勧めします。

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Azure portal を使用してカスタム ドメイン名を作成するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/) に移動し、ご自分の Azure アカウントにサインインします。
1. 必要な Speech リソースを選択します。
1. 左側のペインの **[リソース管理]** グループで、 **[ネットワーク]** を選択します。
1. **[ファイアウォールと仮想ネットワーク]** タブで、 **[カスタム ドメイン名を生成します]** を選択します。 新しい右パネルが開き、リソースの一意のカスタム サブドメインを作成する手順が示されます。
1. **[カスタム ドメイン名の生成]** パネルで、カスタム ドメイン名を入力します。 完全なカスタム ドメインは、`https://{your custom name}.cognitiveservices.azure.com` のようになります。 
    
    作成したカスタム ドメイン名は後で変更 "_できない_" ことに注意してください。
    
    カスタム ドメイン名を入力した後、 **[保存]** を選択します。
1. 操作が終わったら、 **[リソース管理]** グループで **[Keys and Endpoint]\(キーとエンドポイント\)** を選択します。 リソースの新しいエンドポイント名が次のように始まっていることを確認します: `https://{your custom name}.cognitiveservices.azure.com`。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してカスタム ドメイン名を作成するには、コンピューターに Azure PowerShell モジュールのバージョン 5.1.0 以降を含む PowerShell バージョン 7.x 以降がインストールされていることを確認します。 これらのツールのバージョンを確認するには、次の手順のようにします。

1. PowerShell ウィンドウで、次のように入力します。

    `$PSVersionTable`

    `PSVersion` の値が 7.x 以降であることを確認します。 PowerShell をアップグレードするには、「[PowerShell のさまざまなバージョンのインストール](/powershell/scripting/install/installing-powershell)」の手順に従ってください。

1. PowerShell ウィンドウで、次のように入力します。

    `Get-Module -ListAvailable Az`

    何も表示されない場合、または Azure PowerShell モジュールのバージョンが 5.1.0 より前の場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページの手順に従ってアップグレードしてください。

先に進む前に、`Connect-AzAccount` を実行して、Azure との接続を作成します。

## <a name="verify-that-a-custom-domain-name-is-available"></a>カスタム ドメイン名が使用可能であることを確認する

使用するつもりのカスタム ドメインが使用可能かどうかを確認します。 次のコードを実行すると、Cognitive Services REST API の[ドメインの可用性の確認](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)操作を使用して、ドメインが使用可能であることが確認されます。

> [!TIP]
> 次のコードは Azure Cloud Shell では動作 "*しません*"。

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
目的の名前が使用可能な場合は、次のような応答が表示されます。
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
その名前が既に取得されている場合は、次の応答が表示されます。
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>カスタム ドメイン名を作成する

選択した Speech リソースでカスタム ドメイン名を有効にするには、[Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) コマンドレットを使用します。

> [!WARNING]
> 次のコードが正常に実行された後、Speech リソースのカスタム ドメイン名を作成します。 この名前は変更 "*できない*" ことに注意してください。

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

このセクションでは、Azure CLI の最新バージョンが必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="verify-that-the-custom-domain-name-is-available"></a>カスタム ドメイン名が使用可能であることを確認する

使用するつもりのカスタム ドメインが空いているかどうかを確認します。 Cognitive Services REST API の[ドメインが使用可能かどうかを確認する](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)方法を使用します。

次のコード ブロックをコピーし、使用したいカスタム ドメイン名を挿入して、`subdomain.json` ファイルに保存します。

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

現在のフォルダーにファイルをコピーするか、Azure Cloud Shell にアップロードして、次のコマンドを実行します。 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` は、Azure サブスクリプション ID に置き換えてください。

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
目的の名前が使用可能な場合は、次のような応答が表示されます。
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

その名前が既に取得されている場合は、次の応答が表示されます。
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>カスタム ドメイン名を有効にする

選択した Speech リソースでカスタム ドメイン名を有効にするには、[az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) コマンドを使用します。

Speech リソースが含まれている Azure サブスクリプションを選択します。 Azure アカウントにアクティブなサブスクリプションが 1 つしかない場合は、この手順を省略できます。 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` は、Azure サブスクリプション ID に置き換えてください。
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
カスタム ドメイン名を、選択したリソースに設定します。 サンプル パラメーターの値を実際の値に置き換えて、次のコマンドを実行します。

> [!WARNING]
> 以下のコマンドが正常に実行された後、Speech リソースのカスタム ドメイン名を作成します。 この名前は変更 "*できない*" ことに注意してください。

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>プライベート エンドポイントを有効にする

プライベート エンドポイント用に必要な更新が行われた仮想ネットワークにアタッチされている[プライベート DNS ゾーン](../../dns/private-dns-overview.md)を使用することをお勧めします。 プライベート DNS ゾーンは、プロビジョニング プロセスの間に既定で作成されます。 独自の DNS サーバーを使用している場合は、DNS の構成の変更が必要になることもあります。 

運用環境の Speech リソース用にプライベート エンドポイントをプロビジョニングする "*前に*"、DNS 戦略を決定します。 また、DNS の変更をテストします (特に、独自の DNS サーバーを使用する場合)。

以下の記事のいずれかを使用して、プライベート エンドポイントを作成します。 これらの記事では、プライベート エンドポイントで有効にするサンプル リソースとして Web アプリが使用されています。

- [Azure portal を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-portal.md)
- [Azure PowerShell を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-powershell.md)
- [Azure CLI を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-cli.md)

選択した記事のパラメーターの代わりに、次のパラメーターを使用します。

| 設定             | 値                                    |
|---------------------|------------------------------------------|
| リソースの種類       | **Microsoft.CognitiveServices/accounts** |
| リソース            | **\<your-speech-resource-name>**         |
| ターゲット サブリソース | **アカウント**                              |

**プライベート エンドポイントの DNS:** [Cognitive Services リソースでのプライベート エンドポイントの DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints) の一般原則を確認します。 次に、以下のセクションで説明するチェックを実行して、DNS の構成が正常に機能していることを確認します。

### <a name="resolve-dns-from-the-virtual-network"></a>仮想ネットワークから DNS を解決する

このチェックは "*必須*" です。

仮想ネットワークからカスタム DNS エントリをテストするには、次の手順のようにします。

1. プライベート エンドポイントをアタッチした仮想ネットワークにある仮想マシンにログインします。 
1. Windows コマンド プロンプトまたは Bash シェルを開いて `nslookup` を実行し、リソースのカスタム ドメイン名が正しく解決されることを確認します。

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. IP アドレスがプライベート エンドポイントの IP アドレスと一致していることを確認します。

### <a name="resolve-dns-from-other-networks"></a>他のネットワークから DNS を解決する

このチェックは、リソースの **[ネットワーク]** セクションで **[すべてのネットワーク]** オプションまたは **[Selected Networks and Private Endpoints]\(選択したネットワークとプライベート エンドポイント\)** アクセス オプションを有効にした場合にのみ実行します。 

プライベート エンドポイントのみを使用してリソースにアクセスする予定の場合は、このセクションを省略できます。

1. リソースへのアクセスが許可されているネットワークに接続されているコンピューターにログインします。
2. Windows コマンド プロンプトまたは Bash シェルを開いて `nslookup` を実行し、リソースのカスタム ドメイン名が正しく解決されることを確認します。

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> 解決された IP アドレスは、仮想ネットワーク プロキシ エンドポイントを指しています。これにより、ネットワーク トラフィックが Cognitive Services リソースのプライベート エンドポイントにディスパッチされます。 この動作は、カスタム ドメイン名が有効になっていても、プライベート エンドポイントが有効になって *いない* リソースでは異なります。 詳細については、[このセクション](#dns-configuration)を参照してください。

## <a name="adjust-existing-applications-and-solutions"></a>既存のアプリケーションとソリューションを調整する

カスタム ドメインが有効になっている Speech リソースでは、Speech Services とのやり取りに別の方法を使用します。 これは、プライベート エンドポイントの有無に関係なく、カスタム ドメインが有効になっている Speech リソースに当てはまります。 このセクションの情報は、両方のシナリオに該当します。

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>カスタム ドメイン名とプライベート エンドポイントが有効な Speech リソースを使用する

Speech リソースでカスタム ドメイン名とプライベート エンドポイントが有効になっている場合は、Speech Services とのやり取りに別の方法が使用されます。 このセクションでは、Speech Services REST API および [Speech SDK](speech-sdk.md) でこのようなリソースを使用する方法について説明します。

> [!NOTE]
> Speech リソースでプライベート エンドポイントは有効になっていないがカスタム ドメイン名は有効になっている場合も、Speech Services と対話する特別な方法があります。 この方法は、プライベート エンドポイントが有効な Speech リソースのシナリオとは異なります。 そのようなリソースがある場合は (たとえば、リソースで有効にしていたプライベート エンドポイントを削除した場合)、「[カスタム ドメイン名は有効だがプライベート エンドポイントは有効でない Speech リソースを使用する](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)」セクションを参照してください。

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>カスタム ドメイン名とプライベート エンドポイントが有効な Speech リソース: REST API での使用

このセクションでは、Speech リソースの DNS 名 (カスタム ドメイン) のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。

Speech Service には、[Speech to Text](rest-speech-to-text.md) および [Text to Speech](rest-text-to-speech.md) の REST API があります。 プライベート エンドポイントが有効なシナリオでは、次の情報を考慮します。

音声テキスト変換には、2 つの REST API があります。 各 API は、異なる目的に対応し、使用するエンドポイントが異なり、プライベート エンドポイントが有効なシナリオで使用するときは異なるアプローチを必要とします。

以下に、Speech to Text REST API を示します。
- [Speech to Text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) は、[バッチ文字起こし](batch-transcription.md)と [Custom Speech](custom-speech-overview.md) に使用されます。 v3.0 は、[v2.0 の後継](./migrate-v2-to-v3.md)です
- [Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) は、オンライン文字起こしに使用されます 

プライベート エンドポイントのシナリオでは、Speech to Text REST API for short audio と Text to Speech REST API の使用方法は同じです。 これは、この記事で後ほど説明する [Speech SDK の場合](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)と同じです。 

Speech to Text REST API v3.0 により使用されるエンドポイント セットが異なるため、プライベート エンドポイントが有効になっているシナリオには別のアプローチが必要になります。

次のサブセクションでは、両方のケースについて説明します。

##### <a name="speech-to-text-rest-api-v30"></a>Speech to Text REST API v3.0

通常、Speech リソースでは、[Speech to Text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) と通信するために、[Cognitive Services リージョン エンドポイント](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)が使用されます。 これらのリソースの名前付け形式は次のとおりです。 <p/>`{region}.api.cognitive.microsoft.com`.

要求 URL のサンプルを次に示します。

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Azure Government と Azure China のエンドポイントについては、[こちらの記事](sovereign-clouds.md)を参照してください。

Speech リソースのカスタム ドメインを有効にした後 (プライベート エンドポイントに必要)、そのリソースでは基本的な REST API エンドポイントに次の DNS 名パターンが使用されます。 <p/>`{your custom name}.cognitiveservices.azure.com`.

つまり、この例の REST API エンドポイント名は次のようになります。 <p/>`my-private-link-speech.cognitiveservices.azure.com`.

また、要求 URL の例は次のように変換する必要があります。
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
この URL には、プライベート エンドポイントがアタッチされている仮想ネットワークからアクセスできる ([正しい DNS 解決](#resolve-dns-from-the-virtual-network)が提供されている) 必要があります。

Speech リソースのカスタム ドメイン名を有効にした後は、通常、すべての要求 URL のホスト名を新しいカスタム ドメインのホスト名に置き換えます。 要求の他のすべての部分 (上の例でのパス `/speechtotext/v3.0/transcriptions` など) は同じままです。

> [!TIP]
> お客様は、リージョンのエンドポイント DNS 名のリージョン部分を使用するアプリケーションを開発する場合があります (たとえば、特定の Azure リージョンにデプロイされた Speech リソースに要求を送信するため)。
>
> Speech リソースのカスタム ドメインには、リソースがデプロイされるリージョンに関する情報は含まれて "*いません*"。 そのため、上で説明したアプリケーション ロジックは機能 "*しない*" ので、変更する必要があります。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Speech to Text REST API for short audio と Text to Speech REST API

[Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) と [Text to Speech REST API](rest-text-to-speech.md) には、次の 2 種類のエンドポイントが使用されます。
- 認証トークンを取得するために Cognitive Services REST API と通信するための [Cognitive Services リージョン エンドポイント](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)
- 他のすべての操作のための特別なエンドポイント

> [!NOTE]
> Azure Government と Azure China のエンドポイントについては、[こちらの記事](sovereign-clouds.md)を参照してください。

特別なエンドポイントの詳細と、プライベート エンドポイントが有効になっている Speech リソースに応じて URL を変換する方法の詳細については、Speech SDK での使用方法に関する[こちらのサブセクション](#construct-endpoint-url)を参照してください。 SDK について説明されているものと同じ原則が、Speech to Text REST API for short audio および Text to Speech REST API に適用されます。

前の段落で説明したサブセクションの資料を理解し、次の例をご覧ください。 この例では、Text to Speech REST API について説明します。 Speech to Text REST API for short audio の使用方法はまったく同じです。

> [!NOTE]
> プライベート エンドポイントのシナリオで Speech to Text REST API for short audio と Text to Speech REST API を使用している場合は、`Ocp-Apim-Subscription-Key` ヘッダーを介して渡されたサブスクリプション キーを使用します。 (詳細は、[Speech to Text REST API for short audio](rest-speech-to-text.md#request-headers) および [Text to Speech REST API](rest-text-to-speech.md#request-headers) を参照)
>
> 認証トークンを使用してそれを `Authorization` ヘッダーを介して特殊なエンドポイントに渡すことは、音声リソースの **[ネットワーク]** セクションで **[すべてのネットワーク]** アクセス オプションを有効にした場合に "*のみ*" 機能します。 それ以外の場合は、認証トークンを取得しようとすると、`Forbidden` または `BadRequest` エラーが発生します。

**Text to Speech REST API の使用例**

Azure リージョンのサンプルとして西ヨーロッパを、Speech リソースの DNS 名 (カスタム ドメイン) のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。 この例のカスタム ドメイン名 `my-private-link-speech.cognitiveservices.azure.com` は、西ヨーロッパ リージョンに作成された Speech リソースに属しています。

リージョンでサポートされている音声の一覧を取得するには、次の要求を実行します。

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
詳細については、[Text to Speech REST API のドキュメント](rest-text-to-speech.md)を参照してください。

プライベート エンドポイントが有効になっている Speech リソースの場合、同じ操作のエンドポイント URL を変更する必要があります。 同じ要求は次のようになります。

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Speech SDK については、「[エンドポイント URL の構築](#construct-endpoint-url)」サブセクションの詳細な説明を参照してください。

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>カスタム ドメイン名とプライベート エンドポイントが有効な Speech リソース: Speech SDK での使用方法

カスタム ドメイン名とプライベート エンドポイントが有効になっている Speech リソースで Speech SDK を使用する場合は、アプリケーション コードを確認し、おそらくは変更することが必要になります。

このセクションでは、Speech リソースの DNS 名 (カスタム ドメイン) のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。

##### <a name="construct-endpoint-url"></a>エンドポイント URL の構築

通常、SDK のシナリオ (および Speech to Text REST API for short audio と Text to Speech REST API のシナリオ) では、Speech リソースにより、異なるサービス内容ごとに専用のリージョン エンドポイントが使用されます。 これらのエンドポイントの DNS 名の形式は次のとおりです。

`{region}.{speech service offering}.speech.microsoft.com`

DNS 名の例を次に示します。

`westeurope.stt.speech.microsoft.com`

リージョン (DNS 名の最初の要素) に使用可能なすべての値は、「[Speech サービスがサポートされているリージョン](regions.md)」の一覧に示されています。 Azure Government と Azure China のエンドポイントについては、[こちらの記事](sovereign-clouds.md)を参照してください。次の表では、Speech Services オファリング (DNS 名の 2 番目の要素) に使用できる値を示します。

| DNS 名値 | Speech Services オファリング                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [カスタム コマンド](custom-commands.md)                       |
| `convai`       | [会話の文字起こし](conversation-transcription.md) |
| `s2s`          | [音声翻訳](speech-translation.md)                 |
| `stt`          | [音声テキスト変換](speech-to-text.md)                         |
| `tts`          | [テキスト読み上げ](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md)                      |

そのため、前の例 (`westeurope.stt.speech.microsoft.com`) は、西ヨーロッパの音声テキスト変換エンドポイントを表します。

プライベート エンドポイントが有効になっているエンドポイントと Speech Services の通信は、特殊なプロキシを介して行われます。 そのため、"*エンドポイント接続 URL を変更する必要があります*"。 

"標準的な" エンドポイント URL は次のようになります。 <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

プライベート エンドポイント URL は次のようになります。 <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**例 1.** アプリケーションの通信には、次の URL が使用されています (西ヨーロッパでの米国英語用の基本モデルを使用する音声認識)。

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Speech リソースのカスタム ドメイン名が `my-private-link-speech.cognitiveservices.azure.com` のときにプライベート エンドポイントが有効になっているシナリオでこれを使用するには、この URL を次のように変更する必要があります。

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

次の詳細に注目します。

- ホスト名 `westeurope.stt.speech.microsoft.com` は、カスタム ドメインのホスト名 `my-private-link-speech.cognitiveservices.azure.com` に置き換えられます。
- 元の DNS 名の 2 番目の要素 (`stt`) は URL パスの最初の要素になり、元のパスの前に置かれます。 つまり、元の URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` は `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` になります。

**例 2.** カスタム音声モデルを使用して西ヨーロッパで音声を合成するため、次の URL がアプリケーションで使用されます。
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

次の同等の URL ではプライベート エンドポイントが有効になっており、Speech リソースのカスタム ドメイン名は `my-private-link-speech.cognitiveservices.azure.com` です。

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

例 1 と同じ原則が適用されますが、この場合の主要要素は `voice` になります。

##### <a name="modifying-applications"></a>アプリケーションの変更

コードを変更するには、次の手順に従います。

1. アプリケーション エンドポイントの URL を特定します。

   - [アプリケーションのログ記録を有効にし](how-to-use-logging.md)、アプリケーションを実行してアクティビティをログに記録します。
   - ログ ファイルで `SPEECH-ConnectionUrl` を検索します。 一致する行の `value` パラメーターには、Speech Service に接続するために使用するアプリケーションの完全な URL が含まれています。

   例:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   そのため、この例でアプリケーションによって使用される URL は次のようになります。

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. エンドポイントの完全な URL を使用して、`SpeechConfig` インスタンスを作成します。

   1. 前の「[エンドポイント URL の構築](#construct-endpoint-url)」セクションの説明に従って、先ほど特定したエンドポイントを変更します。

   1. `SpeechConfig` のインスタンスの作成方法を変更します。 通常、アプリケーションでは次のようなものが使用されています。
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      これは、前のセクションで説明したホスト名と URL の変更のため、プライベート エンドポイントが有効になっている Speech リソースに対しては機能しません。 プライベート エンドポイントが有効になっているリソースのキーを使用して、既存のアプリケーションを変更せずに実行しようとすると、認証エラー (401) が発生します。

      機能させるには、`SpeechConfig` クラスのインスタンス化方法を変更し、"エンドポイントから" および "エンドポイントで" の初期化を使用します。 次の 2 つの変数が定義されているとします。
      - `subscriptionKey` には、プライベート エンドポイントが有効な Speech リソースのキーが含まれます。
      - `endPoint` には、完全な "*変更された*" エンドポイント URL が含まれます (対応するプログラミング言語で必要な型を使用)。 この例では、この変数には次の内容が含まれている必要があります
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      `SpeechConfig` インスタンスを作成します。
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> エンドポイント URI で指定されたクエリ パラメーターは、他の API によって設定されている場合でも変更されません。 たとえば、認識言語がクエリ パラメーター `language=en-US` として URI で定義されていて、対応するプロパティにより `ru-RU` にも設定されている場合は、URI での言語設定が使用されます。 有効な言語は `en-US` になります。
>
> エンドポイント URI で設定されたパラメーターが常に優先されます。 他の API でオーバーライドできるのは、エンドポイント URI で指定されていないパラメーターのみです。

この変更を行った後、アプリケーションで、プライベート エンドポイントが有効になっている Speech リソースを操作する必要があります。 プライベート エンドポイントのシナリオのサポートをいっそうシームレスにするための作業が行われています。

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>カスタム ドメイン名は有効だがプライベート エンドポイントは有効でない Speech リソースを使用する

この記事では、Speech リソースのカスタム ドメインを有効にしたら "*元に戻すことはできない*" と繰り返し指摘してきました。 そのようなリソースで使用される Speech Services との通信方法は、[リージョンのエンドポイント名](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)を使用しているものとは異なります。

このセクションでは、カスタム ドメイン名は有効になっているがプライベート エンドポイントは有効になって "*いない*" Speech リソースを、Speech Services REST API および [Speech SDK](speech-sdk.md) で使用する方法について説明します。 これは、プライベート エンドポイント シナリオで 1 回使用されたが、そのプライベート エンドポイントが削除されたリソースである場合があります。

#### <a name="dns-configuration"></a>DNS の構成

プライベート エンドポイントが有効になっている Speech リソースのカスタム ドメイン DNS 名が、[パブリック ネットワークから解決](#resolve-dns-from-other-networks)される方法を思い出してください。 この場合、解決された IP アドレスは、仮想ネットワークのプロキシ エンドポイントを指しています。 そのエンドポイントは、プライベート エンドポイントが有効になっている Cognitive Services リソースにネットワーク トラフィックをディスパッチするために使用されます。

ただし、"*すべて*" のリソース プライベート エンドポイントが削除されるときに (またはカスタム ドメイン名を有効にした直後に)、Speech リソースの CNAME レコードは再プロビジョニングされます。 そして、対応する [Cognitive Services リージョン エンドポイント](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)の IP アドレスを指すようになります。

したがって `nslookup` のコマンドの出力は次のようになります。
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
[このセクション](#resolve-dns-from-other-networks)の出力と比較します。

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>カスタム ドメイン名は有効だがプライベート エンドポイントは有効でない Speech リソースREST API での使用

##### <a name="speech-to-text-rest-api-v30"></a>Speech to Text REST API v3.0

Speech to Text REST API v3.0 の使用方法は、[プライベート エンドポイントが有効になっている Speech リソース](#speech-to-text-rest-api-v30)の場合と完全に同じです。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Speech to Text REST API for short audio と Text to Speech REST API

この場合、Speech to Text REST API for short audio と Text to Speech REST API の使用方法は、一般的な場合では違いはありませんが、例外が 1 つあります。 (下記の注を参照)。どちらの API も、[Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) および [Text to Speech REST API](rest-text-to-speech.md) のドキュメントで説明されているように使用する必要があります。

> [!NOTE]
> カスタム ドメインのシナリオで Speech to Text REST API for short audio と Text to Speech REST API を使用している場合は、`Ocp-Apim-Subscription-Key` ヘッダーを介して渡されたサブスクリプション キーを使用します。 (詳細は、[Speech to Text REST API for short audio](rest-speech-to-text.md#request-headers) および [Text to Speech REST API](rest-text-to-speech.md#request-headers) を参照)
>
> 認証トークンを使用してそれを `Authorization` ヘッダーを介して特殊なエンドポイントに渡すことは、音声リソースの **[ネットワーク]** セクションで **[すべてのネットワーク]** アクセス オプションを有効にした場合に "*のみ*" 機能します。 それ以外の場合は、認証トークンを取得しようとすると、`Forbidden` または `BadRequest` エラーが発生します。

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>カスタム ドメイン名は有効だがプライベート エンドポイントは有効でない Speech リソースSpeech SDK での使用方法

プライベート エンドポイントを "*使用せずに*"、カスタム ドメイン対応の音声リソースで Speech SDK を使用することは、[Speech SDK ドキュメント](speech-sdk.md)に記載されている一般的なケースと同じです。

[プライベート エンドポイント対応の音声リソース](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)と一緒に使用するようにコードを変更した場合は、次の点を考慮してください。

[プライベート エンドポイントが有効になっている Speech リソース](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)に関するセクションでは、エンドポイント URL を特定し、それを変更して、`SpeechConfig` クラス インスタンスの "エンドポイントから" および "エンドポイントで" の初期化を通じて機能させる方法について説明しました。

ただし、すべてのプライベート エンドポイントが削除された後で同じアプリケーションを実行しようとすると (対応する DNS レコードが再プロビジョニングされるまでしばらく時間をおきます)、内部サービス エラー (404) が発生します。 この理由は、[DNS レコード](#dns-configuration)が仮想ネットワーク プロキシではなく、Cognitive Services のリージョン エンドポイントを指すようになって、`/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` のような URL パスが見つからないためです。

次のコードのスタイルで `SpeechConfig` の標準のインスタンス化にアプリケーションをロールバックする必要があります。

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="learn-more"></a>詳細情報

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Speech to Text REST API](rest-speech-to-text.md)
* [Text to Speech REST API](rest-text-to-speech.md)