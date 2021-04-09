---
title: Azure Automation を使用した Azure Analysis Services モデルの更新 | Microsoft Docs
description: この記事では、Azure Automation を使用して Azure Analysis Services のモデルの更新をコーディングする方法について説明します。
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491249"
---
# <a name="refresh-with-azure-automation"></a>Azure Automation を使用した更新

Azure Automation および PowerShell Runbook を使用して、Azure Analysis 表形式モデルに対する自動データ更新操作を行うことができます。  

この記事の例では、[SqlServer PowerShell モジュール](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true)を使用します。 モデルの更新方法を示す PowerShell Runbook のサンプルは、この記事の後半で提供されます。  

## <a name="authentication"></a>認証

すべての呼び出しを、有効な Azure Active Directory (OAuth 2) トークンで認証する必要があります。  この記事の例では、サービス プリンシパル (SPN) を使用して Azure Analysis Services を認証します。 詳細については、[Azure portal を使用したサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> 次の例では、Azure Analysis Services ファイアウォールが無効になっていることを前提としています。 ファイアウォールが有効になっている場合は、要求イニシエーターのパブリック IP アドレスがファイアウォール規則に含まれている必要があります。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>PowerShell ギャラリーから SqlServer モジュールをインストールします。

1. Azure Automation アカウントで、 **[モジュール]** をクリックし、 **[Browse gallery]\(ギャラリーの閲覧\)** をクリックします。

2. 検索バーで「**SqlServer**」を検索します。

    ![モジュールを検索する](./media/analysis-services-refresh-azure-automation/1.png)

3. SqlServer を選択し、 **[インポート]** をクリックします。
 
    ![モジュールをインポートする](./media/analysis-services-refresh-azure-automation/2.png)

4. **[OK]** をクリックします。
 
### <a name="create-a-service-principal-spn"></a>サービス プリンシパル (SPN) を作成する

サービス プリンシパルの作成の詳細については、[Azure portal を使用したサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services でアクセス許可を構成する
 
作成するサービス プリンシパルには、サーバーでのサーバー管理者のアクセス許可が必要です。 詳細については、「[サーバー管理者ロールへのサービス プリンシパルの追加](analysis-services-addservprinc-admins.md)」を参照してください。

## <a name="design-the-azure-automation-runbook"></a>Azure Automation Runbook を設計する

1. Automation アカウントで、サービス プリンシパルを安全に保管するために使用される **資格情報** リソースを作成します。

    ![[資格情報] ページのスクリーンショット。[資格情報の追加] アクションが選択されています。](./media/analysis-services-refresh-azure-automation/6.png)

2. 資格情報の詳細を入力します。 **[ユーザー名]** にサービス プリンシパルのアプリケーション ID (appid) を入力し、 **[パスワード]** にサービス プリンシパル シークレットを入力します。

    ![資格情報の作成](./media/analysis-services-refresh-azure-automation/7.png)

3. Automation Runbook をインポートします。

    ![[Runbook] ページのスクリーンショット。[Runbook のインポート] アクションが選択されています。](./media/analysis-services-refresh-azure-automation/8.png)

4. [Refresh-Model.ps1](#sample-powershell-runbook) ファイルを参照し、 **[名前]** および **[説明]** を入力して、次に **[作成]** をクリックします。

    > [!NOTE]
    > このドキュメントの下部にある「[Powershell Runbook のサンプル](#sample-powershell-runbook)」セクションのスクリプトを使用して、Refresh-Model.ps1 という名前のファイルを作成し、ローカル コンピューターに保存して Runbook にインポートします。

    ![Runbook のインポート](./media/analysis-services-refresh-azure-automation/9.png)

5. Runbook が作成されると、自動的に編集モードになります。  **[発行]** を選択します。

    ![Runbook の発行](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 以前作成された資格情報リソースは、**Get-AutomationPSCredential** コマンドを使用して Runbook で取得されます。  このコマンドは次に **Invoke-ProcessASADatabase** PowerShell コマンド渡されて、Azure Analysis Services への認証を実行します。

6. **[開始]** をクリックして Runbook をテストします。

    ![[概要] ページのスクリーンショット。[開始] アクションが選択されています。](./media/analysis-services-refresh-azure-automation/11.png)

7. **DATABASENAME**、**ANALYSISSERVER**、および **REFRESHTYPE** パラメーターを入力し、 **[OK]** をクリックします。 **WEBHOOKDATA** パラメーターは、Runbook を手動で実行するときは必要ありません。

    ![Runbook を開始する](./media/analysis-services-refresh-azure-automation/12.png)

Runbook が正常に実行された場合、次のような出力が表示されます。

![正常に実行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>自己完結型の Azure Automation Runbook を使用する

Runbook は、Azure Analysis Services モデルの更新をスケジュールに基づいてトリガーするように構成できます。

これは次のようにして構成できます。

1. Automation Runbook で、 **[スケジュール]** 、 **[スケジュールの追加]** の順にクリックします。
 
    ![スケジュールを作成する](./media/analysis-services-refresh-azure-automation/14.png)

2. **[スケジュール]**  >  **[新しいスケジュールを作成します]** をクリックし、詳細を入力します。

    ![スケジュールを構成する](./media/analysis-services-refresh-azure-automation/15.png)

3. **Create** をクリックしてください。

4. スケジュールのパラメーターを入力します。 これらは Runbook をトリガーするたびに使用されます。 スケジュールを介して実行する場合、**WEBHOOKDATA** パラメーターは空白のままにしておく必要があります。

    ![パラメーターを構成する](./media/analysis-services-refresh-azure-automation/16.png)

5. **[OK]** をクリックします。

## <a name="consume-with-data-factory"></a>Data Factory で使用する

Azure Data Factory を使用して Runbook を使用するには、最初に Runbook の **Webhook** を作成します。 **Webhook** は Azure Data Factory の Web アクティビティを使用して呼び出すことができる URL を提供します。

> [!IMPORTANT]
> **Webhook** を作成するには、Runbook の状態が **[公開済み]** である必要があります。

1. Automation Runbook で、 **[Webhook]** をクリックし、次に **[Webhook の追加]** をクリックします。

   ![[Webhook の追加]](./media/analysis-services-refresh-azure-automation/17.png)

2. Webhook の名前と有効期限を指定します。  この名前は Automation Runbook 内の Webhook を識別するだけで、URL の一部を形成するわけではありません。

   >[!CAUTION]
   >ウィザードを閉じると元に戻すことができないため、必ずウィザードを閉じる前に URL をコピーしてください。
    
   ![Webhook を構成する](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook のパラメーターは空白のままでかまいません。  Azure Data Factory の Web アクティビティを構成するときに、Web 呼び出しの本文にパラメーターを渡すことができます。

3. Data Factory で、**Web アクティビティ** を構成します

### <a name="example"></a>例

   ![Web アクティビティの例](./media/analysis-services-refresh-azure-automation/19.png)

**[URL]** は、Webhook から作成された URL です。

**[本文]** は、次のプロパティが含まれている必要がある JSON ドキュメントです。


|プロパティ  |値  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services データベースの名前 <br/> 例:AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services のサーバー名。 <br/> 例: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |実行する更新の種類。 <br/> 例:[完全]         |

JSON 本文の例:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

これらのパラメーターは、Runbook PowerShell スクリプトで定義されます。  Web アクティビティが実行されたときに渡される JSON ペイロードは、WEBHOOKDATA です。

これは逆シリアル化されて PowerShell のパラメーターとして格納され、Invoke-ProcesASDatabase PowerShell コマンドで使用されます。

![逆シリアル化された Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Azure Analysis Services で Hybrid Worker を使用する

静的パブリック IP アドレスを持つ Azure 仮想マシンは、Azure Automation Hybrid Worker として使用できます。  その後、このパブリック IP アドレスは Azure Analysis Services ファイアウォールに追加できます。

> [!IMPORTANT]
> 仮想マシンのパブリック IP アドレスが静的に構成されていることを確認します。
>
>Azure Automation Hybrid Worker の構成の詳細については、「[Hybrid Runbook Worker をインストールする](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation)」を参照してください。

Hybrid Worker が構成されたら、「[Data Factory で使用する](#consume-with-data-factory)」セクションで説明されているように Webhook を作成します。  ここでの唯一の違いは、Webhook を構成するときに **[Run on]\(実行先\)**  >  **[Hybrid Worker]** オプションを選択することです。

Hybrid Worker を使用した Webhook の例:

![Hybrid Worker Webhook の例](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell Runbook のサンプル

次のコード スニペットは、PowerShell Runbook を使用して Azure Analysis Services モデルの更新を実行する方法の例です。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>次のステップ

[サンプル](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)