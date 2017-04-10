---
title: "JSON 形式のタグを使用した Azure VM の状態のスケジュール設定 | Microsoft Docs"
description: "この記事では、タグに関する JSON 文字列を使って VM の起動とシャットダウンのスケジュールを自動化する方法を示します。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
translationtype: Human Translation
ms.sourcegitcommit: 00d348306f76194bb44e5252be5c956a48192768
ms.openlocfilehash: 69f05a8c0fc88201fc365546870585de5a419f1d


---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Azure Automation シナリオ: JSON 形式のタグを使用して Azure VM の起動とシャットダウンのスケジュールを作成する
お客様は多くの場合、サブスクリプション コストを削減し、ビジネス要件や技術要件に対応するために、仮想マシンの起動とシャットダウンのスケジュールを設定することを望みます。

次のシナリオでは、Schedule というタグを使用して、Azure のリソース グループ レベルまたは仮想マシン レベルで VM の自動起動と自動シャットダウンを設定できます。 このスケジュールは、起動時刻とシャットダウン時刻を指定して、日曜日から土曜日まで構成できます。

すぐに使用できるオプションがいくつかあります。 チェックの内容は次のとおりです

* [仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) 。
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) サービス。

ただし、これらのオプションは特定のシナリオにのみ対応しており、サービスとしてのインフラストラクチャ (IaaS) VM には適用できません。

Schedule タグをリソース グループに適用すると、タグはそのリソース グループ内のすべての仮想マシンにも適用されます。 いずれかの VM に直接適用されているスケジュールもある場合、前回のスケジュールが次の順序で優先されます。

1. リソース グループに適用されているスケジュール
2. リソース グループとリソース グループ内の仮想マシンに適用されているスケジュール
3. 仮想マシンに適用されているスケジュール

このシナリオでは主に、指定された形式の JSON 文字列を受け取り、それを Schedule というタグの値として追加します。 その後、Runbook によってリソース グループと仮想マシンがすべて一覧表示され、前述のシナリオに基づいて各 VM のスケジュールが識別されます。 次に、スケジュールの設定されている VM がループ処理され、実行するアクションが評価されます。 たとえば、どの VM で停止、シャットダウン、または無視が必要であるかが特定されます。

こうした Runbook は、[Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)を使用して認証します。

## <a name="download-the-runbooks-for-the-scenario"></a>このシナリオで使用する Runbook のダウンロード
このシナリオは&4; つの PowerShell ワークフロー Runbook で構成されています。このシナリオの Runbook は、[TechNet ギャラリー](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7)またはこのプロジェクトの [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) リポジトリからダウンロードできます。

| Runbook | 説明 |
| --- | --- |
| Test-ResourceSchedule |各仮想マシンのスケジュールを確認し、スケジュールに応じてシャットダウンまたは起動を実行します。 |
| Add-ResourceSchedule |VM またはリソース グループに Schedule タグを追加します。 |
| Update-ResourceSchedule |既存の Schedule タグを新しいタグで置き換えることで変更します。 |
| Remove-ResourceSchedule |VM またはリソース グループから Schedule タグを削除します。 |

## <a name="install-and-configure-this-scenario"></a>このシナリオのインストールと構成
### <a name="install-and-publish-the-runbooks"></a>Runbook をインストールして発行する
Runbook をダウンロードしたら、「[Azure Automation での Runbook の作成またはインポート](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation)」の手順を使用してインポートできます。  各 Runbook が Automation アカウントに正常にインポートされたら、それを発行します。

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Test-ResourceSchedule Runbook にスケジュールを追加する
次の手順に従って、Test-ResourceSchedule Runbook のスケジュールを有効にします。 この Runbook によって、起動、シャットダウン、または現状維持の対象となる仮想マシンが特定されます。

1. Azure ポータルで Automation アカウントを開き、 **[Runbook]** タイルをクリックします。
2. **Test-ResourceSchedule** ブレードで、**[スケジュール]** タイルをクリックします。
3. **[スケジュール]** ブレードで、**[スケジュールの追加]** をクリックします。
4. **[スケジュール]** ブレードで、**[スケジュールを Runbook にリンクします]** を選択します。 その後、 **[新しいスケジュールを作成します]**を選択します。
5. **[新しいスケジュール]** ブレードで、このスケジュールの名前を入力します (たとえば、 *HourlyExecution*)。
6. スケジュールの **[開始]**で、スケジュールの開始時刻を&1; 時間単位の値で設定します。
7. **[繰り返し]** を選択し、**[繰り返しの間隔]** で間隔として **[1 時間]** を選択します。
8. **[有効期限の設定]** が **[いいえ]** に設定されていることを確認し、**[作成]** をクリックして新しいスケジュールを保存します。
9. **[Runbook のスケジュール設定]** オプション ブレードで、**[パラメーターと実行設定]** を選択します。 Test-ResourceSchedule の **[パラメーター]** ブレードで、**[サブスクリプション名]** フィールドにサブスクリプションの名前を入力します。  これは Runbook に必要な唯一のパラメーターです。  操作が終了したら、 **[OK]**をクリックします。

完了した Runbook のスケジュールは次のようになります。

![Configured Test-ResourceSchedule runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>JSON 文字列の形式
このソリューションでは主に、指定された形式の JSON 文字列を受け取り、それを Schedule というタグの値として追加します。 その後、Runbook によってリソース グループと仮想マシンがすべて一覧表示され、各仮想マシンのスケジュールが識別されます。

スケジュールの設定されている仮想マシンがループ処理され、実行するアクションが確認されます。 このソリューションで求められる形式の例を以下に示します。

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

この構造に関する詳しい情報を一部次に示します。

1. この JSON 構造の形式は、Azure での 1 つのタグ値の文字数制限 (256 文字) に合わせて最適化されています。
2. *TzId* は、仮想マシンのタイム ゾーンを表します。 この ID は、PowerShell セッションで .NET クラス TimeZoneInfo を使用して取得できます (**[System.TimeZoneInfo]::GetSystemTimeZones()**)。

   ![PowerShell での GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * 曜日は&0; から&6; までの数値で表します。 値&0; は日曜日を表します。
   * 開始時刻は、 **S** 属性で表し、その値は 24 時間形式です。
   * 終了時刻またはシャットダウン時刻は **E** 属性で表し、その値は 24 時間形式です。

     **S** 属性と **E** 属性にそれぞれ値ゼロ (0) を指定した場合、仮想マシンは評価の時点の状態のままになります。
3. 週の特定の曜日の評価をスキップする場合は、その曜日のセクションを追加しないでください。 次の例では、月曜日のみが評価され、その他の曜日は無視されます。

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>リソース グループまたは仮想マシンへのタグ付け
VM をシャットダウンするには、VM または VM が存在するリソース グループにタグを付ける必要があります。 Schedule タグが付けられていない仮想マシンは評価されません。 そのため、起動も、シャットダウンもされません。

このソリューションを使用してリソース グループまたは VM にタグを付ける方法は&2; つあります。 タグ付けはポータルから直接実行できます。 そのほか、Add-ResourceSchedule Runbook、Update-ResourceSchedule Runbook、Remove-ResourceSchedule Runbook を使用する方法もあります。

### <a name="tag-through-the-portal"></a>ポータル経由でタグ付けする
ポータルで仮想マシンまたはリソース グループにタグを付けるには、次の手順に従います。

1. JSON 文字列をフラット化し、スペースがないことを確認します。  JSON 文字列は次のようになります。

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. このスケジュールを適用する VM またはリソース グループの **[タグ]** アイコンを選択します。

   ![VM tag option](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. キー/値のペアの後に続けてタグを定義します。 **[キー]** フィールドに「**Schedule**」と入力し、**[値]** フィールドに JSON 文字列を貼り付けます。 **[Save]**をクリックします。 リソースのタグ一覧に新しいタグが表示されます。

   ![VM schedule tag](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>PowerShell でタグ付けする
インポートしたすべての Runbook には、スクリプトの先頭にヘルプ情報が含まれていて、PowerShell から直接 Runbook を実行する方法が説明されています。 PowerShell から、Add-ScheduleResource Runbook と Update-ScheduleResource Runbook を呼び出すことができます。 そのためには、必要なパラメーターを渡します。これにより、ポータルの外部にある VM またはリソース グループの Schedule タグを作成または更新できます。

PowerShell でタグの作成、追加、削除を行うには、まず [Azure 用の PowerShell 環境を設定する](/powershell/azureps-cmdlets-docs)必要があります。 設定が完了した後で、以下の手順に進むことができます。

### <a name="create-a-schedule-tag-with-powershell"></a>PowerShell でスケジュールのタグを作成する
1. PowerShell セッションを開きます。 次に、次の例を使用して実行アカウントを認証し、サブスクリプションを指定します。

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. スケジュールのハッシュ テーブルを定義します。 作成方法を次の例に示します。

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. Runbook に必要なパラメーターを定義します。 次の例では、VM を対象にしています。

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    リソース グループにタグを付ける場合は、次のように $params ハッシュ テーブルから *VMName* パラメーターを削除します。

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. 次のパラメーターを指定して Add-ResourceSchedule Runbook を実行し、Schedule タグを作成します。

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. リソース グループまたは仮想マシンのタグを更新する場合は、次のパラメーターを指定して **Update-ResourceSchedule** Runbook を実行します。

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>PowerShell でスケジュールのタグを削除する
1. PowerShell セッションを開き、次のコマンドを実行して実行アカウントで認証し、サブスクリプションを選択して指定します。

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Runbook に必要なパラメーターを定義します。 次の例では、VM を対象にしています。

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    リソース グループからタグを削除する場合は、次のように $params ハッシュ テーブルから *VMName* パラメーターを削除します。

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Remove-ResourceSchedule Runbook を実行して Schedule タグを削除します。

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. リソース グループまたは仮想マシンのタグを更新する場合は、次のパラメーターを指定して Remove-ResourceSchedule Runbook を実行します。

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> これらの Runbook (および仮想マシンの状態) を積極的に監視し、仮想マシンがシャットダウンされ、その後に起動されたことを確認するようお勧めします。
>

Azure Portal で Test-ResourceSchedule Runbook ジョブの詳細を表示するには、Runbook の **[ジョブ]** タイルを選択します。 ジョブの概要として、入力パラメーターと出力ストリーム、さらにジョブに関する全般情報が表示されます。例外が発生した場合は、その情報も表示されます。

**[ジョブの概要]** には、出力、警告、およびエラー ストリームからのメッセージが表示されます。 Runbook 実行の詳細な結果を表示するには、 **[出力]** タイルを選択します。

![Test-ResourceSchedule の出力](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>次のステップ
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
* Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。
* PowerShell スクリプトのサポート機能の詳細については、 [Azure Automation でのネイティブ PowerShell スクリプトのサポート](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)に関するブログ記事を参照してください。
* Runbook のログ記録と出力の詳細については、「 [Azure Automation での Runbook の出力およびメッセージ](automation-runbook-output-and-messages.md)」を参照してください。
* Azure 実行アカウントの詳細と、それを使用して Runbook を認証する方法については、「[Azure 実行アカウントを使用した Runbook の認証](automation-sec-configure-azure-runas-account.md)」を参照してください。



<!--HONumber=Jan17_HO4-->


