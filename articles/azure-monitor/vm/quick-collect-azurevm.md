---
title: Azure Monitor を使用して Azure 仮想マシンからデータを収集する | Microsoft Docs
description: Log Analytics エージェントの VM 拡張機能を有効にし、Log Analytics で Azure VM からデータを収集できるようにする方法について説明します。
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2f73614253a1ce442d2a93ce5e9b9d7051542511
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047028"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Azure Monitor を使用して Azure 仮想マシンからデータを収集する

[Azure Monitor](../overview.md) を使用して、Azure 仮想マシンから Log Analytics ワークスペースに直接データを収集し、詳細な分析と相関を行うことができます。 [Windows](../../virtual-machines/extensions/oms-windows.md) および [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics VM 拡張機能をインストールすることにより、Azure Monitor で Azure VM のデータを収集することができます。 このクイックスタートでは、VM 拡張機能を使用して、いくつかの簡単な手順で、Azure Linux または Windows VM を構成し、データを収集する方法を示します。  
 
このクイック スタートでは、既存の Azure Virtual Machines があることを前提とします。 ない場合は、VM のクイック スタートに従って、[Windows VM を作成](../../virtual-machines/windows/quick-create-portal.md)するか、[Linux VM を作成](../../virtual-machines/linux/quick-create-cli.md)できます。

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。 

## <a name="create-a-workspace"></a>ワークスペースの作成

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

    ![Azure portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. **[作成]** を選択し、次の項目について選択します。

   * 新しい **Log Analytics ワークスペース** の名前 (*DefaultLAWorkspace* など) を指定します。  
   * 関連付ける **サブスクリプション** をドロップダウン リストから選択します (既定値が適切でない場合)。
   * **[リソース グループ]** では、1 つ以上の Azure Virtual Machines を含む既存のリソース グループを選択します。  
   * VM のデプロイ先となる **場所** を選択します。  詳細については、[Log Analytics を使用できるリージョン](https://azure.microsoft.com/regions/services/)に関するページを参照してください。
   * 2018 年 4 月 2 日より後に作成された新しいサブスクリプションでワークスペースを作成した場合は、自動的に "*1 GB あたり*" の価格プランが使用され、価格レベルを選択するためのオプションは利用できなくなります。  4 月 2 日より前に作成された既存のサブスクリプションのワークスペースを作成している場合、または既存の EA 登録に関連付けられたサブスクリプションに対してワークスペースを作成している場合は、希望の価格レベルを選択します。  特定のレベルの詳細については、[Log Analytics の価格の詳細](https://azure.microsoft.com/pricing/details/log-analytics/)に関するページを参照してください。
  
        ![Log Analytics リソース ブレードの作成](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. **[Log Analytics ワークスペース]** ウィンドウで必要な情報を入力したら、 **[OK]** を選択します。  

情報が検証され、ワークスペースが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。 

## <a name="enable-the-log-analytics-vm-extension"></a>Log Analytics VM 拡張機能を有効にする

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Azure に既にデプロイされている Windows および Linux の仮想マシンでは、Log Analytics VM 拡張機能を使用して Log Analytics エージェントをインストールします。 この拡張機能を使用すると、インストール プロセスが簡略化され、指定した Log Analytics ワークスペースにデータを送信するようにエージェントが自動的に構成されます。 また、エージェントは、新しいバージョンがリリースされると自動的にアップグレードされるため、最新の機能と修正プログラムを利用できます。 先に進む前に、VM が実行中であることを確認してください。実行されていないと、このプロセスは正常完了しません。  

>[!NOTE]
>Linux 用 Log Analytics エージェントは、複数の Log Analytics ワークスペースにレポートするように構成することはできません。 

1. Azure portal の左上隅にある **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

2. Log Analytics ワークスペースの一覧で、前の手順で作成した *DefaultLAWorkspace* を選択します。

3. 左側のメニューの [ワークスペースのデータ ソース] で **[仮想マシン]** を選択します。  

4. **[仮想マシン]** の一覧で、エージェントをインストールする仮想マシンを選択します。 VM の **Log Analytics 接続の状態** が **[未接続]** になっていることに注意してください。

5. 仮想マシンの詳細で **[接続]** を選択します。 エージェントが自動的にインストールされ、Log Analytics のワークスペース用に構成されます。 このプロセスは数分かかります。その間、 **[状態]** には "**接続中**" と表示されます。

6. エージェントをインストールして接続した後、**Log Analytics 接続の状態** は **このワークスペース** で更新されます。

## <a name="collect-event-and-performance-data"></a>イベントとパフォーマンス データを収集する

Azure Monitor は、Windows イベント ログまたは Linux Syslog からイベントを収集できるほか、特定のパフォーマンス カウンターを収集することによって、より長期的な分析とレポート作成を行えます。また、特定の条件が検出されたときにアクションを実行できます。 まず、以下の手順に従って、Windows システム ログと Linux Syslog、およびいくつかの一般的なパフォーマンス カウンターからのイベント収集を構成します。  

### <a name="data-collection-from-windows-vm"></a>Windows VM からのデータ収集

1. **[詳細設定]** を選択します。

    ![Log Analytics の [詳細設定]](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. **[データ]** を選択してから、 **[Windows イベント ログ]** を選択します。

3. イベント ログを追加するには、ログの名前を入力します。  「**System**」と入力し、プラス記号 ( **+** ) を選択します。

4. テーブルで、重大度の **[エラー]** と **[警告]** のチェック ボックスをオンにします。

5. ページの上部にある **[保存]** を選択して構成を保存します。

6. **[Windows パフォーマンス データ]** を選択して、Windows コンピューターでのパフォーマンス カウンターの収集を有効にします。

7. 新しい Log Analytics ワークスペースの Windows パフォーマンス カウンターを初めて構成する場合は、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。 それぞれのオプションの横には、チェック ボックスが表示されます。

    ![選択したカウンターの一覧が表示され、[選択したパフォーマンス カウンターを追加する] ボタンが選択されている [Windows パフォーマンス カウンター] ペインのスクリーンショット。](media/quick-collect-azurevm/windows-perfcounters-default.png)

    **[選択したパフォーマンス カウンターを追加する]** を選択します。  カウンターが追加され、10 秒間の収集サンプル間隔でプリセットされます。
  
8. ページの上部にある **[保存]** を選択して構成を保存します。

### <a name="data-collection-from-linux-vm"></a>Linux VM からのデータ収集

1. **[Syslog]** を選択します。  

2. イベント ログを追加するには、ログの名前を入力します。  「**Syslog**」と入力し、プラス記号 **+** を選択します。  

3. テーブルで、重大度の **[情報]** 、 **[通知]** 、および **[デバッグ]** の各チェック ボックスをオフにします。 

4. ページの上部にある **[保存]** を選択して構成を保存します。

5. **[Linux パフォーマンス データ]** を選択して、Linux コンピューターでのパフォーマンス カウンターの収集を有効にします。 

6. 新しい Log Analytics ワークスペースの Linux パフォーマンス カウンターを初めて構成する場合は、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。 それぞれのオプションの横には、チェック ボックスが表示されます。

    ![選択したカウンターの一覧が表示され、[選択したパフォーマンス カウンターを追加する] ボタンが選択されている [Linux パフォーマンス カウンター] ペインのスクリーンショット。](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    **[下の構成をコンピューターに適用する]** を選択し、 **[選択したパフォーマンス カウンターを追加する]** を選択します。  カウンターが追加され、10 秒間の収集サンプル間隔でプリセットされます。  

7. ページの上部にある **[保存]** を選択して構成を保存します。

## <a name="view-data-collected"></a>収集されたデータを表示する

これでデータ収集は有効になりました。次は、簡単なログ検索の例を実行して、ターゲット VM のデータを表示してみましょう。  

1. 選択したワークスペースで、左側のウィンドウから **[ログ]** を選択します。

2. ログ クエリ ページでクエリ エディターに「`Perf`」と入力し、 **[実行]** を選択します。

    ![Log Analytics のログ検索クエリの例](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    たとえば、次の画像のクエリでは、10,000 件のパフォーマンス レコードが返されています。 実際の結果はかなり少なくなります。

    ![Log Analytics のログ検索の結果](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合は、Log Analytics ワークスペースを削除します。 そのためには、作成した Log Analytics ワークスペースを選択し、リソース ページで **[削除]** を選択します。


![Log Analytics リソースを削除する](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>次のステップ

次は Windows または Linux の仮想マシンからオペレーションおよびパフォーマンス データを収集し、*無料* で収集するデータの調査と分析、およびデータに対するアクションの実行を簡単に開始することができます。  

データの表示および分析方法を学習する場合は、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Log Analytics でのデータの表示と分析](../logs/log-analytics-tutorial.md)