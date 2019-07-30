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
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445596"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service でステージング環境を設定する
<a name="Overview"></a>

実行している App Service プランのサービス レベルが **Standard**、**Premium**、または **Isolated** である場合は、Web アプリ、Linux 上の Web アプリ、モバイル バック エンド、または API アプリを [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) にデプロイするときに、既定の運用スロットではなく別個のデプロイ スロットを使用できます。 デプロイ スロットは、固有のホスト名を持つライブ アプリです。 アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つのデプロイ スロットの間でスワップすることができます。 

非運用スロットにアプリケーションをデプロイすることには、次のメリットがあります。

* ステージング デプロイ スロットでアプリの変更を検証した後に、運用スロットにスワップできます。
* スロットにアプリをデプロイした後に運用サイトにスワップすると、運用サイトへのスワップ前にスロットのすべてのインスタンスが準備されます。 これにより、アプリをデプロイする際のダウンタイムがなくなります。 トラフィックのリダイレクトはシームレスであるため、スワップ操作によって破棄される要求はありません。 スワップ前の検証が必要ない場合は、[自動スワップ](#Auto-Swap)を構成することで、このワークフロー全体を自動化できます。
* スワップ後も、以前のステージング アプリ スロットに元の運用アプリが残っているため、 運用スロットにスワップした変更が想定どおりでない場合は、適切な動作が確認されている元のサイトにすぐに戻すことができます。

サポートされるデプロイ スロット数は、App Service プラン レベルごとに異なります。 デプロイ スロットは追加料金なしでご利用いただけます。 使用しているアプリのサービス レベルでサポートされるスロット数を確認するには、「[App Service の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)」をご覧ください。 

アプリを別のサービス レベルにスケールするには、アプリが既に使用しているスロット数がターゲット レベルによってサポートされていることを確認します。 たとえば、**Standard** レベルではサポートされるデプロイ スロット数は 5 つのみなので、アプリのスロット数が 5 つより多い場合は、**Standard** レベルにスケール ダウンできません。 

<a name="Add"></a>

## <a name="add-a-slot"></a>スロットを追加する
複数のデプロイ スロットを有効にするには、アプリが **Standard**、**Premium**、**Isolated** のいずれかのレベルで実行されている必要があります。

1. [Azure Portal](https://portal.azure.com/) でアプリの[リソース ページ](../azure-resource-manager/manage-resources-portal.md#manage-resources)を開きます。

2. 左側のウィンドウで、 **[デプロイ スロット]**  >  **[スロットの追加]** と選択します。
   
    ![新しいデプロイ スロットの追加](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > アプリのサービス レベルがまだ **Standard**、**Premium**、または **Isolated** でない場合は、ステージングされた発行を有効にすることがサポートされているレベルを示すメッセージが表示されます。 この時点で、操作を続行する前に、 **[アップグレード]** を選択してアプリの **[スケール]** タブに移動するオプションが用意されています。
   > 

3. **[スロットの追加]** ダイアログ ボックスで、スロット名を指定し、別のデプロイ スロットからアプリ構成を複製するかどうかを選択します。 **[追加]** を選択して続行します。
   
    ![構成元](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    構成は、既存のどのスロットからも複製できます。 複製できる設定には、アプリの設定、接続文字列、言語フレームワークのバージョン、Web ソケット、HTTP のバージョン、プラットフォームのビット数などがあります。

4. スロットを追加した後、 **[閉じる]** を選択してダイアログ ボックスを閉じます。 これで新しいスロットが **[デプロイ スロット]** ページに表示されます。 既定では、新しいスロットの **[トラフィック %]** は 0 に設定され、顧客のトラフィックはすべて運用スロットにルーティングされます。

5. 新しいデプロイ スロットを選択して、そのスロットのリソース ページを開きます。
   
    ![デプロイ スロットのタイトル](./media/web-sites-staged-publishing/StagingTitle.png)

    ステージング スロットには、他の App Service アプリと同様に管理ページがあります。 スロットの構成を変更することができます。 デプロイ スロットが表示されていることを通知するためにスロットの名前がページの上部に表示されます。

6. スロットのリソース ページで、アプリの URL を選択します。 デプロイ スロットは独自のホスト名を持ち、ライブ アプリでもあります。 デプロイ スロットへのパブリック アクセスを制限するには、[Azure App Service の IP 制限](app-service-ip-restrictions.md)に関するページをご覧ください。

別のスロットから設定を複製した場合でも、新しいデプロイ スロットには内容がありません。 たとえば、[Git を使用してこのスロットに発行する](app-service-deploy-local-git.md)ことができます。 スロットには、異なるリポジトリ分岐、または異なるリポジトリからデプロイできます。 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>スワップ中の動作

### <a name="swap-operation-steps"></a>スワップ操作の手順

2 つのスロットを (通常はステージング スロットから運用スロットに) スワップするときには、ターゲット スロットでダウンタイムが発生しないようにするため、App Service が以下のことを行います。

1. ターゲット スロット (たとえば運用スロット) からソース スロットのすべてのインスタンスに対して、以下の設定を適用します。 
    - [スロット固有](#which-settings-are-swapped)のアプリ設定と接続文字列 (該当する場合)。
    - [継続的デプロイ](deploy-continuous-deployment.md)の設定 (有効になっている場合)。
    - [App Service の認証](overview-authentication-authorization.md)の設定 (有効になっている場合)。
    
    これらのどの場合も、ソース スロット内のすべてのインスタンスがトリガーされます。 [プレビューでのスワップ](#Multi-Phase)時には、これは最初のフェーズが終了したことを示します。 スワップ操作が一時停止されると、ソース スロットが、ターゲット スロットの設定で正しく動作していることを検証できます。

1. ソース スロット内のすべてのインスタンスが再起動を完了するまで待機します。 いずれかのインスタンスが再起動に失敗した場合、スワップ操作ではすべての変更がソース スロットに戻されて、操作が停止されます。

1. [ローカル キャッシュ](overview-local-cache.md)が有効になっている場合は、ソース スロットの各インスタンスのアプリケーション ルート ("/") に対して HTTP 要求を行うことで、ローカル キャッシュの初期化をトリガーします。 各インスタンスが何らかの HTTP 応答を返すまで待機します。 ローカル キャッシュの初期化により、各インスタンスでもう一度再起動が発生します。

1. [カスタム ウォームアップ](#Warm-up)によって[自動スワップ](#Auto-Swap)が有効になっている場合は、ソース スロットの各インスタンスのアプリケーション ルート ("/") に対して HTTP 要求を行うことで、[アプリケーションの初期化](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)をトリガーします。

    `applicationInitialization` が指定されていない場合は、各インスタンスのソース スロットのアプリケーション ルートへの HTTP 要求をトリガーします。 
    
    インスタンスが任意の HTTP 応答を返すと、ウォームアップされたと見なされます。

1. ソース スロットのすべてのインスタンスが正常にウォームアップされたら、2 つのスロットのルーティング規則を入れ換えることで 2 つのスロットをスワップします。 この手順の後は、前にソース スロットでウォーム アップされたアプリはターゲット スロット (運用スロットなど) に存在します。

1. この時点でソース スロットには、スワップ以前にはターゲット スロットにあったアプリがあるため、すべての設定を適用してインスタンスを再起動することで、同じ操作を実行します。

スワップ操作のどの時点でも、スワップされるアプリを初期化するすべての作業はソース スロットで発生しています。 ソース スロットが準備され、ウォームアップされている間、スワップがどこで成功するか失敗するかに関わらず、ターゲット スロットはオンライン状態に留まります。 ステージング スロットと運用スロットを入れ換える場合は常に、運用スロットがターゲット スロットであるようにします。 こうすることで、スワップ操作が運用アプリに影響を及ぼしません。

### <a name="which-settings-are-swapped"></a>スワップされる設定
別のデプロイ スロットから構成を複製する場合、複製された構成を編集することができます。 構成要素には、スワップを経ても内容が反映される (スロット固有でない) ものもあれば、スワップ後に同じスロットに残されている (スロット固有の) ものもあります。 次の一覧では、スロットのスワップ時に変更される設定を示します。

**スワップされる設定**:

* 一般設定 (フレームワーク バージョン、32/64 ビット、Web ソケットなど)
* アプリ設定 (スロット固有として構成可能)
* 接続文字列 (スロット固有として構成可能)
* ハンドラー マッピング
* 監視と診断の設定
* パブリック証明書
* Web ジョブ コンテンツ
* ハイブリッド接続 *
* Virtual Network 統合 *
* サービス エンドポイント *
* Azure Content Delivery Network *

アスタリスク (*) 記号付きの機能は、スロット固有とされる予定です。 

**スワップされない設定**:

* 発行エンドポイント
* カスタム ドメイン名
* プライベート証明書と SSL のバインド
* スケールの設定
* Web ジョブ スケジューラ
* IP 制限
* 常時接続
* プロトコル設定 (HTTPS、TLS バージョン、クライアント証明書)
* 診断ログの設定
* クロスオリジン リソース共有 (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

特定のスロットに固有の (スワップされない) アプリ設定や接続文字列を構成するには、そのスロットの **[構成]** ページに移動します。 設定を追加または編集し、 **[deployment slot setting]\(スロット設定のデプロイ\)** を選択します。 このチェック ボックスを選択して、設定がスワップ可能ではないことを App Service に指示します。 

![スロットの設定](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>2 つのスロットをスワップする 
アプリの **[デプロイ スロット]** ページと **[概要]** ページで、デプロイ スロットをスワップできます。 スロットのスワップに関する技術的詳細については、「[スワップ中の動作](#AboutConfiguration)」を参照してください。

> [!IMPORTANT]
> アプリをデプロイ スロットから運用環境にスワップする前に、運用環境がターゲット スロットになっていること、およびソース スロットのすべての設定が、運用環境に反映させたい内容で正確に構成されていることを確認してください。
> 
> 

デプロイ スロットをスワップするには:

1. アプリの **[デプロイ スロット]** ページに移動し、 **[スワップ]** を選択します。
   
    ![[スワップ] ボタン](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **[スワップ]** ダイアログ ボックスに、選択したソース スロットとターゲット スロットの変更される設定が表示されます。

2. 目的の **[ソース]** および **[ターゲット]** スロットを選択します。 通常、ターゲットは運用スロットになります。 また、 **[ソースの変更]** タブと **[ターゲットの変更]** タブを選択して、構成の変更が予定されてることを確認します。 完了したら、 **[スワップ]** を選択することで直ちにスロットをスワップできます。

    ![完全なスワップ](./media/web-sites-staged-publishing/SwapImmediately.png)

    スワップを実際に行う前に、新しい設定でのターゲット スロットの動作を確認するには、 **[スワップ]** を選択しないで、「[プレビューでのスワップ](#Multi-Phase)」の指示に従います。

3. 完了したら、 **[閉じる]** を選択してダイアログ ボックスを閉じます。

問題がある場合は、「[スワップのトラブルシューティングを行う](#troubleshoot-swaps)」を参照してください。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>プレビューでのスワップ (複数フェーズのスワップ)

> [!NOTE]
> プレビューでのスワップは、Linux 上の Web アプリではサポートされていません。

ターゲット スロットとしての運用環境にスワップする前に、スワップ後の設定でアプリの実行を検証します。 ソース スロットは、スワップの完了前にウォームアップもされているため、ミッション クリティカルなアプリケーションに適しています。

プレビューでのスワップを実行すると、App Service は同じ[スワップ操作](#AboutConfiguration)を実行しますが、最初の手順の後に、一時停止します。 その後、スワップを完了する前にステージング スロットでの結果を検証できます。 

スワップをキャンセルすると、構成要素がソース スロットに再適用されます。

プレビューでのスワップを行うには:

1. 「[デプロイ スロットをスワップする](#Swap)」の手順に従いますが、 **[プレビューでスワップを実行]** を選択します。

    ![プレビューでのスワップ](./media/web-sites-staged-publishing/SwapWithPreview.png)

    ダイアログ ボックスに、フェーズ 1 でソース スロットの構成がどのように変わり、フェーズ 2 でソース スロットとターゲット スロットがどのように変わるかが表示されます。

2. スワップを開始する準備ができたら、 **[スワップの開始]** を選択します。

    フェーズ 1 が終了すると、ダイアログ ボックスで通知されます。 `https://<app_name>-<source-slot-name>.azurewebsites.net` に移動して、ソース スロットでのスワップをプレビューします。 

3. 保留中のスワップを完了する準備ができたら、 **[スワップ アクション]** で **[スワップの完了]** を選択し、 **[スワップの完了]** を選択します。

    保留中のスワップを取り消すには、代わりに **[スワップの取り消し]** を選択します。

4. 完了したら、 **[閉じる]** を選択してダイアログ ボックスを閉じます。

問題がある場合は、「[スワップのトラブルシューティングを行う](#troubleshoot-swaps)」を参照してください。

複数フェーズのスワップを自動化するには、「[PowerShell で自動化する](#automate-with-powershell)」をご覧ください。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>スワップをロールバックする
スロットのスワップ後にターゲット スロット (運用スロットなど) でエラーが発生する場合は、同じ 2 つのスロットをすぐにスワップして、スロットをスワップ前の状態に復元します。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>自動スワップを構成する

> [!NOTE]
> 自動スワップは、Linux 上の Web アプリではサポートされていません。

自動スワップを使うと、アプリのユーザーにコールド スタートを課したりダウンタイムを生じさせたりすることなく、アプリを連続的にデプロイする効率的な Azure DevOps シナリオを実現できます。 あるスロットから運用環境への自動スワップが有効になっていると、コードの変更をそのスロットにプッシュするたびに、ソース スロットでウォームアップされた後、App Service によって自動的に、[アプリが運用環境にスワップ](#swap-operation-steps)されます。

   > [!NOTE]
   > 運用スロットの自動スワップを構成する前に、非運用環境のターゲット スロットで自動スワップをテストすることを検討してください。
   > 

自動スワップを構成するには:

1. アプリのリソース ページに移動します。 **[デプロイ スロット]**  >  *[\<目的のソース スロット>]*  >  **[構成]**  >  **[全般設定]** と選択します。
   
2. **[Auto swap enabled]\(自動スワップ有効化\)** で、 **[オン]** を選択します。 **[Auto swap deployment slot]\(自動スワップのデプロイ スロット\)** で目的のターゲット スロットを選択し、コマンド バーで **[保存]** を選択します。 
   
    ![自動スワップを構成するための選択](./media/web-sites-staged-publishing/AutoSwap02.png)

3. ソース スロットへのコードのプッシュを実行します。 しばらくすると自動スワップが実行され、更新がターゲット スロットの URL に反映されます。

問題がある場合は、「[スワップのトラブルシューティングを行う](#troubleshoot-swaps)」を参照してください。

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>カスタム ウォームアップを指定する
[自動スワップ](#Auto-Swap)を使おうとしているときに、一部のアプリでは、スワップ前のカスタム ウォームアップ アクションが必要な場合があります。 web.config の `applicationInitialization` 構成要素を使用して、カスタム初期化アクションを指定できます。 [スワップ操作](#AboutConfiguration)では、このカスタム ウォームアップの終了を待ってから、ターゲット スロットとのスワップが行われます。 以下に、サンプルの web.config フラグメントを示します。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

`applicationInitialization` 要素のカスタマイズの詳細については、「[Most common deployment slot swap failures and how to fix them (最も一般的なデプロイ スロットのスワップ エラーとその修正方法)](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)」を参照してください。

次の[アプリ設定](configure-common.md)の 1 つまたは両方を使用して、ウォームアップの動作をカスタマイズすることもできます。

- `WEBSITE_SWAP_WARMUP_PING_PATH`:サイトをウォームアップするための ping へのパス。 このアプリ設定を追加するには、値としてスラッシュで始まるカスタム パスを指定します。 例: `/statuscheck`。 既定値は `/` です。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`:ウォーム アップ操作の有効な HTTP 応答コード。 HTTP コードのコンマ区切りの一覧で、このアプリ設定を追加します。 たとえば `200,202` とします。 返された状態コードが一覧にない場合、ウォームアップとスワップの操作が停止されます。 既定で、すべての応答コードは有効です。

問題がある場合は、「[スワップのトラブルシューティングを行う](#troubleshoot-swaps)」を参照してください。

## <a name="monitor-a-swap"></a>スワップを監視する

[スワップ操作](#AboutConfiguration)が完了するまで長い時間がかかる場合、[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)でスワップ操作に関する情報を取得できます。

ポータルのアプリのリソース ページで、左側のウィンドウの **[アクティビティ ログ]** を選択します。

スワップ操作がログ クエリに `Swap Web App Slots` として表示されます。 これを展開し、副操作やエラーの 1 つを選択して詳細を表示できます。

## <a name="route-traffic"></a>トラフィックをルーティングする

既定では、アプリの運用環境の URL (`http://<app_name>.azurewebsites.net`) に対するすべてのクライアント要求は、運用スロットにルーティングされます。 トラフィックの一部を、別のスロットにルーティングできます。 この機能は、新しい更新についてのユーザーのフィードバックが必要であっても、運用環境にリリースできる状態ではない場合に便利です。

### <a name="route-production-traffic-automatically"></a>運用トラフィックを自動的にルーティングする

運用トラフィックを自動的にルーティングするには:

1. アプリのリソース ページに移動し、 **[デプロイ スロット]** を選択します。

2. ルーティング先のスロットの **[トラフィック %]** 列で、ルーティングするトラフィックの合計量を表す割合 (0 ～ 100) を指定します。 **[保存]** を選択します。

    ![トラフィックの割合の設定](./media/web-sites-staged-publishing/RouteTraffic.png)

設定の保存後は、指定した割合のクライアントが、非運用スロットにランダムにルーティングされます。 

クライアントは、特定のスロットに自動的にルーティングされると、そのクライアント セッションの有効期間中はそのスロットに "固定" されます。 クライアントのブラウザーで、HTTP ヘッダー内の `x-ms-routing-name` Cookie を調べることにより、セッションが固定されているスロットを確認できます。 "ステージング" スロットにルーティングされる要求には、`x-ms-routing-name=staging` という Cookie が設定されています。 運用スロットにルーティングされる要求には、`x-ms-routing-name=self` という Cookie が設定されています。

### <a name="route-production-traffic-manually"></a>運用トラフィックを手動でルーティングする

App Service では、トラフィックの自動ルーティングだけでなく、特定のスロットに要求をルーティングすることもできます。 この方法は、ユーザーがベータ版アプリの利用を選択または拒否できるようにしたい場合に便利です。 運用トラフィックを手動でルーティングするには、`x-ms-routing-name` クエリ パラメーターを使用します。

ベータ版アプリの利用をユーザーが拒否できるようにするには、たとえば次のようなリンクを Web ページに配置します。

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

文字列 `x-ms-routing-name=self` に指定されているのは運用スロットです。 クライアント ブラウザーは、リンクにアクセスすると、運用スロットにリダイレクトされます。 以降のすべての要求には、セッションをその運用スロットに固定する `x-ms-routing-name=self` cookie が含まれます。

ベータ版アプリの利用をユーザーが選択できるようにするには、同じクエリ パラメーターを、非運用スロットの名前に設定します。 次に例を示します。

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

既定では、新しいスロットには、`0%` のルーティング規則がグレーで表示されます。 この値を明示的に `0%` に設定すると (黒のテキストで表示されます)、ユーザーは `x-ms-routing-name` クエリ パラメーターを使用して、ステージング スロットに手動でアクセスできます。 しかし、ルーティングの割合は 0 に設定されているため、ユーザーはスロットに自動的にルーティングされません。 これは、内部チームにスロットでの変更のテストを許可する一方で、ステージング スロットをパブリックから "非表示" にできる高度なシナリオです。

<a name="Delete"></a>

## <a name="delete-a-slot"></a>スロットを削除する

アプリのリソース ページに移動します。 **[デプロイ スロット]**  > \<*削除するスロット>*  >  **[概要]** の順に選択します。 コマンド バーの **[削除]** を選択します。  

![デプロイ スロットの削除](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>PowerShell で自動化する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell は、Windows PowerShell から Azure を管理するためのコマンドレットを提供するモジュールです (Azure App Service のデプロイ スロットを管理するためのサポートなど)。

Azure PowerShell のインストールと構成、Azure サブスクリプションを使用した Azure PowerShell の認証については、「 [Microsoft Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。  

---
### <a name="create-a-web-app"></a>Web アプリを作成する
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>スロットを作成する
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>プレビューでのスワップ (複数フェーズのスワップ) を開始し、送信先スロットの構成をソース スロットに適用する
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>保留中のスワップ (レビューでのスワップ) を取り消し、ソースのスロットの構成を復元する
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>デプロイ スロットをスワップする
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>アクティビティ ログでスワップ イベントを監視する
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>スロットを削除する
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>CLI で自動化する

デプロイ スロット用の [Azure CLI](https://github.com/Azure/azure-cli) コマンドについては、「[az webapp deployment slot](/cli/azure/webapp/deployment/slot)」をご覧ください。

## <a name="troubleshoot-swaps"></a>スワップのトラブルシューティングを行う

[スロットのスワップ](#AboutConfiguration)中に何らかのエラーが発生した場合、そのエラーは *D:\home\LogFiles\eventlog.xml* にログ記録されます。 アプリケーション固有のエラー ログにも記録されます。

以下に、スワップの一般的なエラーをいくつか示します。

- アプリケーション ルートへの HTTP 要求には時間枠が設けられています。 スワップ操作は、HTTP 要求ごとに 90 秒間待機し、最大 5 回再試行します。 すべての再試行がタイムアウトになると、スワップ操作は停止されます。

- アプリのコンテンツがローカル キャッシュ用に指定されたローカル ディスクのクォータを超過すると、ローカル キャッシュの初期化が失敗することがあります。 詳細については、[ローカル キャッシュの概要](overview-local-cache.md)に関するページを参照してください。

- [カスタム ウォームアップ](#Warm-up)時には、HTTP 要求は (外部 URL を経由することなく) 内部的に作成されます。 *Web.config* 内に特定の URL 書き換えルールがあると、それらが失敗する可能性があります。たとえば、ドメイン名をリダイレクトするルールや HTTPS を適用するルールは、ウォームアップ要求がアプリ コードに到達するのを妨げることがあります。 この問題を回避するには、以下のように 2 つの条件を追加して、書き換えルールを変更します。

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- カスタム ウォームアップを行わなくても、URL 書き換えルールが HTTP 要求をブロックする場合があります。 この問題を回避するには、以下のように条件を追加して、書き換えルールを変更します。

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 一部の [IP 制限ルール](app-service-ip-restrictions.md)により、スワップ操作でのアプリへの HTTP 要求の送信が妨げられる可能性があります。 `10.` および `100.` で始まる IPv4 アドレスの範囲は、デプロイに対して内側です。 これらにアプリへの接続を許可する必要があります。

## <a name="next-steps"></a>次の手順
[非運用スロットへのアクセスをブロックする](app-service-ip-restrictions.md)
