---
title: Azure 内で PowerShell を使用して Traffic Manager を管理する
description: このラーニング パスでは、Traffic Manager で Azure PowerShell の使用を開始します。
services: traffic-manager
documentationcenter: na
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 21076fe23301c189d9987f78706cc81691ce7a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89400536"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>PowerShell を使用した Traffic Manager の管理

Azure Resource Manager は、Azure のサービスの優先管理インターフェイスです。 Azure Traffic Manager ベースの API とツールを使用して、Azure Traffic Manager プロファイルを管理できます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>リソース モデル

Azure Traffic Manager は、Traffic Manager プロファイルと呼ばれる設定のコレクションを使用して構成されます。 このプロファイルには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、トラフィックのルーティング先となるサービス エンドポイントのリストが含まれます。

各 Traffic Manager プロファイルは、'TrafficManagerProfiles' 型のリソースで表されます。 REST API レベルでの各プロファイルの URI は次のとおりです。

`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}`

## <a name="setting-up-azure-powershell"></a>Azure PowerShell の設定

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下の手順では、Microsoft Azure PowerShell を使用します。 次の記事では、Azure PowerShell をインストールして構成する方法について説明します。

* [Azure PowerShell のインストールと構成の方法](/powershell/azure/)

この記事の例では、既存のリソース グループがあることを前提としています。 リソース グループを作成するには、次のコマンドを使用します。

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager では、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループで作成するリソースの既定の保存先として使用されます。 ただし、Traffic Manager プロファイル リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure Traffic Manager には影響しません。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

Traffic Manager プロファイルを作成するには、`New-AzTrafficManagerProfile` コマンドレットを使用します。

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

次の表で、パラメーターについて説明します。

| パラメーター | 説明 |
| --- | --- |
| 名前 |Traffic Manager プロファイル リソースのリソース名。 同じリソース グループ内のプロファイルには、一意の名前が必要です。 この名前は、DNS クエリに使用される DNS 名とは別のものです。 |
| ResourceGroupName |プロファイル リソースを含むリソース グループの名前。 |
| TrafficRoutingMethod |DNS クエリへの応答で返されるエンドポイントを特定するために使用する、トラフィック ルーティング方法を指定します。 指定できる値は、'Performance'、'Weighted' または 'Priority' です。 |
| RelativeDnsName |この Traffic Manager プロファイルで提供される DNS 名のホスト名の部分を指定します。 Azure Traffic Manager が使用する DNS ドメイン名とこの値を組み合わせて、プロファイルの完全修飾ドメイン名 (FQDN) が形成されます。 たとえば、'contoso' と値を設定すると 'contoso.trafficmanager.net' になります。 |
| TTL |DNS の TTL (Time-to-Live) を秒単位で指定します。 この TTL は、ローカル DNS リゾルバーと DNS クライアントに、この Traffic Manager プロファイルでの DNS 応答をキャッシュする時間の長さを通知します。 |
| MonitorProtocol |エンドポイントの正常性の監視に使用するプロトコルを指定します。 指定できる値は、'HTTP' と 'HTTPS' です。 |
| MonitorPort |エンドポイントの正常性の監視に使用する TCP ポートを指定します。 |
| MonitorPath |エンドポイントの正常性のプローブに使用する、エンドポイントのドメイン名の相対パスを指定します。 |

このコマンドレットは、Azure 内に Traffic Manager プロファイルを作成し、対応するプロファイル オブジェクトを PowerShell に返します。 この時点では、プロファイルにはエンドポイントがありません。 Traffic Manager プロファイルにエンドポイントを追加する方法の詳細については、Traffic Manager エンドポイントの追加に関するセクションを参照してください。

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager プロファイルの取得

既存の Traffic Manager プロファイル オブジェクトを取得するには、`Get-AzTrafficManagerProfle` コマンドレットを使用します。

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

このコマンドレットは、Traffic Manager プロファイル オブジェクトを返します。

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager プロファイルの更新

Traffic Manager プロファイルの変更は、次の 3 段階の手順で行います。

1. `Get-AzTrafficManagerProfile` を使用してプロファイルを取得する、または `New-AzTrafficManagerProfile` が返すプロファイルを使用します。
2. プロファイルを変更します。 エンドポイントを追加、削除したり、エンドポイントやプロファイルのパラメーターを変更できます。 これらの変更はオフラインで行う操作です。 プロファイルを表すメモリ内のローカル オブジェクトのみが変更されます。
3. `Set-AzTrafficManagerProfile` コマンドレットを使用して変更をコミットします。

プロファイルの RelativeDnsName を除くすべてのプロファイル プロパティを変更できます。 RelativeDnsName を変更するには、プロファイルを削除し、新しい名前で新しいプロファイルを作成する必要があります。

次の例では、プロファイルの TTL を変更する方法を示します。

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Traffic Manager エンドポイントには、次の 3 種類があります。

1. **Azure エンドポイント** : Azure でホストされるサービスです。
2. **外部エンドポイント**: Azure の外部でホストされるサービスです。
3. **入れ子になったエンドポイント**: Traffic Manager プロファイルの入れ子構造の階層を構築するために使用します。 入れ子になったエンドポイントを使用すると、複雑なアプリケーションの高度なトラフィック ルーティングの構成を行うことができます。

これら 3 つのすべてのケースで、エンドポイントの追加方法が 2 つあります。

1. 前述の 3 段階の手順を使用します。 この方法の利点は、1 回の更新でエンドポイントの変更を複数行うことができる点です。
2. New-AzTrafficManagerEndpoint コマンドレットを使用します。 この cmdlet により、1 回の操作で既存の Traffic Manager プロファイルにエンドポイントが追加されます。

## <a name="adding-azure-endpoints"></a>Azure エンドポイントの追加

Azure エンドポイントは、Azure でホストされるサービスを参照します。 2 種類の Azure エンドポイントがサポートされています。

1. Azure App Service
2. Azure PublicIpAddress リソース (ロード バランサーまたは仮想マシン NIC に接続できます)。 PublicIpAddress には、Traffic Manager で使用するために DNS 名を割り当てておく必要があることに注意してください。

いずれの場合も、次のことが当てはまります。

* サービスは、`Add-AzTrafficManagerEndpointConfig` または `New-AzTrafficManagerEndpoint`の 'targetResourceId' パラメーターを使用して指定します。
* 'Target' と 'EndpointLocation' はTargetResourceId で指定されます。
* "Weight" の指定は省略できます。 Weight が使用されるのは、プロファイルがトラフィック ルーティング方法として 'Weighted' を使用するように構成されている場合のみです。 それ以外の場合は無視されます。 指定する場合、値は 1 から 1000 までの数値である必要があります。 既定値は '1' です。
* "Priority" の指定は省略できます。 Priority が使用されるのは、プロファイルがトラフィック ルーティング方法として 'Priority' を使用するように構成されている場合のみです。 それ以外の場合は無視されます。 有効な値は 1 から 1000 で、数が小さいほど優先順位が高くなります。 1 つのエンドポイントに指定した場合は、すべてのエンドポイントに指定する必要があります。 省略した場合は、1 から始まる既定値が、エンドポイントのリスト順に適用されます。

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>例 1: `Add-AzTrafficManagerEndpointConfig` を使用して App Service エンドポイントを追加する

この例では Traffic Manager プロファイルを作成し、`Add-AzTrafficManagerEndpointConfig` コマンドレットを使用して 2 つのアプリ サービス アプリ エンドポイントを追加します。

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>例 2: `New-AzTrafficManagerEndpoint` を使用して、publicIpAddress エンドポイントを追加する

この例では、パブリック IP アドレス リソースが Traffic Manager プロファイルに追加されます。 パブリック IP アドレスでは DNS 名が構成されている必要があります。また、パブリック IP アドレスは、VM の NIC、またはロード バランサーにバインドすることができます。

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>外部エンドポイントの追加

Traffic Manager は、外部エンドポイントを使用して、Azure の外部でホストされているサービスにトラフィックを送信します。 Azure エンドポイントの場合と同様に、`Add-AzTrafficManagerEndpointConfig` の後に `Set-AzTrafficManagerProfile`または `New-AzTrafficManagerEndpoint`を使用することによって、外部エンドポイントを追加できます。

外部エンドポイントを指定する場合は、次のことが当てはまります。

* 'Target' パラメーターを使用してエンドポイントのドメイン名を指定する必要があります。
* 'Performance' トラフィック ルーティング方法を使用する場合、'EndpointLocation' が必須です。 それ以外の場合は省略可能です。 値には、[有効な Azure リージョン名](https://azure.microsoft.com/regions/)を指定する必要があります。
* Azure エンドポイントについては、'Weight' と 'Priority' は省略可能です。

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>例 1: `Add-AzTrafficManagerEndpointConfig` と `Set-AzTrafficManagerProfile` を使用して外部のエンドポイントを追加する

この例では、Traffic Manager プロファイルを作成し、2 つの外部エンドポイントを追加して、変更をコミットします。

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>例 2: `New-AzTrafficManagerEndpoint` を使用して外部のエンドポイントを追加する

この例では、既存のプロファイルに外部エンドポイントを追加しています。 プロファイルは、プロファイル名とリソース グループ名を使用して指定します。

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>"入れ子" になったエンドポイントの追加

Traffic Manager プロファイルごとに 1 つのトラフィック ルーティング方法を指定します。 ただし、単一の Traffic Manager プロファイルで提供されるものよりも高度なトラフィック ルーティングが必要になるシナリオもあります。 Traffic Manager プロファイルを入れ子にして、複数のトラフィック ルーティング方法の利点を組み合わせることができます。 入れ子になったプロファイルを使用すると、既定の Traffic Manager の動作をオーバーライドして、より大規模でより複雑なアプリケーションのデプロイに対応することができます。 詳細なサンプルについては、「[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」をご覧ください。

入れ子になったエンドポイントは、親プロファイルで特定のエンドポイントの種類 "NestedEndpoints" を使って構成します。 入れ子になったエンドポイントを指定する場合は、次のことが当てはまります。

* エンドポイントは、'targetResourceId' パラメーターを使用して指定する必要があります。
* 'Performance' トラフィック ルーティング方法を使用する場合、'EndpointLocation' が必須です。 それ以外の場合は省略可能です。 値には、[有効な Azure リージョン名](https://azure.microsoft.com/regions/)を指定する必要があります。
* Azure エンドポイントについては、'Weight' と 'Priority' は省略可能です。
* 'MinChildEndpoints' パラメーターは省略可能で、 既定値は '1' です。 利用可能なエンドポイントの数がこのしきい値を下回った場合、親プロファイルは子プロファイルを '機能低下' と見なし、トラフィックを親プロファイルの他のエンドポイントにトラフィックを振り向けます。

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>例 1: `Add-AzTrafficManagerEndpointConfig` と `Set-AzTrafficManagerProfile` を使用して入れ子になったエンドポイントを追加する

この例では、Traffic Manager の子プロファイルと親プロファイルを新たに作成します。入れ子になったエンドポイントとして子を親に追加して変更をコミットします。

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

簡潔にするために、この例では、子プロファイルや親プロファイルには他のエンドポイントを追加していません。

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>例 2: `New-AzTrafficManagerEndpoint` を使用して入れ子になったエンドポイントを追加する

この例では、既存の親プロファイルに、入れ子になったエンドポイントとして既存の子プロファイルを追加しています。 プロファイルは、プロファイル名とリソース グループ名を使用して指定します。

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>別のサブスクリプションからのエンドポイントの追加

Traffic Manager では、別のサブスクリプションからのエンドポイントを操作できます。 追加するエンドポイントがあるサブスクリプションに切り替えて、Traffic Manager に必要な入力を取得する必要があります。 その後、Traffic Manager プロファイルがあるサブスクリプションに切り替えて、そのサブスクリプションにエンドポイントを追加する必要があります。 次の例は、パブリック IP アドレスを使ってこれを行う方法を示しています。

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager エンドポイントの更新

既存の Traffic Manager エンドポイントを更新する方法は 2 通りあります。

1. `Get-AzTrafficManagerProfile` を使用して Traffic Manager プロファイルを取得し、プロファイル内のエンドポイントのプロパティを更新した後、`Set-AzTrafficManagerProfile` を使用して変更をコミットします。 この方法の利点は、1 回の操作で複数のエンドポイントを更新できることです。
2. `Get-AzTrafficManagerEndpoint` を使用して Traffic Manager エンドポイントを取得し、エンドポイントのプロパティを更新した後、`Set-AzTrafficManagerEndpoint` を使用して変更をコミットします。 この方法は、プロファイル内の Endpoints 配列にインデックスを作成する必要がないため、より単純です。

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>例 1: `Get-AzTrafficManagerProfile` と `Set-AzTrafficManagerProfile` を使用してエンドポイントを更新する

この例では、既存のプロファイル内の 2 つのエンドポイントの優先度を変更します。

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>例 2: `Get-AzTrafficManagerEndpoint` と `Set-AzTrafficManagerEndpoint` を使用してエンドポイントを更新する

この例では、既存のプロファイル内の 1 つのエンドポイントの重み付けを変更します。

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>エンドポイントとプロファイルの有効化と無効化

Traffic Manager を使用すると、プロファイル全体だけでなく、個々のエンドポイントを有効にしたり無効にしたりすることができます。
これらの変更は、エンドポイントまたはプロファイルのリソースを取得、更新、設定することで実行できます。 このような一般的な操作を合理化するために、専用のコマンドレットを使用した操作もサポートされています。

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>例 1: Traffic Manager プロファイルを有効または無効にする

Traffic Manager プロファイルを有効にするには、`Enable-AzTrafficManagerProfile` を使用します。 プロファイルは、プロファイル オブジェクトを使用して指定できます。 プロファイル オブジェクトは、パイプライン経由、または '-TrafficManagerProfile' パラメーターを使用して渡すことができます。 この例では、プロファイル名とリソース グループ名でプロファイルを指定します。

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager プロファイルを無効にするには:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Disable-AzTrafficManagerProfile コマンドレットでは、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>例 2: Traffic Manager エンドポイントを有効または無効にする

Traffic Manager エンドポイントを無効にするには、`Enable-AzTrafficManagerEndpoint` を使用します。 エンドポイントの指定には次の 2 つの方法があります。

1. パイプライン経由、または '-TrafficManagerEndpoint' パラメーターを使用して渡された TrafficManagerEndpoint オブジェクトを使用する
2. エンドポイント名、エンドポイントの種類、プロファイル名、リソース グループ名を使用する

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

同様に、Traffic Manager エンドポイントを無効にするには、次のように入力します。

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

`Disable-AzTrafficManagerProfile` と同様に、`Disable-AzTrafficManagerEndpoint` コマンドレットでも、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

## <a name="delete-a-traffic-manager-endpoint"></a>Traffic Manager エンドポイントの削除

個々のエンドポイントを削除するには、`Remove-AzTrafficManagerEndpoint` コマンドレットを使用します。

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

このコマンドレットでは、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager プロファイルの削除

Traffic Manager プロファイルを削除するには、プロファイル名とリソース グループ名を指定して、`Remove-AzTrafficManagerProfile` コマンドレットを使用します。

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

このコマンドレットでは、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

プロファイル オブジェクトを使用して、削除するプロファイルを指定することもできます。

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

また、このシーケンスをパイプすることもできます。

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>次のステップ

[Traffic Manager の監視](traffic-manager-monitoring.md)

[Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)
