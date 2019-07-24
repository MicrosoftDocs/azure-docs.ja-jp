---
title: Azure App Service の Web アプリのステージング環境を設定する | Microsoft Docs
description: Azure App Service の Web アプリのステージングされた発行を使用する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 544ef8947f3a593071cabea018c722db96ab1475
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266207"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service でステージング環境を設定する
<a name="Overview"></a>

> [!NOTE]
> このハウツー ガイドでは、新しいプレビューの管理ページを使用してスロットを管理する方法を説明します。 既存の管理ページを使用するお客様は、前と同じ既存のスロット管理のページの使用を続行できます。 
>

使用している App Service プラン レベルが **Standard**、**Premium**、**Isolated** のいずれかである場合は、Web アプリ、Linux 上の Web アプリ、モバイル バック エンド、API アプリを [App Service](https://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする際、既定の運用スロットではなく、個別のデプロイ スロットにデプロイすることができます。 デプロイ スロットは、実際には固有のホスト名を持つライブ アプリです。 アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つのデプロイ スロットの間でスワップすることができます。 非運用スロットにアプリケーションをデプロイすることには、次のメリットがあります。

* ステージング デプロイ スロットでアプリの変更を検証した後に、運用スロットにスワップできます。
* スロットにアプリをデプロイした後に運用サイトにスワップすると、運用サイトへのスワップ前にスロットのすべてのインスタンスが準備されます。 これにより、アプリをデプロイする際のダウンタイムがなくなります。 トラフィックのリダイレクトはシームレスであるため、スワップ操作によって破棄される要求はありません。 このワークフロー全体は、スワップ前の検証が必要ない場合、[自動スワップ](#Auto-Swap)を構成することで自動化できます。
* スワップ後も、以前のステージング アプリ スロットに元の運用アプリが残っているため、 運用スロットにスワップした変更が想定どおりでない場合は、適切な動作が確認されている元のサイトにすぐに戻すことができます。

サポートされるデプロイ スロット数は、App Service プラン レベルごとに異なります。 使用しているアプリのレベルでサポートされるスロット数を確認するには、「[App Service の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)」をご覧ください。 アプリを別のレベルにスケーリングするには、アプリが既に使用しているスロット数がターゲット レベルによってサポートされている必要があります。 たとえば、**Standard** レベルでは 5 つのデプロイ スロットのみがサポートされるので、アプリに 5 つを超えるスロットがある場合は **Standard** レベルにスケール ダウンできません。

<a name="Add"></a>

## <a name="add-slot"></a>スロットの追加
複数のデプロイ スロットを有効にするには、アプリが **Standard**、**Premium**、**Isolated** のいずれかのレベルで実行されている必要があります。

1. [Azure Portal](https://portal.azure.com/) でアプリの[リソース ページ](../azure-resource-manager/manage-resources-portal.md#manage-resources)を開きます。

2. 左側のナビゲーションで、**[デプロイ スロット (プレビュー)]** オプションを選択し、**[スロットの追加]** をクリックします。
   
    ![新しいデプロイ スロットの追加](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > アプリがまだ **Standard**、**Premium**、**Isolated** のいずれのレベルでもない場合は、ステージングされた発行を有効にするためのサポート対象のレベルを示すメッセージが表示されます。 その場合は、操作を継続する前に、**[アップグレード]** を選択してアプリの **[スケール]** タブに移動できます。
   > 

3. **[スロットの追加]** ダイアログで、スロット名を設定し、別の既存のデプロイ スロットからアプリ構成を複製するかどうかを選択します。 **[追加]** をクリックして続行します。
   
    ![構成元](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    既存のスロットから構成を複製できます。 複製できる設定には、アプリの設定、接続文字列、言語フレームワークのバージョン、Web ソケット、HTTP のバージョン、プラットフォームのビット数などがあります。

4. スロットを追加した後、**[閉じる]** をクリックしてダイアログを閉じます。 新しいスロットが **[デプロイ スロット (プレビュー)]** ページに表示されます。 既定では、新しいスロットの **[トラフィック %]** は 0 に設定され、顧客のトラフィックはすべて運用スロットにルーティングされます。

5. 新しいデプロイ スロットをクリックして、そのスロットのリソース ページを開きます。
   
    ![デプロイ スロットのタイトル](./media/web-sites-staged-publishing/StagingTitle.png)

    ステージング スロットには、他の App Service アプリと同様に管理ページがあります。 スロットの構成を変更することができます。 デプロイ スロットが表示されていることを通知するためにスロットの名前がページの上部に表示されます。

6. スロットのリソース ページで、アプリの URL をクリックします。 デプロイ スロットは独自のホスト名を持ち、ライブ アプリでもあります。 デプロイ スロットへのパブリック アクセスを制限するには、「[Azure App Service の IP 制限](app-service-ip-restrictions.md)」をご覧ください。

別のスロットから設定を複製した場合でも、新しいデプロイ スロットには内容がありません。 たとえば、 [Git を使用してこのスロットに発行する](app-service-deploy-local-git.md)ことができます。 スロットには、異なるリポジトリ分岐、または異なるリポジトリからデプロイできます。 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>スワップされる設定
別のデプロイ スロットから構成を複製する場合、複製された構成を編集することができます。 なお、スワップ先のコンテンツに反映される構成要素 (非スロット固有) もあれば、スワップ後にも同じスロットに維持される構成要素 (スロット固有) もあります。 次の一覧では、スロットのスワップ時に変更される設定を示します。

**スワップされる設定**:

* 一般設定 - フレームワーク バージョン、32/64 ビット、Web ソケットなど
* アプリ設定 (スロット固有として構成可能)
* 接続文字列 (スロット固有として構成可能)
* ハンドラー マッピング
* 監視と診断の設定
* パブリック証明書
* Web ジョブ コンテンツ
* ハイブリッド接続 *
* VNet 統合 *
* サービス エンドポイント *
* Azure CDN *

* でマークされた機能は、スロットに固定される予定です。 

**スワップされない設定**:

* 発行エンドポイント
* カスタム ドメイン名
* プライベート証明書と SSL のバインド
* スケールの設定
* Web ジョブ スケジューラ
* IP 制限
* 常時接続
* プロトコル設定 (HTTP**S**、TLS バージョン、クライアント証明書)
* 診断ログの設定
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

アプリ設定または接続文字列を特定のスロット固有として (スワップされないように) 構成するには、そのスロットの **[アプリケーションの設定]** ページに移動し、スロット固有にする構成要素の **[スロット設定]** ボックスをオンにします。 構成要素をスロット固有としてマークすると、スワップできないことが App Service に通知されます。 

![スロットの設定](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>2 つのスロットをスワップする 
アプリの **[デプロイ スロット (プレビュー)]** ページで、デプロイ スロットをスワップできます。 

**[概要]** ページや **[デプロイ スロット]** ページでスロットをスワップすることもできますが、現在は古いエクスペリエンスが提供されます。 このガイドでは、**[デプロイ スロット (プレビュー)]** ページの新しいユーザー インターフェイスを使用する方法を示します。

> [!IMPORTANT]
> アプリをデプロイ スロットから運用環境にスワップする前に、すべての設定が、スワップ ターゲットへと反映させたいとおりに構成されていることを確認してください。
> 
> 

デプロイ スロットをスワップするには、次の手順のようにします。

1. アプリの **[デプロイ スロット (プレビュー)]** ページに移動し、**[スワップ]** をクリックします。
   
    ![[スワップ] ボタン](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **[スワップ]** ダイアログに、選択したソース スロットとターゲット スロットの変更される設定が表示されます。

2. 目的の **[ソース]** および **[ターゲット]** スロットを選択します。 通常、ターゲットは運用スロットになります。 また、**[ソースの変更]** タブと **[ターゲットの変更]** タブをクリックして、構成の変更が予想されることを確認します。 完了したら、**[スワップ]** をクリックしてすぐにスロットをスワップすることができます。

    ![完全なスワップ](./media/web-sites-staged-publishing/SwapImmediately.png)

    スワップを実際に行う前に、新しい設定でのターゲット スロットの動作を確認するには、**[スワップ]** をクリックしないで、「[プレビューでのスワップ](#Multi-Phase)」の指示に従います。

3. 完了したら、**[閉じる]** をクリックしてダイアログを閉じます。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>プレビューでのスワップ (複数フェーズのスワップ)

> [!NOTE]
> プレビューでのスワップは、Linux 上の Web アプリではサポートされていません。

ターゲット スロットとして運用環境にスワップする前に、スワップした設定でのアプリの実行を検証します。 ソース スロットはスワップの完了前にウォームアップされ、これはミッション クリティカルなアプリケーションにも適しています。

プレビューでスワップを開始すると、App Service では次のことが行われます。

- スロット上の既存のワークロード (運用など) が影響を受けないように、ターゲット スロットは変更されません。
- スロット固有の接続文字列やアプリの設定など、ターゲット スロットの構成要素がソース スロットに適用されます。
- これらの構成要素を使って、ソース スロットのワーカー プロセスが再起動されます。 ソース スロットを参照し、変更した構成でのアプリの実行を確認できます。

別の手順でスワップを完了した場合、App Service により、ウォームアップされたソース スロットがターゲット スロットに移動され、ターゲット スロットがソース スロットに移動されます。 スワップをキャンセルすると、ソース スロットの構成要素がソース スロットに再び適用されます。

プレビューでスワップを行うには、次の手順のようにします。

1. 「[デプロイ スロットをスワップする](#Swap)」の手順に従いますが、**[プレビューでスワップを実行]** を選択します。

    ![プレビューでのスワップ](./media/web-sites-staged-publishing/SwapWithPreview.png)

    ダイアログに、フェーズ 1 でのソース スロットの構成の変更方法と、フェーズ 2 でのソース スロットとターゲット スロットの変更方法が表示されます。

2. スワップを開始する準備ができたら、**[スワップの開始]** をクリックします。

    フェーズ 1 が完了すると、ダイアログで通知されます。 `https://<app_name>-<source-slot-name>.azurewebsites.net` に移動して、ソース スロットでのスワップをプレビューします。 

3. 保留中のスワップを完了する準備ができたら、**[スワップ アクション]** で **[スワップの完了]** を選択し、**[スワップの完了]** をクリックします。

    保留中のスワップを取り消すには、代わりに **[スワップの取り消し]** を選択して、**[スワップの取り消し]** をクリックします。

4. 完了したら、**[閉じる]** をクリックしてダイアログを閉じます。

複数フェーズのスワップを自動化するには、PowerShell を使用した自動化についてのページをご覧ください。

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>スワップをロールバックする
スロットのスワップ後にターゲット スロット (運用スロットなど) でエラーが発生する場合は、同じ 2 つのスロットをすぐにスワップして、スロットをスワップ前の状態に復元します。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>自動スワップの構成

> [!NOTE]
> 自動スワップは、Linux 上の Web アプリではサポートされていません。

自動スワップを使うと、アプリのエンド ユーザーにコールド スタートやダウンタイムを強制することなく、アプリを連続的にデプロイできる、効率的な DevOps シナリオを実現できます。 スロットを運用環境に自動スワップするよう設定すると、コードの変更をそのスロットにプッシュするたびに、ソース スロットでウォームアップされた後、App Service によってアプリが運用環境に自動的にスワップされます。

   > [!NOTE]
   > 運用スロットの自動スワップを構成する前に、まず非運用環境のターゲット スロットで自動スワップをテストしてみてください。
   > 

自動スワップを構成するには、次の手順のようにします。

1. アプリのリソース ページに移動します。 **[デプロイ スロット (プレビュー)]** > \<*目的のソース スロット*> > **[アプリケーション設定]** の順に選択します。
   
2. **[自動スワップ]** で **[オン]** を選択し、**[自動スワップ スロット]** で目的のターゲット スロットを選択して、コマンド バーの **[保存]** をクリックします。 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. ソース スロットへのコードのプッシュを実行します。 しばらくすると自動スワップが実行され、更新がターゲット スロットの URL に反映されます。

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>カスタム ウォームアップ
[自動スワップ](#Auto-Swap)を使用するとき、一部のアプリではスワップ前にカスタム ウォームアップ アクションが必要な場合があります。 web.config の `applicationInitialization` 構成要素を使用して、実行されるカスタム初期化アクションを指定できます。 スワップ操作では、このカスタム ウォームアップが完了するまで待ってから、ターゲット スロットとのスワップが行われます。 サンプルの web.config フラグメントを次に示します。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

`applicationInitialization` 要素のカスタマイズの詳細については、「[Most common deployment slot swap failures and how to fix them (最も一般的なデプロイ スロットのスワップ エラーとその修正方法)](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)」を参照してください。

次の 1 つまたは複数の[アプリ設定](web-sites-configure.md)でウォームアップ動作をカスタマイズすることもできます。

- `WEBSITE_SWAP_WARMUP_PING_PATH`:サイトをウォームアップするための ping へのパス。 このアプリ設定を追加するには、値としてスラッシュで始まるカスタム パスを指定します。 たとえば、「 `/statuscheck` 」のように入力します。 既定値は `/` です。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`:ウォーム アップ操作の有効な HTTP 応答コード。 HTTP コードのコンマ区切りの一覧で、このアプリ設定を追加します。 たとえば、「`200,202`」のように指定します。 返された状態コードが一覧にない場合、ウォーム アップおよびスワップ操作が停止します。 既定で、すべての応答コードは有効です。

## <a name="monitor-swap"></a>スワップを監視する

スワップ操作が完了するまで長い時間がかかる場合、[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)でスワップ操作に関する情報を取得できます。

ポータルのアプリのリソース ページで、左側のナビゲーションの **[アクティビティ ログ]** を選択します。

スワップ操作がログ クエリに `Swap Web App Slots` として表示されます。 これを展開し、副操作やエラーの 1 つを選択して詳細を表示できます。

## <a name="route-traffic"></a>トラフィックをルーティングする

既定では、アプリの運用環境の URL (`http://<app_name>.azurewebsites.net`) に対するすべてのクライアント要求は、運用スロットにルーティングされます。 トラフィックの一部を、別のスロットにルーティングできます。 この機能は、新しい更新についてのユーザーのフィードバックが必要であっても、運用環境にリリースできる状態ではない場合に便利です。

### <a name="route-production-traffic-automatically"></a>運用トラフィックを自動的にルーティングする

運用トラフィックを自動的にルーティングするには、次の手順のようにします。

1. アプリのリソース ページに移動し、**[デプロイ スロット (プレビュー)]** を選択します。

2. ルーティング先のスロットの **[トラフィック %]** 列で、ルーティングするトラフィックの合計量を表す割合 (0 ～ 100) を指定します。 **[Save]** をクリックします。

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

設定を保存すると、指定した割合のクライアントが、非運用スロットにランダムにルーティングされます。 

一度特定のスロットに対して自動的にルーティングされたクライアントは、そのクライアント セッションの有効期間中、そのスロットに "固定" されます。 クライアントのブラウザーで、HTTP ヘッダー内の `x-ms-routing-name` Cookie を調べることにより、セッションが固定されているスロットを確認できます。 "ステージング" スロットにルーティングされる要求には、`x-ms-routing-name=staging` という Cookie が設定されています。 運用スロットにルーティングされる要求には、`x-ms-routing-name=self` という Cookie が設定されています。

### <a name="route-production-traffic-manually"></a>運用トラフィックを手動でルーティングする

App Service では、トラフィックの自動ルーティングだけでなく、特定のスロットに要求をルーティングすることもできます。 この方法は、ベータ版アプリへの参加をユーザーが自由に選択または拒否できるようにしたい場合に有効です。 運用トラフィックを手動でルーティングするには、`x-ms-routing-name` クエリ パラメーターを使用します。

ベータ版アプリへの参加をユーザーが拒否できるようにする方法としては、たとえば以下のようなリンクを Web ページに設置することが考えられます。

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

文字列 `x-ms-routing-name=self` に指定されているのは運用スロットです。 このリンクにアクセスしたクライアント ブラウザーは運用スロットにリダイレクトされます。加えて、それ以降の要求には、セッションを運用スロットに固定する `x-ms-routing-name=self` Cookie が格納されます。

ベータ版アプリへの参加をユーザーが許可できるようにするには、同じクエリ パラメーターに、非運用スロットの名前を設定します。以下に示したのはその例です。

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

<a name="Delete"></a>

## <a name="delete-slot"></a>スロットを削除する

アプリのリソース ページに移動します。 **[デプロイ スロット (プレビュー)]** > \<*削除するスロット*> > **[概要]** の順に選択します。 コマンド バーの **[削除]** をクリックします。  

![デプロイ スロットの削除](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>PowerShell で自動化する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell は、Windows PowerShell から Azure を管理するためのコマンドレットを提供するモジュールです (Azure App Service のデプロイ スロットを管理するためのサポートなど)。

Azure PowerShell のインストールと構成、Azure サブスクリプションを使用した Azure PowerShell の認証については、「 [Microsoft Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。  

- - -
### <a name="create-web-app"></a>Web アプリの作成
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>スロットを作成する
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>プレビューでのスワップ (複数フェーズのスワップ) を開始し、送信先スロットの構成をソース スロットに適用する
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>保留中のスワップ (レビューでのスワップ) を取り消し、ソースのスロットの構成を復元する
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>デプロイ スロットをスワップする
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>アクティビティ ログでスワップ イベントを監視する
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>スロットを削除する
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>CLI で自動化する

デプロイ スロット用の [Azure CLI](https://github.com/Azure/azure-cli) コマンドについては、「[az webapp deployment slot](/cli/azure/webapp/deployment/slot)」をご覧ください。

## <a name="next-steps"></a>次の手順
[非運用スロットへのアクセスをブロックする](app-service-ip-restrictions.md)
