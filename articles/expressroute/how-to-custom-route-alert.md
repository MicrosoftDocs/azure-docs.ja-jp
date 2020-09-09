---
title: ExpressRoute:アドバタイズされるルートのカスタム アラートを構成する方法
description: この記事では、Azure Automation と Logic Apps を使用して、200 ルートの上限に達するのを防ぐために、ExpressRoute ゲートウェイからオンプレミス ネットワークにアドバタイズされるルートの数を監視する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: f29f43234f1541abeb448e722d0b72ef7c0221c9
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401726"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>カスタム アラートを構成して、アドバタイズされるルートを監視する

この記事では、Azure Automation と Logic Apps を使用して、ExpressRoute ゲートウェイからオンプレミス ネットワークにアドバタイズされるルートの数を常に監視する方法について説明します。 監視により、[200 ルートの制限](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering)を超えないようにすることができます。

**Azure Automation** を使用すると、*Runbook* に格納されているカスタム PowerShell スクリプトの実行を自動化できます。 この記事の構成を使用する場合、Runbook には、1 つまたは複数の ExpressRoute ゲートウェイに対してクエリを行う PowerShell スクリプトが含まれます。 これは、リソース グループ、ExpressRoute ゲートウェイ名、オンプレミスでアドバタイズされたネットワーク プレフィックスの数を含むデータセットを収集します。

**Azure Logic Apps** により、Azure Automation の Runbook を呼び出すカスタム ワークフローがスケジュールされます。 Runbook の実行には、ジョブが使用されます。 データ収集が実行されると、Azure Logic Apps ワークフローによってデータが分類され、事前に定義されたしきい値を超えるまたは下回るネットワーク プレフィックス数の一致条件に基づいて、宛先のメール アドレスに情報が送信されます。

### <a name="workflow"></a><a name="workflow"></a>ワークフロー

カスタム アラートの設定は、次の 3 つの主要な手順に基づいています。

1. "実行" アカウントとアクセス許可を持つ Automation アカウントを作成します。

2. Runbook を作成して構成します。

3. Automation アカウントを起動し、数値がしきい値 (160 など) を超えた場合にアラート メールを送信するロジック アプリを作成します。

## <a name="before-you-begin"></a><a name="before"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* デプロイ環境に ExpressRoute ゲートウェイが少なくとも 1 つある。

* Azure Automation での[実行アカウント](../automation/manage-runas-account.md)についての基本的な知識がある。

* [Azure Logic Apps](../logic-apps/logic-apps-overview.md) に慣れている。

* Azure PowerShell の使用に慣れている。 ExpressRoute ゲートウェイでネットワーク プレフィックスを収集するために、Azure PowerShell が必要です。 Azure PowerShell 全般の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/?view=azps-4.1.0)を参照してください。

### <a name="notes-and-limitations"></a><a name="limitations"></a>注意事項と制限事項

* この記事で説明するカスタム アラートは、運用と制御を向上させるためのアドオンです。 ExpressRoute のネイティブ アラートに代わるものではありません。
* ExpressRoute ゲートウェイに対するデータ収集はバックグラウンドで実行されます。 実行時間は予想よりも長くなる可能性があります。 ジョブがキューイングされるのを回避するために、ワークフローの繰り返しを適切に設定する必要があります。
* スクリプトまたは Resource Manager テンプレートによるデプロイは、カスタム アラーム トリガーよりも速くなる可能性があります。 これにより、ExpressRoute ゲートウェイのネットワーク プレフィックス数が 200 ルートの上限を超えて増加する可能性があります。

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>アカウントを作成して構成する

Azure portal で Automation アカウントを作成すると、[実行](../automation/manage-runas-account.md#types-of-run-as-accounts)アカウントが自動的に作成されます。 このアカウントは、次の操作を実行します。

* 自己署名証明書を使用して Azure Active Directory (Azure AD) アプリケーションを作成する。 実行アカウント自体には、既定で毎年更新する必要がある証明書があります。

* Azure AD でアプリケーションのサービス プリンシパル アカウントを作成する。

* 使用されている Azure サブスクリプションの共同作成者ロール (RBAC) を自身に割り当てる。 このロールにより、Runbook を使用して Azure Resource Manager リソースが管理されます。

Automation アカウントを作成するには、特権とアクセス許可が必要です。 詳細については、「[Automation アカウントを作成するために必要なアクセス許可](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)」を参照してください。

### <a name="1-create-an-automation-account"></a><a name="about"></a>1.Automation アカウントの作成

実行アクセス許可を持つ Automation アカウントを作成します。 手順については、「[Azure Automation アカウントを作成する](../automation/automation-quickstart-create-account.md)」をご覧ください。

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Automation アカウントの追加" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2.実行アカウントにロールを割り当てる

既定では、**共同作成者**ロールが、**実行**アカウントによって使用されるサービス プリンシパルに割り当てられます。 サービス プリンシパルに割り当てられた既定のロールを維持することも、[組み込みロール](../role-based-access-control/built-in-roles.md) (閲覧者など) または[カスタム ロール](../active-directory/users-groups-roles/roles-create-custom.md)を割り当てることによってアクセス許可を制限することもできます。

 次の手順を使用して、実行アカウントで使用されるサービス プリンシパルに割り当てられているロールを決定します。

1. Automation アカウントに移動します。 **[アカウント設定]** に移動し、 **[実行アカウント]** を選択します。

2. **[ロール]** を選択し、使用されているロールの定義を表示します。

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="ロールを割り当てる":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Runbook の作成と構成

### <a name="1-install-modules"></a><a name="install-modules"></a>1.モジュールのインストール

Azure Automation の Runbook で PowerShell コマンドレットを実行するには、追加の Azure PowerShell Az モジュールをいくつかインストールする必要があります。 モジュールをインストールするには、次の手順に従います。

1. Azure Automation アカウントを開き、 **[モジュール]** に移動します。

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="モジュールに移動":::

2. ギャラリーを検索し、次のモジュールをインポートします。**Az.Accounts**、**Az.Network**、**Az.Automation**、および **Az.Profile**。

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="モジュールの検索とインポート" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2.Runbook を作成する

1. PowerShell Runbook を作成するには、Automation アカウントに移動します。 **[プロセス オートメーション]** で、 **[Runbook]** タイルを選択し、 **[Runbook の作成]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Runbook を作成する":::

2. **[作成]** を選択して、Runbook を作成します。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="[作成] を選択。":::

3. 新しく作成した Runbook を選択し、 **[編集]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Edit runbook":::

4. **[編集]** で、PowerShell スクリプトを貼り付けます。 [サンプル スクリプト](#script)を変更して、1 つまたは複数のリソース グループ内の ExpressRoute ゲートウェイを監視するために使用できます。

   このサンプル スクリプトでは、次の設定に注意してください。

    * 配列 **$rgList** には、ExpressRoute ゲートウェイを含むリソース グループの一覧が含まれています。 リスト ベースの ExpressRoute ゲートウェイをカスタマイズできます。
    * 変数 **$thresholdNumRoutes** には、ExpressRoute ゲートウェイからオンプレミス ネットワークにアドバタイズされるネットワーク プレフィックス数のしきい値が定義されます。

#### <a name="example-script"></a><a name="script"></a>スクリプトの例

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3.Runbook を保存して発行する

1. **[保存]** を選択して、Runbook の下書きのコピーを保存します。
2. **[発行]** をクリックして、Automation アカウントの Runbook の公式バージョンとして Runbook を発行します。

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Runbook を保存して発行する。":::

PowerShell スクリプトを実行すると、値の一覧が収集されます。
 
* Resource group

* ExpressRoute ゲートウェイ名

* 最初の BGP ピア (peer1) の IP アドレス

* 2 番目の BGP ピア (peer2) の IP アドレス

* ExpressRoute ゲートウェイから最初の BGP ピア (peer1) にアドバタイズされたネットワーク プレフィックスの数

* ExpressRoute ゲートウェイから2番目の BGP ピア (peer2) にアドバタイズされたネットワーク プレフィックスの数

* Timestamp

* 状態。次のように分類されます。

  * 'OK': ルートの数がしきい値未満の場合
  * 'ALERT': ルートの数がしきい値を超えている場合
  * 'WARNING': 2 つの BGP ピアにアドバタイズされているネットワーク プレフィックスの数が異なる場合

* 状態 (OK、ALERT、WARNING) の詳細な説明のアラート メッセージ

PowerShell スクリプトでは、収集された情報が JSON 出力に変換されます。 Runbook では、PowerShell コマンドレット [Write-Output](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Write-Output?)を出力ストリームとして使用して、情報をクライアントに通知します。

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4.Runbook を検証する

Runbook を作成したら、検証する必要があります。 **[開始]** を選択し、さまざまなジョブ ストリームの出力とエラーを確認します。

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Runbook を検証する" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>ロジック アプリを作成して構成する

Azure Logic Apps は、収集とアクションのすべてのプロセスのオーケストレーターです。 次のセクションでは、ロジック アプリを使用してワークフローを作成します。

### <a name="workflow"></a>ワークフロー

このワークフローでは、ExpressRoute ゲートウェイを定期的に監視するロジック アプリを作成します。 新しい項目が存在する場合、ロジック アプリから項目ごとにメールが送信されます。 完成したロジック アプリの大まかなワークフローは、次のようになります。

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="ロジック アプリのワークフロー":::

### <a name="1-create-a-logic-app"></a>1.ロジック アプリを作成します

**ロジック アプリ デザイナー**で、 **[空のロジック アプリ]** テンプレートを使用してロジック アプリを作成します。 手順については、[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)に関する記事を参照してください。

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="空のテンプレート":::

### <a name="2-add-a-trigger"></a>2.トリガーの追加

すべてのロジック アプリは、トリガーによって開始されます。 トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動されるたびに、Azure Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。

事前に定義された時間スケジュールに基づいてロジック アプリを定期的に実行するには、組み込みの **[定期的なスケジュール]** をワークフローに追加します。 検索ボックスに「**スケジュール**」と入力します。 **[トリガー]** を選択します。 トリガーの一覧で、 **[定期的なスケジュール]** を選択します。

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="定期的なスケジュール":::

[定期的なスケジュール] トリガーでは、そのワークフローを繰り返し実行するためのタイムゾーンと繰り返しを設定できます。 ロジック アプリのトリガーには、間隔と頻度の組み合わせでそのスケジュールを定義します。 適切な最小繰り返し頻度を設定するために、次の要因を考慮してください。

* Automation Runbook の PowerShell スクリプトは、完了するまでに時間がかかります。 実行時間は、監視する ExpressRoute ゲートウェイの数によって異なります。 繰り返しの頻度が短すぎると、ジョブのキューが発生します。

* PowerShell スクリプトは、バックグラウンドでジョブとして実行されます。 すぐには開始されません。これは、可変の遅延の後に実行されます。

* 繰り返し頻度が短すぎると、Azure ExpressRoute ゲートウェイに不要な負荷が発生します。

ワークフロー構成の最後に、ワークフローを数回実行して、繰り返しの頻度の整合性を確認し、 **[実行の履歴]** で結果を確認できます。

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="定期的なアイテム" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3.ジョブの作成

ロジック アプリは、コネクタを介して他のアプリ、サービス、およびプラットフォームにアクセスします。 このワークフローの次のステップでは、前に定義した Azure Automation アカウントにアクセスするコネクタを選択します。

1. **Logic Apps デザイナー**で、 **[繰り返し]** の下にある **[新しいステップ]** を選択します。 **[アクションを選択してください]** と検索ボックスの下の **[すべて]** を選択します。
2. 検索ボックスに、「**Automation Accounts**」と入力して検索します。 **[ジョブの作成]** を選択します。 **[ジョブの作成]** は、前に作成した Automation Runbook を起動するために使用されます。

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="ジョブの作成":::

3. サービス プリンシパルを使用してサインインします。 既存のサービス プリンシパルを使用するか、新しく作成することができます。 新しいサービス プリンシパルを作成するには、「[方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md)」を参照してください。 **[サービス プリンシパルを使用して接続する]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="サインイン":::

4. **[接続名]** を入力し、 **[クライアント ID]** (アプリケーション ID)、 **[クライアント シークレット]** 、 **[テナント ID]** を追加します。 そのうえで **[Create]\(作成\)** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="サービス プリンシパルを使用して接続する":::

5. **[ジョブの作成]** ページで、サービス プリンシパルは、Automation アカウントをホストしている**リソース グループ** の "閲覧者" ロールと **Automation アカウント**の "Automation ジョブ オペレーター" を持っている必要があります。 さらに、 **[Runbook 名]** を新しいパラメーターとして追加したことを確認します。

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="ロール" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4.ジョブ出力を取得する

1. **[新しいステップ]** を選択します。 "Azure Automation" を検索します。 **[アクション]** の一覧から **[ジョブの出力を取得します]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="ジョブ出力の取得":::

2. **[ジョブの出力を取得します]** ページで、Automation アカウントへのアクセスに必要な情報を指定します。 使用する **[サブスクリプション]、[リソース グループ]** 、 **[Automation アカウント]** を選択します。 **[ジョブ ID]** ボックスの内側をクリックします。 **[動的なコンテンツ]** のリストが表示されたら、 **[ジョブ ID]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="[ジョブ ID]" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5.JSON を解析する

「Azure Automation のジョブの作成アクション」 (前のステップ) に含まれる情報によって、JSON オブジェクトが生成されます。 Logic Apps の **[JSON の解析]** は、JSON コンテンツのプロパティとその値からユーザー フレンドリなトークンを作成するための組み込みアクションです。 その後、これらのプロパティをワークフローで使用できます。

1. アクションを追加します。 **[ジョブの出力を取得します] -> [アクション]** で、 **[新しいステップ]** を選択します。
2. **[アクションを選択してください]** の検索ボックスで、「parse json」と入力して、このアクションを提供するコネクタを検索します。 **[アクション]** リストの中から、使用するデータ操作に対して **[JSON の解析]** アクションを選択します。

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="JSON の解析":::

3. **[コンテンツ]** ボックス内をクリックします。 [動的なコンテンツ] のリストが表示されたら、 **[コンテンツ]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="コンテンツ" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. JSON を解析するには、スキーマが必要です。 スキーマは、Automation Runbook の出力を使用して生成できます。 新しい Web ブラウザー セッションを開き、Automation Runbook を実行して出力を取得します。 **Logic Apps JSON の解析データ操作**アクションに戻ります。 ページの下部で、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="サンプルのペイロードを使用してスキーマを生成する":::

5. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** で、Automation Runbook の出力を貼り付け、 **[完了]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="サンプル ペイロードの貼り付け" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. JSON 入力ペイロードを解析することによってスキーマが自動的に生成されます。

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="スキーマの生成" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6.変数の定義と初期化

ワークフローのこのステップでは、電子メールでアラームを送信する条件を作成します。 電子メールの本文メッセージの柔軟なカスタム書式設定のために、ワークフローに補助変数が導入されています。

1. **[ジョブの出力を取得します] アクション**の下で、 **[新しいステップ]** を選択します。 検索ボックスで **[変数]** を探して選択します。

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="変数":::

2. **[アクション]** の一覧から **[変数を初期化する]** アクションを選択します。

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="変数を初期化する":::

3. 変数の名前を指定します。 **[型]** で **[文字列]** を選択します。 変数の **[値]** は、後でワークフローで割り当てられます。

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="String" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7."For each" アクションを作成する

JSON が解析されると、**JSON の解析データ操作**アクションによって、コンテンツが "*本文*" 出力に格納されます。 出力を処理するには、配列内の各項目に対して 1 つ以上のアクションを繰り返す "For each" ループを作成します。

1. **[変数を初期化する]** の下で、 **[アクションの追加]** を選択します。 検索ボックスに、フィルターとして「for each」と入力します。

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="コントロール":::

2. **[アクション]** の一覧から、**For each - 制御**を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="For each - 制御":::

3. **[以前の手順から出力を選択]** テキスト ボックス内をクリックします。 **[動的なコンテンツ]** の一覧が表示されたら、 **[本体]** を選択します。これは、解析された JSON からの出力です。

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="本文":::

4. JSON 本文の各要素に対して、条件を設定します。 アクション グループから **[制御]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="コントロール":::

5. **[アクション]** の一覧で、 **[条件 - 制御]** を選択します。 [条件 - 制御] は、ワークフローのデータを特定の値またはフィールドと比較する制御構造です。 その後、データが条件を満たしたかどうかによって実行するさまざまなアクションを指定できます。

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="[条件] [制御]":::

6. **[条件]** アクションのルートで、ロジック操作を **[Or]** に変更します。

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Or" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. ExpressRoute ゲートウェイが 2 つの BGP ピアにアドバタイズするネットワーク プレフィックス数の値を確認します。 ルートの数は、 **[動的なコンテンツ]** の "numRoutePeer1" および "numRoutePeer2" にあります。 [値] ボックスに、**numRoutePeer1** の値を入力します。

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. 条件に別の行を追加するには、 **[追加] -> [行の追加]** を選択します。 2 番目のボックスで、 **[動的なコンテンツ]** から **[numRoutePeer2]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. 2 つの動的変数 numRoute1 および numRoute2 のいずれかがしきい値を超える場合、ロジック条件は true になります。 この例では、しきい値は 160 (最大値 200 ルートの 80%) に固定されています。 しきい値は、要件に合わせて変更できます。 一貫性を確保するために、値は Runbook PowerShell スクリプトで使用されるのと同じ値にする必要があります。

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="ロジック条件":::

10. **[true の場合]** で、電子メールでアラートを送信するアクションを書式設定して作成します。 [アクションの選択] で、 **[変数]** を検索して選択します。

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="true の場合":::

11. [変数] で、次に、 **[アクションの追加]** を選択します。 **[アクション]** の一覧で **[変数の設定]** を選択します。

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="変数の設定":::

12. **[名前]** で、前に作成した **EmailBody** という名前の変数を選択します。 **[値]** には、アラートの電子メールを書式設定するために必要な HTML スクリプトを貼り付けます。 JSON 本文の値を含めるには、 **[動的なコンテンツ]** を使用します。 これらの設定を構成した後、変数 **Emailbody** に、アラートに関連するすべての情報が HTML 形式で含まれることになります。

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="変数の設定":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8.電子メール コネクタを追加する

Logic Apps には多数の電子メール コネクタが用意されています。 この例では、アラートを電子メールで送信する Outlook コネクタを追加します。 **[変数の設定]** の下で、 **[アクションの追加]** を選択します。 **[アクションの選択]** で、検索ボックスに「メールの送信」と入力します。

1. **[Office 365 Outlook]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="電子メールの送信":::

2. **[アクション]** の一覧で、 **[メールの送信 (V2)]** を選択します。

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="メールの送信 (V2)":::

3. サインインして Office 365 Outlook への接続を作成します。

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="サインイン":::

4. **[本文]** フィールドで、 **[動的なコンテンツの追加]** をクリックします。 [動的なコンテンツ] パネルから、変数 **Emailbody** を追加します。 **[件名]** と **[To]** フィールドに入力します。

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="本文":::

5. **[メールの送信 (V2)]** アクションにより、ワークフローのセットアップが完了します。

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="メールの送信 (V2)" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9.ワークフローの検証

最後の手順は、ワークフローの検証です。 **Logic Apps の [概要]** で、 **[トリガーの実行]** を選択します。 **[繰り返し]** を選択します。 ワークフローは、 **[実行の履歴]** で監視および検証できます。

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="トリガーの実行":::

## <a name="next-steps"></a>次のステップ

ワークフローをカスタマイズする方法の詳細については、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) に関する記事を参照してください。
