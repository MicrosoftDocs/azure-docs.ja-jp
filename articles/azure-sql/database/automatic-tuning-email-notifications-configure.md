---
title: 自動チューニングの電子メール通知 攻略ガイド
description: Azure SQL Database の自動クエリ チューニングに対する電子メール通知を有効にします。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500956"
---
# <a name="email-notifications-for-automatic-tuning"></a>自動チューニングの電子メール通知
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL Database のチューニング推奨情報は、Azure SQL Database の[自動チューニング](automatic-tuning-overview.md)によって生成されます。 このソリューションは、データベースのワークロードを継続的に監視して分析し、個別のデータベースそれぞれについて、インデックスの作成や削除、クエリ実行プランの最適化に関するカスタマイズされたチューニング推奨情報を提供します。

Azure SQL Database 自動チューニングの推奨情報は、[REST API](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) 呼び出しで取得するか、[T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) や [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) のコマンドを使用して、[Azure portal](database-advisor-find-recommendations-portal.md) で確認できます。 この記事は、PowerShell スクリプトを使用して自動チューニングに関する推奨情報を取得する方法に基づいています。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>自動チューニング推奨情報の電子メール通知を自動化する

この後のソリューションにより、自動チューニング推奨情報の電子メール通知の送信を自動化します。 説明するソリューションには、チューニング推奨情報を取得するための PowerShell スクリプトの実行の自動化 ([Azure Automation](../../automation/automation-intro.md) を使用) と、電子メール配信ジョブの自動化 ([Microsoft Flow](https://flow.microsoft.com) を使用) が含まれます。

## <a name="create-azure-automation-account"></a>Azure Automation アカウントを作成する

Azure Automation を使用するには、まず Automation アカウントを作成し、PowerShell スクリプトの実行に使用する Azure リソースに対して構成します。 Azure Automation とその機能について詳しくは、[Azure Automation の使用](../../automation/index.yml)に関する記事をご覧ください。

次の手順に従い、Azure Marketplace で Automation アプリを選択して構成する方法により、Azure Automation アカウントを作成します。

1. Azure Portal にログインします。
1. 左上隅の **[+ リソースの作成]** をクリックします。
1. 「**Automation**」を検索します (Enter キーを押します)。
1. 検索結果で Automation アプリをクリックします。

    ![Azure Automation の追加](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. [Automation アカウントの作成] ペインが表示されたら、 **[作成]** をクリックします。
1. 必要な情報を設定します。この Automation アカウントの名前を入力し、PowerShell スクリプトの実行に使用する Azure サブスクリプション ID と Azure リソースを選択します。
1. **[Azure 実行アカウントの作成]** オプションでは **[はい]** を選択し、Azure Automation を利用して PowerShell スクリプトを実行するためのアカウントの種類を構成します。 アカウントの種類について詳しくは、[実行アカウント](../../automation/manage-runas-account.md)に関する記事をご覧ください。
1. **[作成]** をクリックして、Automation アカウントの作成を完了します。

> [!TIP]
> Automation アプリを作成するときに入力したとおりに、Azure Automation アカウント名、サブスクリプション ID、リソースを正確に記録してください (コピーしてメモ帳に貼り付けるなど)。 この情報は後で必要になります。

同じように自動化を設定する Azure サブスクリプションが複数ある場合は、他のサブスクリプションについてもこのプロセスを繰り返す必要があります。

## <a name="update-azure-automation-modules"></a>Azure Automation モジュールを更新する

自動チューニング推奨情報を取得する PowerShell スクリプトでは、Azure モジュール バージョン 4 以降が必要な [Get-AzResource](/powershell/module/az.Resources/Get-azResource) および [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) コマンドが使用されます。

- Azure モジュールに更新が必要な場合は、「[Azure Automation での Az モジュールのサポート](../../automation/shared-resources/modules.md)」を参照してください。

## <a name="create-azure-automation-runbook"></a>Azure Automation Runbook を作成する

次の手順では、チューニング推奨情報を取得する PowerShell スクリプトを配置する Runbook を Azure Automation に作成します。

次の手順に従って、新しい Azure Automation Runbook を作成します。

1. 前の手順で作成した Azure Automation アカウントにアクセスします。
1. Automation アカウントのウィンドウが表示されたら、左側の **[Runbook]** メニュー項目をクリックして、PowerShell スクリプトを含む新しい Azure Automation Runbook を作成します。 Automation Runbook の作成の詳細については、[新しい Runbook の作成](../../automation/manage-runbooks.md#create-a-runbook)に関するページを参照してください。
1. 新しい Runbook を追加するには、 **[+ Runbook の追加]** メニュー オプションをクリックしてから、 **[簡易作成 – 新しい Runbook の作成]** をクリックします。
1. [Runbook] ウィンドウで、Runbook の名前 (この例のためには 「**AutomaticTuningEmailAutomation**」を使用) を入力し、Runbook の種類として **PowerShell** を選択し、この Runbook の目的を示す説明を入力します。
1. **[作成]** ボタンをクリックすると、新しい Runbook の作成が完了します。

    ![Azure Automation Runbook を追加する](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

次の手順に従って、作成した Runbook 内に PowerShell スクリプトを読み込みます。

1. **[PowerShell Runbook の編集]** ウィンドウで、メニュー ツリーの **[Runbook]** を選択し、自分の Runbook の名前 (この例では "**AutomaticTuningEmailAutomation**") が表示されるまで展開します。 この Runbook を選択します。
1. [PowerShell Runbook の編集] の 1 行目 (番号 1 から開始) に、次のPowerShell スクリプト コードをコピーして貼り付けます。 この PowerShell スクリプトは作業を開始できるように提供しています。 ニーズに合わせてスクリプトを変更してください。

提供された PowerShell スクリプトのヘッダーで、`<SUBSCRIPTION_ID_WITH_DATABASES>` を自分の Azure サブスクリプション ID で置き換える必要があります。 自分の Azure サブスクリプション ID を取得する方法については、「[Getting your Azure Subscription GUID](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal)」(Azure サブスクリプション GUID の取得) をご覧ください。

複数のサブスクリプションがある場合は、スクリプトのヘッダーにある "$subscriptions" プロパティにコンマで区切って追加できます。

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

右上隅の **[保存]** ボタンをクリックして、スクリプトを保存します。 スクリプトに問題がなければ、 **[公開]** ボタンをクリックしてこの Runbook を公開します。

Runbook のメイン ウィンドウで **[開始]** ボタンをクリックすると、スクリプトを **テスト** できます。 **[出力]** をクリックすると、実行したスクリプトの結果が表示されます。 この出力が電子メールの内容になります。 スクリプトのサンプル出力は、次のスクリーンショットで確認できます。

![Azure Automation による自動チューニング推奨情報の実行ビュー](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

PowerShell スクリプトをニーズに合わせてカスタマイズして、内容を調整してください。

上記の手順では、自動チューニング推奨情報を取得する PowerShell スクリプトが Azure Automation に読み込まれます。 次の手順では、電子メール配信ジョブを自動化してスケジュール設定します。

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Microsoft Flow で電子メール・ジョブを自動化する

ソリューションを完成させるには、最後の手順として、3 つのアクション (ジョブ) で構成される自動フローを Microsoft Flow で作成します。

- **[Azure Automation - ジョブの作成]** – PowerShell スクリプトを実行して Azure Automation Runbook 内の自動チューニング推奨情報を取得するために使用します。
- **[Azure Automation - ジョブ出力の取得]** – 実行した PowerShell スクリプトの出力を取得するために使用します。
- **[Office 365 Outlook – 電子メールの送信]** – 電子メールを送信します。 電子メールは、フロー作成者の職場または学校アカウントを使用して送信されます。

Microsoft Flow の機能について詳しくは、「[Microsoft Flow を使ってみる](/flow/getting-started)」をご覧ください。

この手順の前提条件として、[Microsoft Flow](https://flow.microsoft.com) アカウントを新規登録してログインします。 ソリューションにログインしたら、次の手順に従って **新しいフロー** を設定します。

1. **[マイ フロー]** メニュー項目にアクセスします。
1. [マイ フロー] で、ページの一番上の **[+ 一から作成]** リンクを選択します。
1. ページの一番下の **[多数のコネクタやトリガーを検索する]** リンクをクリックします。
1. 検索フィールドに「**繰り返し**」と入力し、検索結果から **[スケジュール - 繰り返し]** を選択し、電子メール配信ジョブの実行スケジュールを設定します。
1. [繰り返し] ウィンドウの [頻度] フィールドで、このフロー (自動電子メールの送信) を実行するスケジュール頻度として、分、時、日、週などを選択します。

次の手順では、新たに作成した繰り返しフローに 3 つのジョブ (作成、出力の取得、電子メールの送信) を追加します。 必要なジョブをフローに追加するには次の手順に従います。

1. チューニング推奨情報を取得する PowerShell スクリプトを実行するアクションを作成します。

   - **[+ 新しいステップ]** を選択してから、繰り返しフローのペインで **[アクションの追加]** を選択します。
   - 検索フィールドに「**automation**」と入力し、検索結果から **[Azure Automation – ジョブの作成]** を選択します。
   - [ジョブの作成] ウィンドウで、ジョブのプロパティを構成します。 この構成では、 **[Automation アカウント] ウィンドウ** で **前に記録した** Azure サブスクリプション ID、リソース グループ、Automation アカウントの詳細が必要になります。 このセクションで指定できるオプションについて詳しくは、[Azure Automation でのジョブの作成](/connectors/azureautomation/#create-job)に関する記事をご覧ください。
   - **[フローの保存]** をクリックすると、このアクションの作成が完了します。

2. 実行した PowerShell スクリプトの出力を取得するアクションを作成します。

   - **[+ 新しいステップ]** を選択し、繰り返しフローのウィンドウで **[アクションの追加]** を順に選択します。
   - 検索フィールドに「**automation**」と入力し、検索結果から **[Azure Automation - ジョブ出力の取得]** を選択します。 このセクションで指定できるオプションについて詳しくは、「[Azure Automation – Get job output](/connectors/azureautomation/#get-job-output)」(Azure Automation - ジョブ出力の取得) をご覧ください。
   - 必須フィールドを設定します (前のジョブの作成と同様)。([Automation アカウント] ペインに入力したように) Azure サブスクリプション ID、リソース グループ、Automation アカウントを入力します。
   - フィールド **[ジョブ ID]** の内側をクリックして **[動的なコンテンツ]** メニューを表示します。 このメニューで、オプション **[ジョブ ID]** を選択します。
   - **[フローの保存]** をクリックすると、このアクションの作成が完了します。

3. Office 365 統合を使用して電子メールを送信するアクションを作成します。

   - **[+ 新しいステップ]** を選択してから、繰り返しフローのペインで **[アクションの追加]** を選択します。
   - 検索フィールドに「**電子メールの送信**」と入力し、検索結果から **[Office 365 Outlook – 電子メールの送信]** を選択します。
   - **[宛先]** フィールドに、通知電子メールを送信する必要があるメール アドレスを入力します。
   - **[件名]** フィールドに、電子メールの件名、たとえば「自動チューニング推奨情報の電子メール通知」を入力します。
   - **[本文]** フィールドの内側をクリックして、 **[動的なコンテンツ]** メニューを表示します。 このメニューの **[ジョブ出力の取得]** で **[コンテンツ]** を選択します。
   - **[フローの保存]** をクリックすると、このアクションの作成が完了します。

> [!TIP]
> さまざまな宛先に自動電子メールを送信するには、個別のフローを作成します。 これらの追加フローでは、[宛先] フィールドの受信者電子メール アドレスと [件名] フィールドの電子メール件名を変更します。 Azure Automation で新しい Runbook を作成し、カスタマイズした PowerShell スクリプト (Azure サブスクリプション ID の変更に関するものなど) を含めることにより、自動化シナリオをさらにカスタマイズできるようになります。たとえば、個別のサブスクリプションに関する自動チューニング推奨情報を別々の受信者に電子メール送信することができます。

上記で、電子メール配信ジョブ ワークフローの構成に必要な手順は完了します。 作成した 3 つのアクションを含むフロー全体を次の図に示します。

![自動チューニング電子メール通知の表示](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

このフローをテストするには、フロー ウィンドウの右上隅の **[今すぐ実行]** をクリックします。

電子メール通知が正常に送信されたかどうかを示す自動ジョブの実行統計は、[フローの分析] ウィンドウに表示されます。

![自動チューニング電子メール通知のフローの実行](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

[フローの分析] ウィンドウは、ジョブ実行の正常終了を監視するため、またトラブルシューティングが必要な場合に役立ちます。  トラブルシューティングでは、PowerShell スクリプトの実行ログを調べることも必要になりますが、これには Azure Automation アプリからアクセスできます。

自動電子メールの最終的な出力は、このソリューションを作成して実行した後に受け取る次の電子メールのようになります。

![自動チューニング電子メール通知のサンプル電子メール出力](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

PowerShell スクリプトを調整することにより、自動電子メールの出力や書式設定をニーズに合わせて調整できます。

カスタム シナリオによって異なりますが、さらにソリューションをカスタマイズして、特定のチューニング イベントに基づいた電子メールや、複数のサブスクリプションまたはデータベースに関して複数の受信者への電子メールを作成できます。

## <a name="next-steps"></a>次のステップ

- 自動チューニングによってデータベース パフォーマンスを向上させる方法について詳しくは、「[Azure SQL Database での自動チューニング](automatic-tuning-overview.md)」をご覧ください。
- Azure SQL Database で自動チューニングを有効にするには、「[自動チューニングの有効化](automatic-tuning-enable.md)」をご覧ください。
- 自動チューニング推奨情報を手動で確認して適用する方法については、「[パフォーマンスに関する推奨事項の検索と適用](database-advisor-find-recommendations-portal.md)」をご覧ください。