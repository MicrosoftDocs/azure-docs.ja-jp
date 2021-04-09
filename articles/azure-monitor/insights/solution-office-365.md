---
title: Azure の Office 365 管理ソリューション
description: この記事では、Azure での Office 365 ソリューションの構成と使用について詳しく説明します。  Azure Monitor で作成された Office 365 レコードの詳細な説明が含まれています。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: be5409f7130f0e79b77871a2657609e8ddb76e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728639"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure の Office 365 管理ソリューション (プレビュー)

![Office 365 のロゴ](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>ソリューションの更新
> このソリューションは、[Azure Sentinel](../../sentinel/overview.md) の [Office 365](../../sentinel/connect-office-365.md) 一般公開ソリューション、および [Azure AD レポートおよび監視ソリューション](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)に置き換えられました。 これらのソリューションにより、以前の Azure Monitor Office 365 ソリューションの更新バージョンが提供され、構成操作が向上しました。 2020 年 10 月 31 日までは、既存のソリューションを引き続きご利用いただけます。
> 
> Azure Sentinel は、ログを取り込み、検出、調査、検出、機械学習による分析情報を含む追加の SIEM 機能を提供するクラウド ネイティブのセキュリティ情報およびイベント管理ソリューションです。 Azure Sentinel を使用することで、Office 365 SharePoint アクティビティと Exchange 管理ログの取り込みが提供されるようになりました。
> 
> Azure AD のレポートは、サインイン イベント、監査イベント、ディレクトリへの変更など、環境内の Azure AD アクティビティのさらに包括的なビューとログを提供します。 Azure AD ログに接続するには [Azure Sentinel Azure ADコネクタ](../../sentinel/connect-azure-active-directory.md)を使用するか、[Azure Monitor で Azure AD ログ統合](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)を構成します。 
>
> Azure AD ログのコレクションは Azure Monitor 価格が適用されます。  詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。
>
> Azure Sentinel Office 365 ソリューションを使用するには、次の手順に従います。
> 1. Azure Sentinel で Office 365 コネクタを使用すると、ワークスペースの料金に影響します。 詳細については、「[Azure Sentinel の価格](https://azure.microsoft.com/pricing/details/azure-sentinel/)」を参照してください。
> 2. Azure Monitor Office 365 ソリューションを既に使用している場合は、[次のアンインストールに関するセクション](#uninstall)のスクリプトを使用して、まずアンインストールする必要があります。
> 3. ワークスペースで [Azure Sentinel ソリューションを有効にします](../../sentinel/quickstart-onboard.md)。
> 4. Azure Sentinel の **[データ コネクタ]** ページにアクセスし、**Office 365** コネクタを有効にします。
>
> ## <a name="frequently-asked-questions"></a>よく寄せられる質問
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>Q:現時点から 10 月 31 日までの間に、Office 365 Azure Monitor ソリューションをオンボードすることはできますか。
> いいえ、Azure Monitor Office 365 ソリューションのオンボード スクリプトは使用できません。 このソリューションは 10 月 31 日に削除されます。
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>Q:テーブルとスキーマは変更されますか。
> **OfficeActivity** テーブル名とスキーマは、現在のソリューションと同じままです。 新しいソリューションでは、Azure AD データを参照するクエリを除き、同じクエリを使用し続けることができます。
> 
> 新しい [Azure AD レポートおよび監視ソリューション](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)ログは、**OfficeActivity** ではなく、[SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) と [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) テーブルに取り込まれます。 詳細については、[Azure AD ログの分析方法](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)に関する記事を参照してください。これは、Azure Sentinel と Azure Monitor ユーザーにも関連しています。
> 
> **OfficeActivity** から **SigninLogs** にクエリを変換するためのサンプルを次に示します。
> 
> **ユーザーがサインインに失敗した場合のクエリ:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Azure AD の操作を表示:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>Q:Azure Sentinel をオンボードするにはどうすればよいですか。
> Azure Sentinel は、新規または既存の Log Analytics ワークスペースで有効にできるソリューションです。 詳細については、[Azure Sentinel のオンボードに関するドキュメント](../../sentinel/quickstart-onboard.md)を参照してください。
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>Q:Azure AD ログを接続するために Azure Sentinel は必要ですか。
> [Azure AD ログと Azure Monitor の統合](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)を構成できます。これは、Azure Sentinel ソリューションに関連付けられていません。 Azure Sentinel は、ネイティブ コネクタと、すぐに使用できる Azure AD ログ用のコンテンツを提供します。 詳細については、組み込みのセキュリティ指向のコンテンツに関する次の質問を参照してください。
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>Q:Azure Sentinel と Azure Monitor から Azure AD ログを接続する場合の違いは何ですか。
> Azure Sentinel と Azure Monitor は、同じ [Azure AD レポートおよび監視ソリューション](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)に基づいて Azure AD ログに接続します。 Azure Sentinel では、同じデータを接続し、監視情報をワンクリック提供するでネイティブ コネクタが使用できます。
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>Q:新しい Azure AD レポートおよび監視テーブルに移動する場合は、どのように変更する必要がありますか。
> 警告、ダッシュボード、および Office 365 Azure AD データを使用して作成したコンテンツを含む Azure AD データを使用するすべてのクエリを、新しいテーブルを使用して再作成する必要があります。
>
> Azure Sentinel と Azure AD には、Azure AD レポートおよび監視ソリューションに移行するときに使用できる組み込みコンテンツが用意されています。 詳細については、組み込みのセキュリティ指向のコンテンツに関する次の質問、および 「[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md)」を参照してください。 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>Q:組み込みの Azure Sentinel のセキュリティ指向コンテンツを使用するにはどうすればよいですか。
> Azure Sentinel は、Office 365 および Azure AD ログに基づいた、組み込みのセキュリティ指向のダッシュボード、カスタム アラート クエリ、検索クエリ、調査、および自動応答機能を提供します。 詳細については、Azure Sentinel GitHub とチュートリアルを参照してください。
>
> - [難しい設定なしで脅威を検出する](../../sentinel/tutorial-detect-threats-built-in.md)
> - [疑わしい脅威を検出するカスタム分析ルールを作成する](../../sentinel/tutorial-detect-threats-custom.md)
> - [データを監視する](../../sentinel/tutorial-monitor-your-data.md)
> - [Azure Sentinel でインシデントを調査します](../../sentinel/tutorial-investigate-cases.md)
> - [Azure Sentinel で脅威への自動対応を設定します](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure Sentinel GitHub コミュニティ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>Q:Azure Sentinel は、ソリューションの一部として追加のコネクタを提供しますか。
> はい。[Azure Sentinel データ ソースの接続](../../sentinel/connect-data-sources.md)に関する記事を参照してください。
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>Q:10 月 31 日には何が行われるのですか。 事前にオフボードする必要はありますか。
> 
> - **Office365** ソリューションからデータを受信することができなくなります。 ソリューションはワークスペースから削除され、Marketplace で入手できなくなります。
> - Azure Sentinel のお客様については、Log Analytics ワークスペース ソリューション **Office365** が Azure Sentinel **SecurityInsights** ソリューションに含まれるようになります。
> - 10 月 31 日までに手動でソリューションをオフボードしない場合、データは自動的に切断され、**OfficeActivity** テーブルは削除されます。 このような場合でも、以下で説明するように、Azure Sentinel で Office 365 コネクタを有効にすると、テーブルを復元できます。
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>Q:データは新しいソリューションに転送されますか。
> はい。 **Office 365** ソリューションをワークスペースから削除すると、スキーマが削除されるため、そのデータは一時的に使用できなくなります。 新しい **Office 365** コネクタを Azure Sentinel で有効にすると、スキーマがワークスペースに復元され、既に収集されたデータが使用できるようになります。 
 

Office 365 管理ソリューションでは、Azure Monitor で Office 365 環境を監視できます。

- Office 365 アカウント上でのユーザー アクティビティを監視し、使用パターンを分析したり、行動傾向を識別したりします。 たとえば、組織の外で共有されるファイルや、最も人気のある SharePoint サイトといった特定の使用シナリオを抽出することができます。
- 管理者のアクティビティを監視し、構成変更や高権限操作を追跡します。
- 不必要なユーザーの行動を検出および調査します。これは、組織のニーズに合わせてカスタマイズできます。
- 監査とコンプライアンスを実証します。 たとえば、機密ファイルに対するファイル アクセス操作を監視でき、これは監査とコンプライアンスのプロセスに役立ちます。
- 組織の Office 365 アクティビティ データに対して[ログ クエリ](../logs/log-query-overview.md)を使用し、運用上のトラブルシューティングを実行します。


## <a name="uninstall"></a>アンインストール

[管理ソリューションを削除する](solutions.md#remove-a-monitoring-solution)のプロセスを使用して Office 365 管理ソリューションを削除できます。 ただしこれによって、Office 365 から Azure Monitor に収集されているデータは停止されません。 Office 365 からのサブスクリプションを解除し、データの収集を停止するには、以下の手順に従います。

1. 次のスクリプトを *office365_unsubscribe.ps1* として保存します。

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. 次のコマンドを使用してこのスクリプトを実行します。

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    例:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

資格情報を求めるメッセージが表示されます。 Log Analytics ワークスペースの資格情報を入力します。

## <a name="data-collection"></a>データ コレクション

最初のデータ収集には数時間かかる場合があります。 収集が開始されると、レコードが作成されるたびに、Office 365 は [webhook 通知](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications)と詳細なデータを Azure Monitor に送信します。 このレコードは、受信した後、数分以内に Azure Monitor で使用できます。

## <a name="using-the-solution"></a>ソリューションの使用

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Log Analytics ワークスペースに Office 365 ソリューションを追加すると、ダッシュボードに **[Office 365]** タイルが追加されます。 このタイルには、ご利用の環境におけるコンピューターの数と更新プログラムの対応状態が数字とグラフで表示されます。<br><br>
![Office 365 の概要タイル](media/solution-office-365/tile.png)  

**[Office 365]** タイルをクリックして **[Office 365]** ダッシュボードを開きます。

![Office 365 ダッシュボード](media/solution-office-365/dashboard.png)  

ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定のスコープと時間範囲について、その列の基準に該当するアラート数の上位 10 件が表示されます。 ログ検索を実行してアラート全件を取得するには、列の一番下にある [See all] \(すべて表示) をクリックするか、列ヘッダーをクリックします。

| 列 | 説明 |
|:--|:--|
| 操作 | すべての監視対象 Office 365 サブスクリプションから、アクティブ ユーザーに関する情報を提供します。 時間の経過と共に発生するアクティビティの数を見ることもできます。
| Exchange | Add-Mailbox Permission、または Set-Mailbox などの Exchange Server アクティビティの内訳を示します。 |
| SharePoint | SharePoint ドキュメントに対してユーザーが実行する最上位のアクティビティを示します。 このタイルからドリル ダウンすると、ターゲット ドキュメントやこのアクティビティの場所など、これらのアクティビティの詳細が検索ページに表示されます。 たとえば、File Accessed イベントの場合、アクセスされているドキュメント、それと関連付けられたアカウント名、および IP アドレスを見ることができます。 |
| Azure Active Directory | ユーザー パスワードのリセットやログイン試行など、最上位のユーザー アクティビティが含まれます。 ドリルダウンすると、結果の状態など、これらのアクティビティの詳細を見ることができます。 これは主に、Azure Active Directory 上の不審なアクティビティを監視する場合に便利です。 |




## <a name="azure-monitor-log-records"></a>Azure Monitor のログ レコード

Azure Monitor の Log Analytics ワークスペースで Office 365 ソリューションによって作成されたすべてのレコードは、**型** が **OfficeActivity** です。  **OfficeWorkload** プロパティは、レコードが参照する Office 365 サービス (Exchange、AzureActiveDirectory、SharePoint、または OneDrive) を決定します。  **RecordType** プロパティは操作の種類を指定します。  プロパティは操作の種類ごとに異なり、次の表に示しています。

### <a name="common-properties"></a>共通プロパティ

次のプロパティは、Office 365 のすべてのレコードに共通です。

| プロパティ | 説明 |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | アクティビティが記録されたときに使用されたデバイスの IP アドレス。 IP アドレスは IPv4 または IPv6 アドレスの形式で表示されます。 |
| OfficeWorkload | レコードが参照する Office 365 サービス。<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| 操作 | ユーザーまたは管理者アクティビティの名前。  |
| OrganizationId | 組織の Office 365 テナントの GUID。 どの Office 365 サービスで発生するかにかかわらず、この値は組織に対して常に同じになります。 |
| RecordType | 実行する操作の種類。 |
| ResultStatus | (Operation プロパティで指定された) アクションが正常に終了したかどうかを示します。 値は Succeeded、PartiallySucceeded、Failed のいずれかです。 Exchange 管理者アクティビティの場合、値は True または False です。 |
| UserId | レコードがログに記録される結果になったアクションを実行したユーザーの UPN (ユーザー プリンシパル名)。たとえば、my_name@my_domain_name。 (SHAREPOINT\system や NTAUTHORITY\SYSTEM などの) システム アカウントによって実行されるアクティビティのレコードも含まれることに注意してください。 | 
| UserKey | UserId プロパティで識別されるユーザーの代替 ID。  たとえば、SharePoint、OneDrive for Business、および Exchange でユーザーによって実行されたイベントの Passport 一意識別子 (PUID) が、このプロパティの値になります。 このプロパティは、他のサービスで発生するイベントや、システム アカウントによって実行されるイベントの UserID プロパティと同じ値を指定する場合もあります。|
| UserType | 操作を実行したユーザーの種類。<br><br>[Admin]<br>Application<br>DcAdmin<br>通常<br>予約済み<br>ServicePrincipal<br>システム |


### <a name="azure-active-directory-base"></a>Azure Active Directory ベース

次のプロパティは、Azure Active Directory のすべてのレコードに共通です。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD イベントの種類。 |
| ExtendedProperties | Azure AD イベントの拡張プロパティ。 |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory アカウント ログオン

これらのレコードは、Active Directory のユーザーがログオンを試みたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | アカウント ログイン イベントをトリガーするアプリケーション (Office 15 など)。 |
| `Client` | アカウント ログイン イベントで使用されたクライアント デバイス、デバイス OS、およびデバイス ブラウザーの詳細。 |
| `LoginStatus` | このプロパティは OrgIdLogon.LoginStatus に直接由来します。 アルゴリズムを変えることにより、さまざまな興味深いログオン失敗のマッピングを実行できます。 |
| `UserDomain` | テナント ID 情報 (TII)。 | 


### <a name="azure-active-directory"></a>Azure Active Directory

これらのレコードは、変更または追加が Azure Active Directory オブジェクトに行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | (Operation プロパティによって識別される) アクションの実行対象だったユーザー。 |
| Actor | アクションを実行したユーザーまたはサービス プリンシパル。 |
| ActorContextId | アクターが所属する組織の GUID。 |
| ActorIpAddress | アクターの IP アドレス。IPv4 または IPv6 アドレスの形式。 |
| InterSystemsId | Office 365 サービス内のコンポーネント間でアクションを追跡する GUID。 |
| IntraSystemId |     アクションを追跡するために Azure Active Directory によって生成される GUID。 |
| SupportTicketId | "代理操作" の状況におけるアクションのカスタマー サポート チケット ID。 |
| TargetContextId | ターゲットのユーザーが所属する組織の GUID。 |


### <a name="data-center-security"></a>データ センター セキュリティ

これらのレコードは、データ センター セキュリティの監査データから作成されます。  

| プロパティ | 説明 |
|:--- |:--- |
| EffectiveOrganization | 昇格/コマンドレットのターゲットだったテナントの名前。 |
| ElevationApprovedTime | 昇格が承認されたときのタイムスタンプ。 |
| ElevationApprover | Microsoft マネージャーの名前。 |
| ElevationDuration | 昇格がアクティブだった期間。 |
| ElevationRequestId |     昇格要求の一意識別子。 |
| ElevationRole | 昇格が要求されたロール。 |
| ElevationTime | 昇格の開始時刻。 |
| Start_Time | コマンドレット実行の開始時刻。 |


### <a name="exchange-admin"></a>Exchange 管理者

これらのレコードは、Exchange 構成が変更されたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     組織内のユーザー、Microsoft のデータセンター担当者またはデータセンター サービス アカウント、委任された管理者のいずれによってコマンドレットが実行されたかを指定します。 False の値は、組織内の人物によってコマンドレットが実行されたことを示します。 True の値は、データセンター担当者、データセンター サービス アカウント、または委任管理者によってコマンドレットが実行されたことを示します。 |
| ModifiedObjectResolvedName |     コマンドレットによって変更されたオブジェクトのユーザー フレンドリ名。 コマンドレットがオブジェクトを変更する場合にのみ記録されます。 |
| OrganizationName | テナントの名前。 |
| OriginatingServer | コマンドレットの実行元だったサーバーの名前。 |
| パラメーター | Operations プロパティで識別されるコマンドレットと共に使用されたすべてのパラメーターの名前と値。 |


### <a name="exchange-mailbox"></a>Exchange メールボックス

これらのレコードは、変更または追加が Exchange メールボックスに行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | ブラウザーのバージョン、Outlook のバージョン、モバイル デバイス情報など、操作を実行するために使用された電子メール クライアントに関する情報。 |
| Client_IPAddress | 操作が記録されたときに使用されたデバイスの IP アドレス。 IP アドレスは IPv4 または IPv6 アドレスの形式で表示されます。 |
| ClientMachineName | Outlook クライアントをホストするマシン名。 |
| ClientProcessName | メールボックスへのアクセスに使用された電子メール クライアント。 |
| ClientVersion | 電子メール クライアントのバージョン。 |
| InternalLogonType | 内部使用のために予約されています。 |
| Logon_Type | メールボックスにアクセスし、記録された操作を実行したユーザーの種類を示します。 |
| LogonUserDisplayName |     操作を実行したユーザーのユーザー フレンドリ名。 |
| LogonUserSid | 操作を実行したユーザーの SID。 |
| MailboxGuid | アクセスされたメールボックスの Exchange GUID。 |
| MailboxOwnerMasterAccountSid | メールボックス所有者アカウントのマスター アカウント SID。 |
| MailboxOwnerSid | メールボックス所有者の SID。 |
| MailboxOwnerUPN | アクセスされたメールボックスを所有する人物の電子メール アドレス。 |


### <a name="exchange-mailbox-audit"></a>Exchange メールボックス監査

これらのレコードは、メールボックス監査エントリが作成されるときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | 操作が実行された対象の項目を表します。 | 
| SendAsUserMailboxGuid | その名前で電子メールを送信するためにアクセスされたメールボックスの Exchange GUID。 |
| SendAsUserSmtp | 偽装されているユーザーの SMTP アドレス。 |
| SendonBehalfOfUserMailboxGuid | 代理でメールを送信するためにアクセスされたメールボックスの Exchange GUID。 |
| SendOnBehalfOfUserSmtp | その代理でメールが送信されるユーザーの SMTP アドレス。 |


### <a name="exchange-mailbox-audit-group"></a>Exchange メールボックス監査グループ

これらのレコードは、変更または追加が Exchange グループに行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | グループ内の各項目に関する情報。 |
| CrossMailboxOperations | 複数のメールボックスが操作に関係したかどうかを示します。 |
| DestMailboxId | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックス GUID を指定します。 |
| DestMailboxOwnerMasterAccountSid | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックス所有者のマスター アカウント SID の SID を指定します。 |
| DestMailboxOwnerSid | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックスの SID を指定します。 |
| DestMailboxOwnerUPN | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックスの所有者の UPN を指定します。 |
| DestFolder | 移動などの操作の宛先フォルダー。 |
| Folder | 項目のグループが位置しているフォルダー。 |
| Folders |     操作に関係したソース フォルダーに関する情報。たとえば、フォルダーが選択後に削除されるかどうか。 |


### <a name="sharepoint-base"></a>SharePoint ベース

これらのプロパティは、SharePoint のすべてのレコードに共通です。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | SharePoint で発生したイベントを識別します。 値は SharePoint または ObjectModel です。 |
| ItemType | アクセスまたは変更されたオブジェクトの種類。 オブジェクトの種類の詳細については、ItemType の表を参照してください。 |
| MachineDomainInfo | デバイス同期操作に関する情報。 この情報は、要求に存在する場合にのみ報告されます。 |
| MachineId |     デバイス同期操作に関する情報。 この情報は、要求に存在する場合にのみ報告されます。 |
| Site_ | ユーザーがアクセスするファイルまたはフォルダーがあるサイトの GUID。 |
| Source_Name | 監査対象の操作をトリガーしたエンティティ。 値は SharePoint または ObjectModel です。 |
| UserAgent | ユーザーのクライアントまたはブラウザーに関する情報。 この情報は、クライアントまたはブラウザーによって提供されます。 |


### <a name="sharepoint-schema"></a>SharePoint スキーマ

これらのレコードは、SharePoint の構成変更が行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | カスタム イベントに関する省略可能文字列。 |
| Event_Data |     カスタム イベントに関する省略可能ペイロード。 |
| ModifiedProperties | サイトまたはサイト コレクション管理者グループのメンバーとしてユーザーを追加するなどの管理者イベントに含まれるプロパティ。 プロパティには、変更されたプロパティの名前 (サイト管理者グループなど)、変更されたプロパティの新しい値 (サイト管理者として追加されたユーザーなど)、変更されたオブジェクトの以前の値が含まれます。 |


### <a name="sharepoint-file-operations"></a>SharePoint ファイル操作

これらのレコードは、SharePoint でのファイル操作に応答して作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | コピーまたは移動されるファイルのファイル拡張子。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| DestinationFileName | コピーまたは移動されるファイルの名前。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| DestinationRelativeUrl | ファイルのコピーまたは移動先フォルダーの URL。 SiteURL、DestinationRelativeURL、および DestinationFileName パラメーターの値の組み合わせは、(コピーされたファイルの完全パス名である) ObjectID プロパティの値と同じです。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| SharingType | リソースの共有相手だったユーザーに割り当てられていた共有アクセス許可の種類。 このユーザーは UserSharedWith パラメーターによって識別されます。 |
| Site_Url | ユーザーがアクセスするファイルまたはフォルダーがあるサイトの URL。 |
| SourceFileExtension | ユーザーがアクセスしたファイルのファイル拡張子。 アクセスしたオブジェクトがフォルダーの場合、このプロパティは空です。 |
| SourceFileName |     ユーザーがアクセスしたファイルまたはフォルダーの名前。 |
| SourceRelativeUrl | ユーザーがアクセスするファイルが含まれているフォルダーの URL。 SiteURL、SourceRelativeURL、および SourceFileName パラメーターの値の組み合わせは、(ユーザーがアクセスするファイルの完全パス名である) ObjectID プロパティの値と同じです。 |
| UserSharedWith |     リソースの共有相手だったユーザー。 |




## <a name="sample-log-queries"></a>サンプル ログ クエリ

次の表は、このソリューションによって収集された更新レコードを探すログ クエリの例です。

| クエリ | 説明 |
| --- | --- |
|Office 365 サブスクリプションでのすべての操作のカウント |OfficeActivity &#124; summarize count() by Operation |
|SharePoint サイトの使用率|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl \| sort by Count asc|
|ユーザーの種類別のファイル アクセス操作 | OfficeActivity &#124; summarize count() by UserType |
|Exchange 上の外部アクションを監視する|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>次のステップ

* [Azure Monitor でログ クエリ](../logs/log-query-overview.md)を使用して、詳細な更新プログラムのデータを表示します。
* [独自のダッシュボードを作成](../visualize/tutorial-logs-dashboards.md)して、お気に入りの Office 365 検索クエリを表示します。
* [アラートを作成](../alerts/alerts-overview.md)して、重要な Office 365 アクティビティがあらかじめ通知されるようにします。  
