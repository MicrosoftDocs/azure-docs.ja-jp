---
title: Azure で VM 用にスケジュールされたイベントを監視する
description: Azure 仮想マシンでスケジュールされているイベントを監視する方法について説明します。
author: mysarn
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 866522da162d22621bd37bf9d2f2fa6838206e17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674687"
---
# <a name="monitor-scheduled-events-for-your-azure-vms"></a>Azure VM のスケジュールされたイベントを監視する

更新プログラムは Azure のさまざまな部分に毎日適用され、そこで実行されているサービスをセキュリティで保護された最新の状態に保ちます。 計画された更新に加えて、計画外のイベントが発生することもあります。 たとえば、ハードウェアの性能低下や障害が検出された場合、Azure サービスでは計画外メンテナンスの実行が必要になることがあります。 ライブ マイグレーションとメモリ保持更新を使用し、通常は更新の影響を厳密にチェックするため、ほとんどの場合、これらのイベントは顧客に対してほぼ透過的に行われ、影響を与えることはなく、仮想マシンがせいぜい数秒凍結するくらいです。 ただし、アプリケーションによっては、仮想マシンが数秒凍結しただけで影響を受ける可能性があります。 こうしたアプリケーションの最適なエクスペリエンスを確保するためにも、今後の Azure メンテナンスについて事前に把握しておくことが重要です。 [Scheduled Events サービス](scheduled-events.md)には、今後のメンテナンスに関する通知を受けるためのプログラマティック インターフェイスが用意されているため、メンテナンスに適切に対応できます。 

この記事では、スケジュールされたイベントを使用して、ご利用の VM に影響を与える可能性があるメンテナンス イベントについて通知を受け、さらに監視と分析に役立つ基本的なオートメーションを構築する方法について説明します。


## <a name="routing-scheduled-events-to-log-analytics"></a>スケジュールされたイベントを Log Analytics にルーティングする

Scheduled Events は、すべての Azure 仮想マシン上で使用できる [Azure Instance Metadata Service](instance-metadata-service.md) の一部として提供されます。 お客様は、仮想マシンのエンドポイントに対してクエリを実行することで、スケジュールされたメンテナンス通知を検索して、状態の保存や仮想マシンのローテーションからの除外などの軽減策を実行するといった、自動化を作成できます。 Scheduled Events を記録する自動化を作成することをお勧めします。これにより、Azure メンテナンス イベントの監査ログを取得できます。 

この記事では、メンテナンスの Scheduled Events を Log Analytics にキャプチャする方法について説明します。 さらに、いくつかの基本的な通知アクションをトリガーします。たとえば、所属チームへのメール送信や、ご利用の仮想マシンに影響を与えているすべてのイベントの履歴ビューの取得などがあります。 ここではイベントの集計と自動化に [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) を使用しますが、これらのログの収集と自動化のトリガーには任意の監視ソリューションを使用できます。

![イベントのライフサイクルを示す図](./media/notifications/events.png)

## <a name="prerequisites"></a>前提条件

この例では、[可用性セットに Windows 仮想マシン](tutorial-availability-sets.md)を作成する必要があります。 Scheduled Events から、可用性セット、クラウド サービス、仮想マシン スケール セット、またはスタンドアロン VM 内の仮想マシンのいずれかに影響する可能性がある変更について通知が提供されます。 スケジュールされたイベントを、コレクターとして機能するいずれか 1 つの VM 上でポーリングする[サービス](https://github.com/microsoft/AzureScheduledEventsService)を実行して、可用性セット内の他のすべての VM のイベントを取得します。    

このチュートリアルの終了時に、グループ リソース グループを削除しないでください。

また、可用性セット内の VM からの情報を集約するために [Log Analytics ワークスペースを作成](../../azure-monitor/logs/quick-create-workspace.md)する必要があります。

## <a name="set-up-the-environment"></a>環境をセットアップする

これで、可用性セット内に 2 つの初期 VM が作成されました。 次に、同じ可用性セット内に `myCollectorVM` という 3 番目の VM を作成する必要があります。 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

[GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip) からプロジェクトのインストール .zip ファイルをダウンロードします。

**myCollectorVM** に接続し、.zip ファイルをその仮想マシンにコピーして、すべてのファイルを抽出します。 ご利用の VM 上で PowerShell プロンプトを開きます。 `SchService.ps1` が含まれるフォルダー (例: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`) にプロンプトを移動し、サービスを設定します。

```powershell
.\SchService.ps1 -Setup
```

サービスを開始します。

```powershell
.\SchService.ps1 -Start
```

サービスでは、スケジュールされたイベントのポーリングが 10 秒ごとに開始され、イベントが承認され、メンテナンスが迅速化されます。  凍結、再起動、再デプロイ、およびプリエンプトは、スケジュール イベントによってキャプチャされるイベントです。   イベントを承認する前にいくつかの軽減策をトリガーするようにスクリプトを拡張できます。

サービスの状態を検証し、それが実行中であることを確認します。

```powershell
.\SchService.ps1 -status  
```

この場合は、`Running` が返されるはずです。

サービスでは、スケジュールされたイベントのポーリングが 10 秒ごとに開始され、イベントが承認され、メンテナンスが迅速化されます。  凍結、再起動、再デプロイ、およびプリエンプトは、スケジュール イベントによってキャプチャされるイベントです。 イベントを承認する前にいくつかの軽減策をトリガーするようにスクリプトを拡張することができます。

上記のイベントのいずれかが Schedule Event サービスによってキャプチャされると、アプリケーション イベント ログのイベントの状態、イベントの種類、リソース (仮想マシン名)、および NotBefore (最小通知期間) に記録されます。 アプリケーション イベント ログでは、ID が 1234 のイベントを見つけることができます。

サービスを設定して開始すると、Windows アプリケーション ログにイベントが記録されます。   これが機能することを確認するには、可用性セット内のいずれかの仮想マシンを再起動します。記録されたイベントがイベント ビューアーに表示されるはずです。[Windows ログ]、[アプリケーション] の順に進むと、VM が再起動されたことを示すログを確認できます。 

![イベント ビューアーのスクリーンショット。](./media/notifications/event-viewer.png)

イベントが Schedule Event サービスによってキャプチャされると、イベントの状態、イベントの種類、リソース (仮想マシン名)、および NotBefore (最小通知期間) がアプリケーション イベント ログに記録されます。 アプリケーション イベント ログでは、ID が 1234 のイベントを見つけることができます。

> [!NOTE] 
> この例では、仮想マシンが可用性セット内に置かれていたため、1 つの仮想マシンをコレクターとして指定することで、スケジュールされたイベントをリッスンし、ログ分析ワークスペースにルーティングすることができました。 スタンドアロンの仮想マシンが複数ある場合は、どの仮想マシンでもサービスを実行することができ、それぞれを個別にログ分析ワークスペースに接続することができます。
>
> このセットアップでは、Windows を選択しましたが、Linux 上でも同様のソリューションを設計することができます。

Scheduled Event Service は、`–stop` スイッチおよび `–remove` スイッチを使用して、いつでも停止または削除することができます。

## <a name="connect-to-the-workspace"></a>ワークスペースに接続する


ここで、Log Analytics ワークスペースをコレクター VM に接続します。 Log Analytics ワークスペースはリポジトリとして機能するので、コレクター VM からアプリケーション ログをキャプチャできるようにイベント ログ コレクションを構成します。 

 Microsoft のサービスによってアプリケーション ログとして保存されるイベント ログに Scheduled Events をルーティングするには、ご利用の仮想マシンをご利用の Log Analytics ワークスペースに接続する必要があります。  
 
1. 作成したワークスペースのページを開きます。
1. **[データソースへの接続]** で、 **[Azure 仮想マシン (VM)]** を選択します。

    ![データソースとして VM に接続する](./media/notifications/connect-to-data-source.png)

1. **[myCollectorVM]** を検索して選択します。 
1. **myCollectorVM** の新しいページで、 **[接続]** を選択します。

これにより、ご利用の仮想マシンに [Microsoft Monitoring Agent](../extensions/oms-windows.md) がインストールされます。 ご利用の VM をワークスペースに接続して拡張機能をインストールするまで数分かかります。 

## <a name="configure-the-workspace"></a>ワークスペースを構成する

1. ご自分のワークスペースのページを開き、 **[詳細設定]** を選択します。
1. 左側のメニューから **[データ]** を選択し、次に **[Windows イベント ログ]** を選択します。
1. **[次のイベント ログから収集]** で、「*アプリケーション*」と入力して、一覧から **[アプリケーション]** を選択します。

    ![[詳細設定] を選択する](./media/notifications/advanced.png)

1. **[エラー]** 、 **[警告]** 、 **[情報]** を選択したままとし、次に **[保存]** を選択して設定を保存します。


> [!NOTE]
> 遅延が発生します。ログが使用可能になるまでに最大で 10 分かかる場合があります。 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Azure Monitor を使用したアラート ルールの作成 


イベントが Log Analytics にプッシュされたら、次の[クエリ](../../azure-monitor/logs/log-analytics-tutorial.md)を実行して、スケジュール イベントを探すことができます。

1. ページの上部にある **[ログ]** を選択し、次の内容をテキスト ボックスに貼り付けます。

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. **[保存]** を選択してから、名前として「`ogQuery`」を入力し、種類は **[クエリ]** のままとし、 `VMLogs` に「**VMLogs**」と入力して、 **[保存]** を選択します。 

    ![クエリの保存](./media/notifications/save-query.png)

1. **[新しいアラート ルール]** を選択します。 
1. **[ルールの作成]** ページでは、 **[リソース]** として `collectorworkspace` をそのまま使用します。
1. **[条件]** で、エントリ *[Whenever the customer log search is]\(顧客ログ検索がいつでも\)<login undefined>* を選択します。 **[シグナル ロジックの構成]** ページが開きます。
1. **[しきい値]** に「*0*」を入力して、 **[完了]** を選択します。
1. **[アクション]** で、 **[アクショングループの作成]** を選択します。 **[アクション グループの追加]** ページが開きます。
1. **[アクション グループ名]** に「*myActionGroup*」と入力します。
1. **[短い名前]** に「*myActionGroup*」と入力します。
1. **[リソース グループ]** で、**myResourceGroupAvailability** を選択します。
1. [アクション] の **[アクション名]** に「**電子メール**」 と入力し、 **[電子メール/SMS/プッシュ/音声]** を選択します。 **[電子メール/SMS/プッシュ/音声]** ページが開きます。
1. **[電子メール]** を選択し、ご自分の電子メール アドレスを入力して、 **[OK]** を選択します。
1. **[アクション グループの追加]** ページで、 **[OK]** を選択します。 
1. **[ルールの作成]** ページの **[アラートの詳細]** で、 **[アラート ルール名]** に「*myAlert*」と入力し、 **[説明]** に「*電子メールによるアラート ルール*」と入力します。
1. 完了したら、 **[アラート ルールの作成]** を選択します。
1. 可用性セット内の VM のいずれかを再起動します。 数分以内に、アラートがトリガーされたことを知らせる電子メールが届きます。

ご自分のアラート ルールを管理するには、リソース グループに移動し、左側のメニューから **[アラート]** を選択し、次にページの上部にある **[アラート ルールの管理]** を選択します。

     
## <a name="next-steps"></a>次のステップ

詳細については、GitHub の「[Scheduled events service](https://github.com/microsoft/AzureScheduledEventsService)」 (スケジュール化されたイベント サービス) ページを参照してください。