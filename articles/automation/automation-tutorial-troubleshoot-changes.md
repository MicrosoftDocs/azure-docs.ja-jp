---
title: Azure Automation での Azure VM に対する変更に関するトラブルシューティング | Microsoft Docs
description: この記事では、Azure VM に対する変更をトラブルシューティングする方法について説明します。
services: automation
ms.subservice: change-inventory-management
keywords: 変更, 追跡, Change Tracking, インベントリ, Automation
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 63fe36a51b69648039c3a16be4267fc0c72e8a58
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185808"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Azure VM に対する変更のトラブルシューティング

このチュートリアルでは、Azure 仮想マシンの変更に関する問題を解決する方法について説明します。 Change Tracking とインベントリを有効にして、コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーの変更を追跡することができます。
このような構成の変更を識別することで、環境全体の運用上の問題を特定できるようになります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM の Change Tracking とインベントリを有効にする
> * 停止したサービスの変更ログを検索する
> * 変更の追跡を構成する
> * アクティビティ ログの接続を有効にする
> * イベントをトリガーする
> * 変更を表示する
> * アラートを構成する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する [Automation アカウント](./index.yml)。
* 機能に対して有効にする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

このチュートリアルでは、まず Change Tracking とインベントリを有効にする必要があります。 既に機能を有効にしてある場合は、この手順は不要です。

>[!NOTE]
>フィールドが淡色表示されている場合は、その VM で別の Automation 機能が有効になっているため、同じワークスペースと Automation アカウントを使用する必要があります。

1. **[仮想マシン]** を選択し、一覧から VM を選択します。
2. 左側のメニューで、 **[操作]** の **[インベントリ]** を選択します。 [インベントリ] ページが開きます。

    ![変更の有効化](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. [Log Analytics](../azure-monitor/log-query/log-query-overview.md) ワークスペースを選択します。 このワークスペースは、Change Tracking やインベントリなどの機能によって生成されるデータを収集します。 ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. 使用する Automation アカウントを選択します。

5. 展開する場所を構成します。

5. **[有効化]** をクリックして、VM の機能を展開します。 

セットアップの過程で、Windows 用の Log Analytics エージェントと [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) と共に VM がプロビジョニングされます。 Change Tracking およびインベントリの有効化には、最大で 15 分かかることがあります。 この処理中はブラウザーのウィンドウは閉じないでください。

機能が有効になると、VM にインストールされているソフトウェアと変更に関する情報が Azure Monitor ログに送られます。
データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Azure Monitor ログで Change Tracking とインベントリを使用する

Change Tracking とインベントリによってログ データが生成され、Azure Monitor ログに送信されます。 クエリを実行してログを検索するには、[Change tracking]\(変更履歴\) ページの上部にある **[Log Analytics]** ウィンドウを選択します。 変更履歴データは、`ConfigurationChange` 型に格納されます。

次のサンプル Log Analytics クエリは、停止しているすべての Windows サービスを返します。

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Azure Monitor ログでのログ ファイルの実行と検索については、[Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)に関するページを参照してください。

## <a name="configure-change-tracking"></a>変更の追跡を構成する

変更の追跡では、VM の [Change Tracking] ページの上部にある **[設定の編集]** を使用して、収集および追跡するファイルとレジストリ キーを選択します。 [ワークスペースの構成] ページで、追跡する Windows レジストリ キー、Windows ファイル、または Linux ファイルを追加できます。

> [!NOTE]
> Change Tracking とインベントリでは同じ収集設定が使用され、設定はワークスペース レベルで構成されます。

### <a name="add-a-windows-registry-key"></a>Windows レジストリ キーを追加する

1. **[Windows レジストリ]** タブで **[追加]** を選択します。 

1. [変更履歴用の Windows レジストリを追加する] ページで、追跡するキーの情報を入力し、 **[保存]** をクリックします。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用されるかどうかを決定します。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |Windows レジストリ キー   | ファイル確認のためのパス。その例を次に示します。"HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Windows ファイルを追加する

1. **[Windows ファイル]** タブで **[追加]** を選択します。 

1. [変更履歴用の Windows ファイルを追加する] ページで、追跡するファイルまたはディレクトリの情報を入力し、 **[保存]** をクリックします。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用されるかどうかを決定します。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |パスの入力     | ファイル確認のためのパス (例: "c:\temp\\\*.txt")。<br>"%winDir%\System32\\\*.*" などの環境変数も使用できます。         |
    |再帰     | 追跡する項目を検索するときに、再帰を使用するかどうかを決定します。        |
    |すべての設定のファイル コンテンツをアップロードする| 追跡した変更についてファイル コンテンツのアップロードをオンまたはオフにします。 使用できるオプションは **True** または **False** です。|

### <a name="add-a-linux-file"></a>Linux ファイルを追加する

1. **[Linux ファイル]** タブで **[追加]** を選択します。 

1. [変更履歴用の Linux ファイルを追加する] ページで、追跡するファイルまたはディレクトリの情報を入力し、 **[保存]** をクリックします。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用されるかどうかを決定します。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |パスの入力     | ファイル確認のためのパス (例: "/etc/*.conf")。       |
    |パスの種類     | 追跡する項目の種類。"ファイル" または "ディレクトリ" を指定できます。        |
    |再帰     | 追跡する項目を検索するときに、再帰を使用するかどうかを決定します。        |
    |sudo の使用     | この設定により、項目を確認するときに、sudo を使用するかどうかが決まります。         |
    |リンク     | この設定により、ディレクトリを走査するときの、シンボリック リンクの処理方法が決まります。<br> **無視** - シンボリック リンクを無視し、参照先のファイル/ディレクトリを含めません。<br>**フォロー** - 再帰中、シンボリック リンクをフォローします。参照先のファイル/ディレクトリも含めます。<br>**管理** - シンボリック リンクをフォローします。また、返された内容の処理を変更することができます。      |
    |すべての設定のファイル コンテンツをアップロードする| 追跡した変更についてファイル コンテンツのアップロードをオンまたはオフにします。 使用できるオプションは True または False。|

   > [!NOTE]
   > **[リンク]** プロパティの **[管理]** 値は推奨されません。 ファイルのコンテンツの取得はサポートされていません。

## <a name="enable-activity-log-connection"></a>アクティビティ ログの接続を有効にする

1. VM の [Change tracking]\(変更履歴\) ページから **[アクティビティ ログ接続の管理]** を選択します。 

2. [Azure アクティビティ ログ] ページで、 **[接続]** をクリックして、Change Tracking とインベントリを VM の Azure アクティビティ ログに接続します。

3. VM の [概要] ページに移動し、 **[停止]** を選択して VM を停止します。 

4. メッセージが表示されたら、 **[はい]** を選択して VM を停止します。 

5. VM の割り当てが解除されたら、 **[開始]** を選択して VM を再起動します。 VM を停止して起動すると、アクティビティ ログにイベントが記録されます。 

## <a name="view-changes"></a>変更を表示する

1. [Change Tracking] ページに戻り、ページの下部にある **[イベント]** タブを選択します。 

2. しばらくすると、グラフと表に変更追跡イベントが表示されます。 グラフには、時間の経過とともに発生した変更が表示されます。 上部の折れ線グラフには、Azure アクティビティ ログ イベントが表示されます。 棒グラフの各行は、さまざまな追跡可能な変更の種類を表します。 具体的には、Linux デーモン、ファイル、Windows レジストリ キー、ソフトウェア、および Windows サービスです。 [変更] タブには、表示されている変更の詳細情報が、最新のものから順に示されます。

    ![ポータルでのイベントの表示](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. サービスとソフトウェアの変更を含め、システムに複数の変更があったことがわかります。 ページ上部にあるフィルターを使用し、 **[型の変更]** または期間を指定して結果を絞り込むことができます。

    ![VM に加えられた変更の一覧](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. **WindowsServices** の変更を選択します。 [変更の詳細] ページが開いて、変更の詳細と変更前の値および変更後の値が表示されます。 この例では、Software Protection サービスが停止されました。

    ![ポータルで変更の詳細を表示する](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>アラートを構成する

Azure portal に変更を表示することは有益ですが、サービスの停止などの変化が発生したときにアラートを受信できると、もっと便利です。 停止したサービスのアラートを追加してみましょう。 

1. Azure portal で **[監視]** に移動します。 

2. **[共有サービス]** で **[アラート]** を選択し、 **[+ 新しいアラート ルール]** をクリックします。

3. **[選択]** をクリックしてリソースを選択します。 

4. [リソースの選択] ページで、 **[リソースの種類でフィルター]** ボックスの一覧から **[Log Analytics]** を選択します。 

5. Log Analytics ワークスペースを選択し、 **[完了]** を選択します。

    ![リソースの選択](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. **[条件の追加]** をクリックします。

7. [シグナル ロジックの構成] の表で、 **[カスタム ログ検索]** を選択します。 

8. [検索クエリ] テキスト ボックスに次のクエリを入力します。

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    このクエリは、指定した時間帯に W3SVC サービスが停止したコンピューターを返します。

9. **[アラート ロジック]** の **[しきい値]** に「**0**」を入力します。 完了したら、 **[完了]** をクリックします。

    ![シグナル ロジックの構成](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. **[アクション グループ]** の **[新規作成]** を選択します。 アクション グループとは、複数のアラートで使用できるアクションのグループです。 アクションには、電子メール通知、Runbook、webhook などがありますが、これらに限定されるわけではありません。 アクション グループの詳細については、[アクション グループの作成および管理](../azure-monitor/platform/action-groups.md)に関するページを参照してください。

11. **[アラートの詳細]** で、アラートの名前と説明を入力します。 

12. **[重大度]** を、 **[情報 (重大度 2)]** 、 **[警告 (重大度 1)]** または **[重大 (重大度 0)]** に設定します。

13. **[アクション グループ名]** ボックスに、アラートの名前と短い名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

14. **[アクション]** に、 **[電子メール管理者]** などのアクションの名前を入力します。 

15. **[アクションの種類]** として、 **[Email/SMS/Push/Voice]\(メール、SMS、プッシュ、音声\)** を選択します。 

16. **[詳細]** では、 **[詳細を編集する]** を選択します。

    ![アクション グループの追加](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. [Email/SMS/Push/Voice]\(電子メール、SMS、プッシュ、音声\) ペインに名前を入力し、 **[電子メール]** チェック ボックスをオンにして、有効なメール アドレスを入力します。 完了したら、このペインで **[OK]** をクリックし、[アクション グループの追加] ページで **[OK]** をクリックします。

18. アラート メールの件名をカスタマイズするには **[アクションをカスタマイズする]** を選択します。 

19. **[ルールの作成]** で、 **[電子メールの件名]** 、 **[アラート ルールの作成]** の順に選択します。 このアラートにより、更新プログラムの展開が成功したことと、その更新プログラムの展開の実行対象となったコンピューターが通知されます。 次の図は、W3SVC サービスが停止したときに受信する電子メールの例です。

    ![email](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * VM の Change Tracking とインベントリを有効にする
> * 停止したサービスの変更ログを検索する
> * 変更の追跡を構成する
> * アクティビティ ログの接続を有効にする
> * イベントをトリガーする
> * 変更を表示する
> * アラートを構成する

さらに詳しく学ぶには、Change Tracking およびインベントリ機能の概要に進んでください。

> [!div class="nextstepaction"]
> [Change Tracking とインベントリの概要](./change-tracking.md)
