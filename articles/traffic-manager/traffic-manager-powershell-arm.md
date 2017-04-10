---
title: "PowerShell を使用して Azure の Traffic Manager を管理する | Microsoft Docs"
description: "Azure Resource Manager での Traffic Manager に対する Powershell の使用"
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: c2fb44817f168eee8303d0c07473f043ae30d350
ms.lasthandoff: 03/18/2017

---

# <a name="using-powershell-to-manage-traffic-manager"></a>PowerShell を使用した Traffic Manager の管理

Azure Resource Manager は、Azure のサービスの優先管理インターフェイスです。 Azure Traffic Manager ベースの API とツールを使用して、Azure Traffic Manager プロファイルを管理できます。

## <a name="resource-model"></a>リソース モデル

Azure Traffic Manager は、Traffic Manager プロファイルと呼ばれる設定のコレクションを使用して構成されます。 このプロファイルには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、トラフィックのルーティング先となるサービス エンドポイントのリストが含まれます。

各 Traffic Manager プロファイルは、'TrafficManagerProfiles' 型のリソースで表されます。 REST API レベルでの各プロファイルの URI は次のとおりです。

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Azure PowerShell の設定

以下の手順では、Microsoft Azure PowerShell を使用します。 次の記事では、Azure PowerShell をインストールして構成する方法について説明します。

* [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)

この記事の例では、既存のリソース グループがあることを前提としています。 リソース グループを作成するには、次のコマンドを使用します。

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager では、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループで作成するリソースの既定の保存先として使用されます。 ただし、Traffic Manager プロファイル リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure Traffic Manager には影響しません。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

Traffic Manager プロファイルを作成するには、`New-AzureRmTrafficManagerProfile` コマンドレットを使用します。

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
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

このコマンドレットは、Azure 内に Traffic Manager プロファイルを作成し、対応するプロファイル オブジェクトを PowerShell に返します。 この時点では、プロファイルにはエンドポイントがありません。 Traffic Manager プロファイルにエンドポイントを追加する方法の詳細については、「[Traffic Manager エンドポイントの追加](#adding-traffic-manager-endpoints)をご覧ください。

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager プロファイルの取得

既存の Traffic Manager プロファイル オブジェクトを取得するには、`Get-AzureRmTrafficManagerProfle` コマンドレットを使用します。

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

このコマンドレットは、Traffic Manager プロファイル オブジェクトを返します。

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager プロファイルの更新

Traffic Manager プロファイルの変更は、次の 3 段階の手順で行います。

1. `Get-AzureRmTrafficManagerProfile` を使用してプロファイルを取得する、または `New-AzureRmTrafficManagerProfile` が返すプロファイルを使用します。
2. プロファイルを変更します。 エンドポイントを追加、削除したり、エンドポイントやプロファイルのパラメーターを変更できます。 これらの変更はオフラインで行う操作です。 プロファイルを表すメモリ内のローカル オブジェクトのみが変更されます。
3. `Set-AzureRmTrafficManagerProfile` コマンドレットを使用して変更をコミットします。

プロファイルの RelativeDnsName を除くすべてのプロファイル プロパティを変更できます。 RelativeDnsName を変更するには、プロファイルを削除し、新しい名前で新しいプロファイルを作成する必要があります。

次の例では、プロファイルの TTL を変更する方法を示します。

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Traffic Manager エンドポイントには、次の 3 種類があります。

1. **Azure エンドポイント** : Azure でホストされるサービスです。
2. **外部エンドポイント**: Azure の外部でホストされるサービスです。
3. **入れ子になったエンドポイント**: Traffic Manager プロファイルの入れ子構造の階層を構築するために使用します。 入れ子になったエンドポイントを使用すると、複雑なアプリケーションの高度なトラフィック ルーティングの構成を行うことができます。

これら 3 つのすべてのケースで、エンドポイントの追加方法が 2 つあります。

1. 前述の 3 段階の手順を使用します。 この方法の利点は、1 回の更新でエンドポイントの変更を複数行うことができる点です。
2. New-AzureRmTrafficManagerEndpoint コマンドレットを使用します。 この cmdlet により、1 回の操作で既存の Traffic Manager プロファイルにエンドポイントが追加されます。

## <a name="adding-azure-endpoints"></a>Azure エンドポイントの追加

Azure エンドポイントは、Azure でホストされるサービスを参照します。 2 種類の Azure エンドポイントがサポートされています。

1. Azure Web Apps 
2. Azure PublicIpAddress リソース (ロード バランサーまたは仮想マシン NIC に接続できます)。 PublicIpAddress には、Traffic Manager で使用するために DNS 名を割り当てておく必要があることに注意してください。

いずれの場合も、次のことが当てはまります。

* サービスは、`Add-AzureRmTrafficManagerEndpointConfig` または `New-AzureRmTrafficManagerEndpoint`の 'targetResourceId' パラメーターを使用して指定します。
* 'Target' と 'EndpointLocation' はTargetResourceId で指定されます。
* "Weight" の指定は省略できます。 Weight が使用されるのは、プロファイルがトラフィック ルーティング方法として 'Weighted' を使用するように構成されている場合のみです。 それ以外の場合は、無視されます。 指定する場合、値は 1 から 1000 までの数値である必要があります。 既定値は '1' です。
* "Priority" の指定は省略できます。 Priority が使用されるのは、プロファイルがトラフィック ルーティング方法として 'Priority' を使用するように構成されている場合のみです。 それ以外の場合は、無視されます。 有効な値は 1 から 1000 で、数が小さいほど優先順位が高くなります。 1 つのエンドポイントに指定した場合は、すべてのエンドポイントに指定する必要があります。 省略した場合は、1 から始まる既定値が、エンドポイントのリスト順に適用されます。

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>例 1: `Add-AzureRmTrafficManagerEndpointConfig` を使用して Web アプリ エンドポイントを追加する

この例では Traffic Manager プロファイルを作成し、`Add-AzureRmTrafficManagerEndpointConfig` コマンドレットを使用して 2 つの Web アプリ エンドポイントを追加します。

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>例 2: `New-AzureRmTrafficManagerEndpoint` を使用して、publicIpAddress エンドポイントを追加する

この例では、パブリック IP アドレス リソースが Traffic Manager プロファイルに追加されます。 パブリック IP アドレスでは DNS 名が構成されている必要があります。また、パブリック IP アドレスは、VM の NIC、またはロード バランサーにバインドすることができます。

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>外部エンドポイントの追加

Traffic Manager は、外部エンドポイントを使用して、Azure の外部でホストされているサービスにトラフィックを送信します。 Azure エンドポイントの場合と同様に、`Add-AzureRmTrafficManagerEndpointConfig` の後に `Set-AzureRmTrafficManagerProfile`または `New-AzureRMTrafficManagerEndpoint`を使用することによって、外部エンドポイントを追加できます。

外部エンドポイントを指定する場合は、次のことが当てはまります。

* 'Target' パラメーターを使用してエンドポイントのドメイン名を指定する必要があります。
* 'Performance' トラフィック ルーティング方法を使用する場合、'EndpointLocation' が必須です。 それ以外の場合は省略可能です。 値には、[有効な Azure リージョン名](https://azure.microsoft.com/regions/)を指定する必要があります。
* Azure エンドポイントについては、'Weight' と 'Priority' は省略可能です。

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>例 1: `Add-AzureRmTrafficManagerEndpointConfig` と `Set-AzureRmTrafficManagerProfile` を使用して外部のエンドポイントを追加する

この例では、Traffic Manager プロファイルを作成し、2 つの外部エンドポイントを追加して、変更をコミットします。

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>例 2: `New-AzureRmTrafficManagerEndpoint` を使用して外部のエンドポイントを追加する

この例では、既存のプロファイルに外部エンドポイントを追加しています。 プロファイルは、プロファイル名とリソース グループ名を使用して指定します。

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>"入れ子" になったエンドポイントの追加

Traffic Manager プロファイルごとに 1 つのトラフィック ルーティング方法を指定します。 ただし、単一の Traffic Manager プロファイルで提供されるものよりも高度なトラフィック ルーティングが必要になるシナリオもあります。 Traffic Manager プロファイルを入れ子にして、複数のトラフィック ルーティング方法の利点を組み合わせることができます。 入れ子になったプロファイルを使用すると、既定の Traffic Manager の動作はオーバーライドされ、より大規模で複雑なアプリケーション デプロイをサポートできます。 詳細なサンプルについては、「[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」をご覧ください。

入れ子になったエンドポイントは、親プロファイルで特定のエンドポイントの種類 "NestedEndpoints" を使って構成します。 入れ子になったエンドポイントを指定する場合は、次のことが当てはまります。

* エンドポイントは、'targetResourceId' パラメーターを使用して指定する必要があります。
* 'Performance' トラフィック ルーティング方法を使用する場合、'EndpointLocation' が必須です。 それ以外の場合は省略可能です。 値には、[有効な Azure リージョン名](http://azure.microsoft.com/regions/)を指定する必要があります。
* Azure エンドポイントについては、'Weight' と 'Priority' は省略可能です。
* 'MinChildEndpoints' パラメーターは省略可能で、 既定値は '1' です。 利用可能なエンドポイントの数がこのしきい値を下回った場合、親プロファイルは子プロファイルを '機能低下' と見なし、トラフィックを親プロファイルの他のエンドポイントにトラフィックを振り向けます。

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>例 1: `Add-AzureRmTrafficManagerEndpointConfig` と `Set-AzureRmTrafficManagerProfile` を使用して入れ子になったエンドポイントを追加する

この例では、Traffic Manager の子プロファイルと親プロファイルを新たに作成します。入れ子になったエンドポイントとして子を親に追加して変更をコミットします。

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

簡潔にするために、この例では、子プロファイルや親プロファイルには他のエンドポイントを追加していません。

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>例 2: `New-AzureRmTrafficManagerEndpoint` を使用して入れ子になったエンドポイントを追加する

この例では、既存の親プロファイルに、入れ子になったエンドポイントとして既存の子プロファイルを追加しています。 プロファイルは、プロファイル名とリソース グループ名を使用して指定します。

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager エンドポイントの更新

既存の Traffic Manager エンドポイントを更新する方法は 2 通りあります。

1. `Get-AzureRmTrafficManagerProfile` を使用して Traffic Manager プロファイルを取得し、プロファイル内のエンドポイントのプロパティを更新した後、`Set-AzureRmTrafficManagerProfile` を使用して変更をコミットします。 この方法の利点は、1 回の操作で複数のエンドポイントを更新できることです。
2. `Get-AzureRmTrafficManagerEndpoint` を使用して Traffic Manager エンドポイントを取得し、エンドポイントのプロパティを更新した後、`Set-AzureRmTrafficManagerEndpoint` を使用して変更をコミットします。 この方法は、プロファイル内の Endpoints 配列にインデックスを作成する必要がないため、より単純です。

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>例 1: `Get-AzureRmTrafficManagerProfile` と `Set-AzureRmTrafficManagerProfile` を使用してエンドポイントを更新する

この例では、既存のプロファイル内の 2 つのエンドポイントの優先度を変更します。

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>例 2: `Get-AzureRmTrafficManagerEndpoint` と `Set-AzureRmTrafficManagerEndpoint` を使用してエンドポイントを更新する

この例では、既存のプロファイル内の 1 つのエンドポイントの重み付けを変更します。

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>エンドポイントとプロファイルの有効化と無効化

Traffic Manager を使用すると、プロファイル全体だけでなく、個々のエンドポイントを有効にしたり無効にしたりすることができます。
これらの変更は、エンドポイントまたはプロファイルのリソースを取得、更新、設定することで実行できます。 このような一般的な操作を合理化するために、専用のコマンドレットを使用した操作もサポートされています。

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>例 1: Traffic Manager プロファイルを有効または無効にする

Traffic Manager プロファイルを有効にするには、`Enable-AzureRmTrafficManagerProfile` を使用します。 プロファイルは、プロファイル オブジェクトを使用して指定できます。 プロファイル オブジェクトは、パイプライン経由、または '-TrafficManagerProfile' パラメーターを使用して渡すことができます。 この例では、プロファイル名とリソース グループ名でプロファイルを指定します。

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager プロファイルを無効にするには:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Disable-AzureRmTrafficManagerProfile コマンドレットでは、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>例 2: Traffic Manager エンドポイントを有効または無効にする

Traffic Manager エンドポイントを無効にするには、`Enable-AzureRmTrafficManagerEndpoint` を使用します。 エンドポイントの指定には次の 2 つの方法があります。

1. パイプライン経由、または '-TrafficManagerEndpoint' パラメーターを使用して渡された TrafficManagerEndpoint オブジェクトを使用する
2. エンドポイント名、エンドポイントの種類、プロファイル名、リソース グループ名を使用する

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

同様に、Traffic Manager エンドポイントを無効にするには、次のように入力します。

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

`Disable-AzureRmTrafficManagerProfile` と同様に、`Disable-AzureRmTrafficManagerEndpoint` コマンドレットでも、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

## <a name="delete-a-traffic-manager-endpoint"></a>Traffic Manager エンドポイントの削除

個々のエンドポイントを削除するには、`Remove-AzureRmTrafficManagerEndpoint` コマンドレットを使用します。

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

このコマンドレットでは、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager プロファイルの削除

Traffic Manager プロファイルを削除するには、プロファイル名とリソース グループ名を指定して、`Remove-AzureRmTrafficManagerProfile` コマンドレットを使用します。

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

このコマンドレットでは、確認を求めるメッセージが表示されます。 '-Force' パラメーターを使用すると、このプロンプトを表示しないようにすることができます。

プロファイル オブジェクトを使用して、削除するプロファイルを指定することもできます。

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

また、このシーケンスをパイプすることもできます。

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>次のステップ

[Traffic Manager の監視](traffic-manager-monitoring.md)

[Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)

