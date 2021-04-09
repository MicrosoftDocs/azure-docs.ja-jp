---
title: エンドポイント クォータを増やす- LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) では、1 つのキーのクォータを超えて、エンドポイント要求クォータを増やすことができます。 そのためには、LUIS の複数のキーを作成し、 **公開** ページの **リソースとキー** セクションで LUIS アプリケーションに追加します。
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609653"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Microsoft Azure Traffic Manager を使用した複数のキーにわたるエンドポイント クォータの管理
Language Understanding (LUIS) では、1 つのキーのクォータを超えて、エンドポイント要求クォータを増やすことができます。 そのためには、LUIS の複数のキーを作成し、 **公開** ページの **リソースとキー** セクションで LUIS アプリケーションに追加します。

クライアント アプリケーションでは、複数のキーにわたってトラフィックを管理する必要があります。 LUIS ではこれは行われません。

この記事では、Azure [Traffic Manager][traffic-manager-marketing] を使用して、複数のキーにわたってトラフィックを管理する方法について説明します。 トレーニングされた公開済みの LUIS アプリが必要です。 ない場合は、事前構築済みドメインの[クイック スタート](luis-get-started-create-app.md)に従ってください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Azure portal で PowerShell に接続する
[Azure][azure-portal] portal で PowerShell ウィンドウを開きます。 PowerShell ウィンドウのアイコンは、上部のナビゲーション バーの **[>_]** です。 PowerShell をポータルから使用することで、PowerShell の最新バージョンを取得し、認証を行います。 ポータルの PowerShell には、[Azure Storage](https://azure.microsoft.com/services/storage/) アカウントが必要です。

![Powershell ウィンドウが開いている Azure portal のスクリーンショット](./media/traffic-manager/azure-portal-powershell.png)

以下のセクションでは、[Traffic Manager PowerShell コマンドレット](/powershell/module/az.trafficmanager/#traffic_manager)を使用します。

## <a name="create-azure-resource-group-with-powershell"></a>PowerShell を使用して Azure リソース グループを作成する
Azure リソースを作成する前に、すべてのリソースを含むリソース グループを作成します。 リソース グループに `luis-traffic-manager` という名前を付け、`West US` リージョンを使用します。 リソース グループのリージョンには、そのグループに関するメタデータが保存されます。 リソースが別のリージョンにあっても、速度が低下することはありません。

次のように **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** コマンドレットを使用して、リソース グループを作成します。

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS キーを作成して合計エンドポイント クォータを増やす
1. Azure portal で、2 つの **Language Understanding** キーを作成します。`West US` に 1 つと、`East US` に 1 つです。 前のセクションで作成した `luis-traffic-manager` という名前の既存のリソース グループを使用します。

    ![luis-traffic-manager リソース グループ内の 2 つの LUIS キーが表示された Azure portal のスクリーンショット](./media/traffic-manager/luis-keys.png)

2. [LUIS][LUIS] Web サイトの **[管理]** セクションの **[Azure リソース]** ページで、アプリにキーを割り当てて、右上のメニューで **[公開]** ボタンを選択してアプリを再公開します。

    **エンドポイント** 列の URL の例では、エンドポイント キーをクエリ パラメーターとして指定した GET 要求を使用します。 2 つの新しいキーのエンドポイント URL をコピーします。 これらは、この記事で後述する Traffic Manager の構成の一部として使用されます。

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Traffic Manager を使用して複数のキーにわたって LUIS エンドポイント要求を管理する
Traffic Manager により、エンドポイント用の新しい DNS アクセス ポイントが作成されます。 これは、ゲートウェイやプロキシとして機能するのではなく、厳密には DNS レベルで機能します。 この例では DNS レコードは変更しません。 特定の要求に対して適切なエンドポイントを取得するために、DNS ライブラリを使用して Traffic Manager と通信します。 LUIS を対象とする "_各_" 要求では、使用する LUIS エンドポイントを決定するために、Traffic Manager の要求が必要となります。

### <a name="polling-uses-luis-endpoint"></a>ポーリングで LUIS エンドポイントを使用する
Traffic Manager では、エンドポイントを定期的にポーリングして、エンドポイントが引き続き使用可能であることを確認します。 ポーリングされる Traffic Manager URL は、GET 要求を使用してアクセス可能である必要があり、200 を返す必要があります。 **[Publish]\(公開\)** ページのエンドポイント URL がこれを実現します。 エンドポイント キーによってルートとクエリ文字列パラメーターが異なるため、各エンドポイント キーには異なるポーリング パスが必要です。 Traffic Manager がポーリングするたびに、クォータ要求のコストがかかります。 LUIS エンドポイントの **q** クエリ文字列パラメーターは、LUIS に送信される発話です。 このパラメーターは、発話の送信ではなく、Traffic Manager の構成時に、デバッグ手法として、Traffic Manager によるポーリングを LUIS エンドポイント ログに追加するために使用されます。

各 LUIS エンドポイントには独自のパスが必要であるため、独自の Traffic Manager プロファイルが必要です。 プロファイル全体を管理するために、["_入れ子になった_" Traffic Manager](../../traffic-manager/traffic-manager-nested-profiles.md) アーキテクチャを作成します。 1 つの親プロファイルが複数の子プロファイルを参照し、それらのトラフィックを管理します。

Traffic Manager が構成されたら、ログがポーリングでいっぱいにならないように、logging=false クエリ文字列パラメーターを使用するようにパスを必ず変更してください。

## <a name="configure-traffic-manager-with-nested-profiles"></a>入れ子になったプロファイルで Traffic Manager を構成する
以下のセクションでは、2 つの子プロファイルを作成します。1 つは東部 LUIS キー用、もう 1 つは西部 LUIS キー用です。 次に、親プロファイルを作成し、2 つの子プロファイルを親プロファイルに追加します。

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>PowerShell を使用して米国東部 Traffic Manager プロファイルを作成する
米国東部 Traffic Manager プロファイルを作成するには、プロファイルの作成、エンドポイントの追加、エンドポイントの設定という複数の手順があります。 Traffic Manager プロファイルには多数のエンドポイントを含めることができますが、各エンドポイントでは同じ検証パスを使用します。 リージョンとエンドポイント キーのために、東部と西部のサブスクリプションの LUIS エンドポイント URL が異なるため、各 LUIS エンドポイントはプロファイル内で単一のエンドポイントである必要があります。

1. **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** コマンドレットを使用してプロファイルを作成する

    次のコマンドレットを使用してプロファイルを作成します。 `appIdLuis` と `subscriptionKeyLuis` を必ず変更してください。 subscriptionKey は米国東部の LUIS キーに対応します。 LUIS アプリ ID とエンドポイント キーを含むパスが正しくない場合は、Traffic Manager が LUIS エンドポイントを正常に要求できないため、Traffic Manager のポーリングは `degraded` の状態になります。 LUIS エンドポイント ログで `q` の値を確認できるように、この値が `traffic-manager-east` であることを確認します。

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    次の表に、このコマンドレットの各変数を示します。

    |構成パラメーター|変数名または値|目的|
    |--|--|--|
    |-Name|luis-profile-eastus|Azure portal での Traffic Manager 名|
    |-ResourceGroupName|luis-traffic-manager|前のセクションで作成済み|
    |-TrafficRoutingMethod|パフォーマンス|詳細については、「[Traffic Manager のルーティング方法][routing-methods]」を参照してください。 パフォーマンスを使用する場合、Traffic Manager に対する URL 要求は、ユーザーのリージョンから送信される必要があります。 チャットボットや他のアプリケーションを経由する場合は、Traffic Manager の呼び出しでそのチャットボットがリージョンを模倣する必要があります。 |
    |-RelativeDnsName|luis-dns-eastus|サービスのサブドメイン (luis-dns-eastus.trafficmanager.net)|
    |-Ttl|30|ポーリング間隔 (30 秒)|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS のプロトコルとポートは HTTPS/443 です。|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|`<appIdLuis>` と `<subscriptionKeyLuis>` を独自の値に置き換えます。|

    要求が成功した場合、応答はありません。

2. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** コマンドレットを使用して、米国東部エンドポイントを追加する

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    次の表に、このコマンドレットの各変数を示します。

    |構成パラメーター|変数名または値|目的|
    |--|--|--|
    |-EndpointName|luis-east-endpoint|プロファイルの下で表示されるエンドポイント名|
    |-TrafficManagerProfile|$eastprofile|手順 1. で作成したプロファイル オブジェクトを使用します。|
    |-Type|ExternalEndpoints|詳細については、「[Traffic Manager エンドポイント][traffic-manager-endpoints]」をご覧ください |
    |-Target|eastus.api.cognitive.microsoft.com|LUIS エンドポイントのドメイン|
    |-EndpointLocation|"eastus"|エンドポイントのリージョン|
    |-EndpointStatus|Enabled|作成時にエンドポイントを有効にします。|

    正常な応答は次のようになります。

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. **[Set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** コマンドレットを使用して米国東部エンドポイントを設定する

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    正常な応答は手順 2. と同じ応答になります。

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>PowerShell を使用して米国西部 Traffic Manager プロファイルを作成する
米国西部 Traffic Manager プロファイルを作成するには、プロファイルの作成、エンドポイントの追加、エンドポイントの設定という同じ手順に従います。

1. **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** コマンドレットを使用してプロファイルを作成する

    次のコマンドレットを使用してプロファイルを作成します。 `appIdLuis` と `subscriptionKeyLuis` を必ず変更してください。 subscriptionKey は米国東部の LUIS キーに対応します。 LUIS アプリ ID とエンドポイント キーを含むパスが正しくない場合は、Traffic Manager が LUIS エンドポイントを正常に要求できないため、Traffic Manager のポーリングは `degraded` の状態になります。 LUIS エンドポイント ログで `q` の値を確認できるように、この値が `traffic-manager-west` であることを確認します。

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    次の表に、このコマンドレットの各変数を示します。

    |構成パラメーター|変数名または値|目的|
    |--|--|--|
    |-Name|luis-profile-westus|Azure portal での Traffic Manager 名|
    |-ResourceGroupName|luis-traffic-manager|前のセクションで作成済み|
    |-TrafficRoutingMethod|パフォーマンス|詳細については、「[Traffic Manager のルーティング方法][routing-methods]」を参照してください。 パフォーマンスを使用する場合、Traffic Manager に対する URL 要求は、ユーザーのリージョンから送信される必要があります。 チャットボットや他のアプリケーションを経由する場合は、Traffic Manager の呼び出しでそのチャットボットがリージョンを模倣する必要があります。 |
    |-RelativeDnsName|luis-dns-westus|サービスのサブドメイン (luis-dns-westus.trafficmanager.net)|
    |-Ttl|30|ポーリング間隔 (30 秒)|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS のプロトコルとポートは HTTPS/443 です。|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|`<appId>` と `<subscriptionKey>` を独自の値に置き換えます。 このエンドポイント キーは東部のエンドポイント キーとは異なることに注意してください。|

    要求が成功した場合、応答はありません。

2. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** コマンドレットを使用して、米国西部エンドポイントを追加する

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    次の表に、このコマンドレットの各変数を示します。

    |構成パラメーター|変数名または値|目的|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|プロファイルの下で表示されるエンドポイント名|
    |-TrafficManagerProfile|$westprofile|手順 1. で作成したプロファイル オブジェクトを使用します。|
    |-Type|ExternalEndpoints|詳細については、「[Traffic Manager エンドポイント][traffic-manager-endpoints]」をご覧ください |
    |-Target|westus.api.cognitive.microsoft.com|LUIS エンドポイントのドメイン|
    |-EndpointLocation|"westus"|エンドポイントのリージョン|
    |-EndpointStatus|Enabled|作成時にエンドポイントを有効にします。|

    正常な応答は次のようになります。

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** コマンドレットを使用して米国西部エンドポイントを設定する

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    正常な応答は手順 2. と同じ応答です。

### <a name="create-parent-traffic-manager-profile"></a>Traffic Manager 親プロファイルを作成する
Traffic Manager 親プロファイルを作成し、2 つの Traffic Manager 子プロファイルを親にリンクします。

1. **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** コマンドレットを使用して親プロファイルを作成する

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    次の表に、このコマンドレットの各変数を示します。

    |構成パラメーター|変数名または値|目的|
    |--|--|--|
    |-Name|luis-profile-parent|Azure portal での Traffic Manager 名|
    |-ResourceGroupName|luis-traffic-manager|前のセクションで作成済み|
    |-TrafficRoutingMethod|パフォーマンス|詳細については、「[Traffic Manager のルーティング方法][routing-methods]」を参照してください。 パフォーマンスを使用する場合、Traffic Manager に対する URL 要求は、ユーザーのリージョンから送信される必要があります。 チャットボットや他のアプリケーションを経由する場合は、Traffic Manager の呼び出しでそのチャットボットがリージョンを模倣する必要があります。 |
    |-RelativeDnsName|luis-dns-parent|サービスのサブドメイン (luis-dns-parent.trafficmanager.net)|
    |-Ttl|30|ポーリング間隔 (30 秒)|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS のプロトコルとポートは HTTPS/443 です。|
    |-MonitorPath|`/`|子エンドポイントのパスが代わりに使用されるため、このパスは重要ではありません。|

    要求が成功した場合、応答はありません。

2. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** と **NestedEndpoints** 型を使用して、米国東部の子プロファイルを親に追加する

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    次の表に、このコマンドレットの各変数を示します。

    |構成パラメーター|変数名または値|目的|
    |--|--|--|
    |-EndpointName|child-endpoint-useast|東部プロファイル|
    |-TrafficManagerProfile|$parentprofile|このエンドポイントの割り当て先のプロファイル|
    |-Type|NestedEndpoints|詳しくは、「[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)」をご覧ください。 |
    |-TargetResourceId|$eastprofile.Id|子プロファイルの ID|
    |-EndpointStatus|Enabled|親に追加した後のエンドポイントの状態|
    |-EndpointLocation|"eastus"|リソースの [Azure リージョン名](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|子エンドポイントの最小数|

    正常な応答は次のようになります。応答には、新しい `child-endpoint-useast` エンドポイントが含まれています。

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** コマンドレットと **NestedEndpoints** 型を使用して、米国西部の子プロファイルを親に追加する

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    次の表に、このコマンドレットの各変数を示します。

    |構成パラメーター|変数名または値|目的|
    |--|--|--|
    |-EndpointName|child-endpoint-uswest|西部プロファイル|
    |-TrafficManagerProfile|$parentprofile|このエンドポイントの割り当て先のプロファイル|
    |-Type|NestedEndpoints|詳しくは、「[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)」をご覧ください。 |
    |-TargetResourceId|$westprofile.Id|子プロファイルの ID|
    |-EndpointStatus|Enabled|親に追加した後のエンドポイントの状態|
    |-EndpointLocation|"westus"|リソースの [Azure リージョン名](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|子エンドポイントの最小数|

    正常な応答は次のようになります。応答には、以前の `child-endpoint-useast` エンドポイントと新しい `child-endpoint-uswest` エンドポイントの両方が含まれています。

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** コマンドレットを使用してエンドポイントを設定する

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    正常な応答は手順 3. と同じ応答です。

### <a name="powershell-variables"></a>PowerShell 変数
これまでのセクションで、`$eastprofile`、`$westprofile`、`$parentprofile` の 3 つの PowerShell 変数が作成されました。 これらの変数は、Traffic Manager の構成の最後に使用されます。 変数を作成しない場合、作成するのを忘れた場合、または PowerShell ウィンドウがタイムアウトした場合は、PowerShell コマンドレットの **[Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** を使用してプロファイルを再度取得し、変数に割り当てることができます。

山かっこ (`<>`) で囲まれた項目を、必要な 3 つの各プロファイルの正しい値に置き換えます。

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Traffic Manager が機能していることを確認する
Traffic Manager プロファイルが機能していることを確認するには、プロファイルの状態が `Online` である必要があります。この状態は、エンドポイントのポーリング パスに基づいています。

### <a name="view-new-profiles-in-the-azure-portal"></a>Azure portal で新しいプロファイルを表示する
`luis-traffic-manager` リソース グループ内のリソースを調べることで、3 つのプロファイルがすべて作成されていることを確認できます。

![luis-traffic-manager Azure リソース グループのスクリーンショット](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>プロファイルの状態が Online であることを確認する
Traffic Manager では、各エンドポイントのパスをポーリングして、オンラインであることを確認します。 オンラインの場合、子プロファイルの状態は `Online` になります。 これは、各プロファイルの **[概要]** に表示されます。

![監視の状態が Online であることを示す Azure Traffic Manager プロファイルの概要のスクリーンショット](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Traffic Manager のポーリングが機能していることを確認する
Traffic Manager のポーリングが機能していることを確認するもう 1 つの方法は、LUIS エンドポイント ログを使用することです。 [LUIS][LUIS] Web サイトのアプリの一覧ページで、アプリケーションのエンドポイント ログをエクスポートします。 Traffic Manager では、2 つのエンドポイントのポーリングが頻繁に実行されるため、数分しか経過していなくても、ログにエントリが存在します。 クエリが `traffic-manager-` で始まるエントリを探してください。

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Traffic Manager からの DNS 応答が機能していることを確認する
DNS 応答で LUIS エンドポイントが返されることを確認するには、DNS クライアント ライブラリを使用して、Traffic Manager 親プロファイルの DNS を要求します。 親プロファイルの DNS 名は `luis-dns-parent.trafficmanager.net` です。

次の Node.js コードでは、親プロファイルを要求し、LUIS エンドポイントを返します。

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

LUIS エンドポイントによる正常な応答を次に示します。

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Traffic Manager 親プロファイルを使用する
エンドポイント全体にわたってトラフィックを管理するには、Traffic Manager の DNS 呼び出しを挿入して、LUIS エンドポイントを検出する必要があります。 この呼び出しはすべての LUIS エンドポイント要求に対して行われます。呼び出しでは、LUIS クライアント アプリケーションのユーザーの地理的な場所をシミュレートする必要があります。 LUIS クライアント アプリケーションとエンドポイント予測のための LUIS への要求の間に DNS 応答コードを追加します。

## <a name="resolving-a-degraded-state"></a>機能低下状態の解決

Traffic Manager の[診断ログ](../../traffic-manager/traffic-manager-diagnostic-logs.md)を有効にて、エンドポイントの状態が機能低下した理由を確認します。

## <a name="clean-up"></a>クリーンアップ
2 つの LUIS エンドポイント キー、3 つの Traffic Manager プロファイル、およびこれらの 5 つのリソースが含まれていたリソース グループを削除します。 これは Azure portal から実行します。 リソースの一覧から 5 つのリソースを削除します。 次に、リソース グループを削除します。

## <a name="next-steps"></a>次のステップ

このトラフィック管理コードを BotFramework ボットに追加する方法を理解するために、BotFramework v4 の[ミドルウェア](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) オプションを確認します。

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md