---
title: Azure Portal でのアクション グループの作成および管理
description: Azure Portal でアクション グループを作成および管理する方法について説明します。
author: dkamstra
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: dukek
ms.openlocfilehash: fb067e603c181482a863dc9fd75556e32a801bc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772350"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal でのアクション グループの作成および管理
アクション グループは、Azure サブスクリプションの所有者によって定義された通知設定のコレクションです。 Azure Monitor および Service Health のアラートでは、アクション グループを使用して、アラートがトリガーされたことをユーザーに通知します。 ユーザーの要件に応じて、さまざまなアラートで同じアクション グループを使用することも、異なるアクション グループを使用することもあります。 

この記事では、Azure Portal でアクション グループを作成および管理する方法について説明します。

各アクションは次のプロパティで構成されます。

* **[種類]** :実行される通知またはアクション。 たとえば、音声通話、SMS、電子メールの送信やさまざまな種類の自動化されたアクションのトリガーなどです。 この記事の後半の種類を参照してください。
* **Name**:アクション グループ内の一意識別子。
* **[詳細]** :*種類* によって異なる対応する詳細。

Azure Resource Manager テンプレートを使用したアクション グループの構成に関する詳細については、「[アクション グループの Resource Manager テンプレート](./action-groups-create-resource-manager-template.md)」を参照してください。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal を使用したアクション グループの作成

1. [Azure portal](https://portal.azure.com) で、 **[モニター]** を検索して選択します。 **[モニター]** ウィンドウでは、すべての監視設定とデータが 1 つのビューにまとめられています。

1. **[アラート]** 、 **[アクションの管理]** の順に選択します。

    ![[アクションの管理] ボタン](./media/action-groups/manage-action-groups.png)
    
1. **[アクショングループの追加]** を選択し、ウィザード エクスペリエンスで関連するフィールドに入力します。

    ![[アクション グループの追加] コマンド](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>基本アクション グループ設定の構成

**[プロジェクトの詳細]** で:

アクション グループが保存される **[サブスクリプション]** と **[リソース グループ]** を選択します。

**[インスタンスの詳細]** で、以下の操作を行います。

1. **[アクション グループ名]** を入力します。

1. **[表示名]** を入力します。 表示名は、通知がこのグループを使用して送信されるときに、完全なアクション グループ名の代わりに使用されます。

      ![[アクション グループの追加] ダイアログ ボックス](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>通知の構成

1. **次へ:通知 >**  ボタンをクリックして、**通知** タブに移動するか、画面の上部にある **通知** タブを選択します。

1. アラートがトリガーされたときに送信する通知の一覧を定義します。 通知ごとに次を指定します。

    a. **通知の種類**:送信する通知の種類を選択します。 使用可能なオプションは次のとおりです。
      * Azure Resource Manager ロールへの電子メール送信 - 特定のサブスクリプションレベルの ARM ロールに割り当てられているユーザーに電子メールを送信します。
      * 電子メール/SMS/プッシュ/音声 - 特定の受信者にこれらの通知の種類を送信します。
    
    b. **Name**:通知の一意の名前を入力します。

    c. **[詳細]** :選択した通知の種類に基づいて、電子メールアドレス、電話番号などを入力します。
    
    d. **[共通アラート スキーマ]** : [共通アラート スキーマ](./alerts-common-schema.md)を有効にすることを選択できます。これは、Azure Monitor のすべてのアラート サービスにわたって 1 つの拡張可能で、かつ統合されたアラート ペイロードを持つ利点を提供します。

    ![[通知] タブ](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>アクションを構成する

1. **次へ:アクション >**  ボタンをクリックして **アクション** タブに移動するか、画面の上部にある **アクション** タブを選択します。

1. アラートがトリガーされたときにトリガーするアクションの一覧を定義します。 アクションごとに次の内容を指定します。

    a. **アクションの種類**: Automation Runbook、Azure Function、ITSM、Logic App、Secure Webhook、Webhook を選択します。
    
    b. **Name**:アクションの一意の名前を入力します。

    c. **[詳細]** :アクションの種類に基づいて、Webhook の URI、Azure アプリ、ITSM 接続、または Automation Runbook を入力します。 ITSM アクションの場合は、さらに ITSM ツールで必要な **[作業項目]** および他のフィールドを指定します。
    
    d. **[共通アラート スキーマ]** : [共通アラート スキーマ](./alerts-common-schema.md)を有効にすることを選択できます。これは、Azure Monitor のすべてのアラート サービスにわたって 1 つの拡張可能で、かつ統合されたアラート ペイロードを持つ利点を提供します。
    
    ![[アクション] タブ](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>アクション グループの作成

1. 希望に応じて、 **[タグ]** 設定を調べてもかまいません。 これは、キー/値のペアをカテゴリ化のアクション グループに関連付けることができ、任意の Azure リソースで利用できる機能です。

    ![[タグ] タブ](./media/action-groups/action-group-4-tags.png)
    
1. **[確認と作成]** をクリックして設定を確認します。 これにより、入力をすばやく検証して、すべての必須フィールドが選択されていることを確認します。 問題がある場合は、ここで報告されます。 設定を確認したら、 **[作成]** をクリックして、アクション グループをプロビジョニングします。
    
    ![[確認と作成] タブ](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> 電子メールまたは SMS でユーザーに通知するようアクションを構成すると、ユーザーは自分がアクション グループに追加されたことを示す確認メッセージを受け取ります。

## <a name="manage-your-action-groups"></a>アクション グループの管理

アクション グループの作成後、 **[モニター]** ウィンドウの **[アラート]** ランディング ページで **[アクションの管理]** を選択すると、 **[アクション グループ]** を表示できます。 次の操作を行うために管理するアクション グループを選択します。

* アクションの追加、編集、または削除。
* アクション グループの削除。

## <a name="action-specific-information"></a>アクション固有の情報

> [!NOTE]
> 次の各項目の数値の制限については、[監視のためのサブスクリプション サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits)に関するセクションを参照してください。  

### <a name="automation-runbook"></a>Automation Runbook
Runbook ペイロードの制限については、[Azure サブスクリプション サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md)に関するページを参照してください。

アクション グループには、限られた数の Runbook アクションを保持できます。 

### <a name="azure-app-push-notifications"></a>Azure アプリのプッシュ通知
Azure mobile app の構成時にアカウント ID として使用するメール アドレスを指定して、[Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/) へのプッシュ通知を有効にします。

アクション グループには、限られた数の Azure アプリのアクションを保持できます。

### <a name="email"></a>Email
電子メールは、次の電子メール アドレスから送信されます。 電子メールのフィルタリングが適切に構成されていることを確認してください
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

アクション グループには、電子メールに関する限られた数のアクションを持つことができます。 [レート制限情報](./alerts-rate-limiting.md)の記事を参照してください。

### <a name="email-azure-resource-manager-role"></a>電子メールの Azure Resource Manager のロール
サブスクリプションのロールのメンバーに電子メールを送信します。 電子メールはこのロールの **Azure AD ユーザー** メンバーにのみ送信されます。 Azure AD グループまたはサービス プリンシパルに電子メールが送信されることはありません。

通知電子メールは、*標準の電子メール* アドレスにのみ送信されます。

*プライマリ電子メール* で通知を受信していない場合は、次の手順を試してください。

1. Azure portal で *[Active Directory]* に移動します。
2. [すべてのユーザー] (左側のウィンドウ) をクリックすると、ユーザーの一覧が表示されます (右側のウィンドウ)。
3. *プライマリ電子メール* 情報を表示する対象のユーザーを選択します。

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="ユーザー プロファイルを表示する方法の例。" border="true":::

4. [連絡先情報] の下の [ユーザー プロファイル] で、[電子メール] タブが空白の場合は上部にある *[編集]* ボタンをクリックし、お使いの *プライマリ電子メール* を追加して、上部にある *[保存]* ボタンをクリックします。

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="プライマリ電子メールを追加する方法の例。" border="true":::

アクション グループには、電子メールに関する限られた数のアクションを持つことができます。 [レート制限情報](./alerts-rate-limiting.md)の記事を参照してください。

### <a name="function"></a>機能
[Azure Functions](../../azure-functions/functions-get-started.md) で既存の HTTP トリガー エンドポイントを呼び出します。 要求を処理するには、エンドポイントで HTTP POST 動詞を処理する必要があります。

アクション グループには、限られた数の Function アクションを保持できます。

### <a name="itsm"></a>ITSM
ITSM アクションには ITSM 接続が必要です。 [ITSM 接続](./itsmc-overview.md)の作成方法を確認してください。

アクション グループには、限られた数の ITSM アクションを保持できます。 

### <a name="logic-app"></a>ロジック アプリ
アクション グループには、限られた数のロジック アプリのアクションを保持できます。

### <a name="secure-webhook"></a>Secure Webhook

> [!NOTE]
> Webhook アクションを使用するには、ターゲット Webhook エンドポイントにおいてアラートの細部が正常に機能する必要がないか、POST 操作の一環として与えられるアラート コンテキスト情報を解析できることが求められます。 Webhook エンドポイント自体でアラート コンテキスト情報を処理できない場合、アラート コンテキスト情報のカスタム操作のための [Logic App アクション](./action-groups-logic-app.md)などのソリューションを利用し、Webhook で求められるデータ形式に一致させることができます。
> セキュリティに違反しないようにするために、ユーザーは Webhook サービス プリンシパルの **所有者** である必要があります。 Azure のすべてのお客様は、所有者を確認せずに、ポータルを通じてすべてのオブジェクト ID にアクセスできるため、すべてのユーザーが、セキュリティに違反する Azure Monitor アラート通知用の独自のアクション グループに対して、セキュリティで保護された Webhook を追加できます。

アクション グループの Webhook アクションを使用すると、Azure Active Directory を利用して、アクション グループと、保護された Web API (Webhook エンドポイント) との間の接続をセキュリティで保護することができます。 この機能を利用するための全体的なワークフローを次に示します。 Azure AD アプリケーションとサービス プリンシパルの概要については、「[Microsoft ID プラットフォーム (v2.0) の概要](../../active-directory/develop/v2-overview.md)」を参照してください。

1. 保護された Web API 用の Azure AD アプリケーションを作成します。 「[保護された Web API: アプリの登録](../../active-directory/develop/scenario-protected-web-api-app-registration.md)」の手順に従う必要があります。
    - [デーモン アプリで呼び出される](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)よう、保護された API を構成します。
    
2. アクション グループで Azure AD アプリケーションを使用できるようにします。

    > [!NOTE]
    > このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](../../active-directory/roles/permissions-reference.md#all-roles)のメンバーである必要があります。
    
    - Azure AD テナント ID を使用するように、PowerShell スクリプトの Connect-AzureAD 呼び出しを変更します。
    - Azure AD アプリケーションのオブジェクト ID を使用するように、PowerShell スクリプトの変数 $myAzureADApplicationObjectId を変更します。
    - 変更したスクリプトを実行します。
    
3. アクション グループの Secure Webhook アクションを構成します。
    - スクリプトから値 $myApp.ObjectId をコピーし、Webhook アクション定義の [アプリケーション オブジェクト ID] フィールドに入力します。
    
    ![Secure Webhook アクション](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Secure Webhook PowerShell スクリプト

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
その他の重要な情報については、[レート制限情報](./alerts-rate-limiting.md)と [SMS アラート動作](./alerts-sms-behavior.md)に関する各ページを参照してください。 

アクション グループには、限られた数の SMS アクションを保持できます。

> [!NOTE]
> Azure portal アクション グループのユーザー インターフェイスで国/地域コードを選択できない場合、SMS はお住まいの国/地域ではサポートされていません。  国/地域コードが利用できない場合は、[ユーザーの声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)でお住まいの国/地域を追加するように投票できます。 当面の回避策としては、お住まいの国/地域でサポートされているサードパーティの SMS プロバイダーに対して、アクション グループで Webhook を呼び出します。  

サポートされている国/地域における価格については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページを参照してください。

**SMS 通知がサポートされている国の一覧**

| 国番号 | 国名 |
|:---|:---|
| 61 | オーストラリア |
| 43 | オーストリア |
| 32 | ベルギー |
| 55 | ブラジル |
| 1 |カナダ |
| 56 | チリ |
| 86 | 中国 |
| 420 | チェコ共和国 |
| 45 | デンマーク |
| 372 | エストニア |
| 358 | フィンランド |
| 33 | フランス |
| 49 | ドイツ |
| 852 | 香港特別行政区 |
| 91 | インド |
| 353 | アイルランド |
| 972 | イスラエル |
| 39 | イタリア |
| 81 | 日本 |
| 352 | ルクセンブルク |
| 60 | マレーシア |
| 52 | メキシコ |
| 31 | オランダ |
| 64 | ニュージーランド |
| 47 | ノルウェー |
| 351 | ポルトガル |
| 1 | プエルトリコ |
| 40 | ルーマニア |
| 65 | シンガポール |
| 27 | 南アフリカ |
| 82 | 韓国 |
| 34 | スペイン |
| 41 | スイス |
| 886 | 台湾 |
| 44 | イギリス |
| 1 | アメリカ合衆国 |

### <a name="voice"></a>音声
その他の重要な動作については、[レート制限情報](./alerts-rate-limiting.md)に関する記事を参照してください。

アクション グループには、限られた数の音声アクションを保持できます。

> [!NOTE]
> Azure portal アクション グループのユーザー インターフェイスで国/地域コードを選択できない場合、音声通話はお住まいの国/地域ではサポートされていません。 国/地域コードが利用できない場合は、[ユーザーの声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)でお住まいの国/地域を追加するように投票できます。  当面の回避策としては、お住まいの国/地域でサポートされているサードパーティの音声通話プロバイダーに対して、アクション グループで Webhook を呼び出します。  
> 音声通知について Azure portal アクション グループで現在サポートされている国番号は +1 (米国) のみです。 

サポートされている国/地域における価格については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページを参照してください。

### <a name="webhook"></a>Webhook

> [!NOTE]
> Webhook アクションを使用するには、ターゲット Webhook エンドポイントにおいてアラートの細部が正常に機能する必要がないか、POST 操作の一環として与えられるアラート コンテキスト情報を解析できることが求められます。 Webhook エンドポイント自体でアラート コンテキスト情報を処理できない場合、アラート コンテキスト情報のカスタム操作のための [Logic App アクション](./action-groups-logic-app.md)などのソリューションを利用し、Webhook で求められるデータ形式に一致させることができます。

Webhook は、次のルールを使用して再処理されます。
- Webhook 呼び出しは、最大 3 回試行されます。
- タイムアウト期間内に応答が受信されない場合、または次の HTTP 状態コードのいずれかが返された場合、呼び出しは再試行されます: 408、429、503、または 504。
- 最初の呼び出しでは、応答が返るまで 10 秒間待機します。
- 2 回目と 3 回目の試行では、応答が返るまで 30 秒間待機します。
- Webhook の呼び出しを 3 回試行して失敗した後、アクション グループで 15 分間エンドポイントが呼び出されることはありません。

ソース IP アドレス範囲の[アクション グループ IP アドレス](../app/ip-addresses.md)について参照してください。


## <a name="next-steps"></a>次のステップ
* 詳細については、「[SMS アラート動作](./alerts-sms-behavior.md)」を参照してください。  
* [アクティビティ ログ アラートに対する webhook スキーマについて理解](./activity-log-alerts-webhook.md)します。  
* [ITSM Connector](./itsmc-overview.md) について学習します。
* アラートの[レート制限](./alerts-rate-limiting.md)について学習します。
* [アクティビティ ログ アラートの概要](./alerts-overview.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](../../service-health/alerts-activity-log-service-notifications-portal.md)する方法について学習します。
