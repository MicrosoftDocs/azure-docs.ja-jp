---
title: Azure Monitor を使用してハイブリッド Windows コンピューターからデータを収集する
description: このクイックスタートでは、Azure の外部で実行中の Windows コンピューター用に Log Analytics エージェントをデプロイし、Azure Monitor ログを使用してデータを収集できるようにする方法について説明します。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 65d60c77280b569d1bff5e4d3acbdf97ead96270
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733790"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Azure Monitor を使用してハイブリッド環境の Windows コンピューターからデータを収集する

[Azure Monitor](../overview.md) は、詳細な分析と相関のために、環境内の物理的または仮想的な Windows コンピューターから直接データを Log Analytics ワークスペースに収集することができます。 [Log Analytics エージェント](../agents/log-analytics-agent.md)をインストールすることにより、Azure Monitor でデータセンターまたは他のクラウド環境からデータを収集できるようになります。 このクイック スタートでは、いくつかの簡単な手順で、Windows コンピューターを構成し、データを収集する方法を示します。 Azure Windows VM の詳細については、「[Azure Virtual Machines に関するデータの収集](./quick-collect-azurevm.md)」を参照してください。  

サポートされている構成を確認するには、「[サポートされているオペレーティング システム](../agents/agents-overview.md#supported-operating-systems)」と[ネットワーク ファイアウォールの構成](../agents/log-analytics-agent.md#network-requirements)に関するセクションを参照してください。
 
Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

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


## <a name="get-the-workspace-id-and-key"></a>ワークスペース ID とキーを入手する

Windows 用の Log Analytics エージェント (Microsoft Monitoring Agent (MMA)) をインストールするには、あらかじめ Log Analytics ワークスペースの ID とキーが必要です。 この情報は、設定ウィザードでエージェントを適切に構成し、そのエージェントが Azure Monitor と通信できるようにするために必要です。  

1. Azure portal の左上隅にある **[すべてのサービス]** を選択します。 検索ボックスに「**Log Analytics**」と入力します。 入力すると、入力内容に基づいて一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

2. Log Analytics ワークスペースの一覧で、前の手順で作成したワークスペースを選択します。 (**DefaultLAWorkspace** という名前を付けた可能性があります。)

3. **[詳細設定]** を選択します。

    ![Log Analytics の詳細設定](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. **[接続されたソース]** 、 **[Windows サーバー]** の順に選択します。

5. **[ワークスペース ID]** と **[主キー]** の右側の値をコピーします。 任意のエディターに貼り付けます。

## <a name="install-the-agent-for-windows"></a>Windows 用エージェントをインストールする

次の手順では、Azure および Azure Government で Log Analytics 用のエージェントをインストールして構成します。 Microsoft Monitoring Agent のセットアップ プログラムを使用して、コンピューターにエージェントをインストールします。

1. 前の一連の手順からの続きとして、 **[Windows サーバー]** ページで、ダウンロードするバージョンの **[Windows エージェントのダウンロード]** を選択します。 お使いの Windows オペレーティング システムのプロセッサ アーキテクチャに適したバージョンを選択します。

2. セットアップを実行して、コンピューターにエージェントをインストールします。

3. **[ようこそ]** ページで **[次へ]** をクリックします。

4. **[ライセンス条項]** ページの記述内容を確認し、 **[同意する]** を選択します。

5. **[インストール先フォルダー]** ページで、既定のインストール フォルダーを変更するか、そのまま使用して、 **[次へ]** を選択します。

6. **[エージェントのセットアップ オプション]** ページで、エージェントを Azure Log Analytics に接続し、 **[次へ]** を選択します。

7. **[Azure Log Analytics]** ページで、次の手順を完了します。

   1. **[ワークスペース ID]** と **[ワークスペース キー (主キー)]** に、先ほどコピーした値を貼り付けます。 コンピューターが Azure Government 内の Log Analytics ワークスペースにレポートする必要がある場合は、 **[Azure クラウド]** リストで **[Azure US Government]** を選択します。  
   2. コンピューターがプロキシ サーバーを介して Log Analytics サービスと通信する必要がある場合は、 **[詳細]** を選択し、プロキシ サーバーの URL とポート番号を指定します。 プロキシ サーバーで認証が必要な場合には、プロキシ サーバーとの認証用のユーザー名とパスワードを入力し、 **[次へ]** を選択します。  

8. 構成設定を追加したら、 **[次へ]** を選択します。

    ![Microsoft Monitoring Agent のセットアップ](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. **[インストールの準備完了]** ページで、設定内容を確認し、 **[インストール]** を選択します。

10. **[構成は正常に終了しました]** ページで **[完了]** を選択します。

インストールとセットアップが完了したら、コントロール パネルに [Microsoft Monitoring Agent] が表示されます。 構成を検証して、エージェントが Log Analytics ワークスペースに接続されていることを確認できます。 接続時に、 **[Azure ログ分析]** タブで、エージェントに次のメッセージが表示されます。**Microsoft Monitoring Agent は、Microsoft ログ分析サービスに正常に接続されています。**<br><br> ![MMA 接続状態](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>イベントとパフォーマンス データを収集する

Azure Monitor では、指定されたイベントを Windows イベント ログから収集できるほか、パフォーマンス カウンターを収集することによって、より長期的な分析とレポート作成を行えます。 特定の条件を検出したときにアクションを実行することもできます。 以下の手順に従って、Windows イベント ログと、手始めとしていくつかの一般的なパフォーマンス カウンターからのイベントの収集を構成します。  

1. Azure portal の左下隅にある **[その他のサービス]** を選択します。 検索ボックスに「**Log Analytics**」と入力します。 入力すると、入力内容に基づいて一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

2. **[詳細設定]** を選択します。

    ![Log Analytics の詳細設定](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. **[データ]** を選択してから、 **[Windows イベント ログ]** を選択します。  

4. イベント ログを追加するには、ログの名前を入力します。 「**System**」と入力し、プラス記号 ( **+** ) を選択します。  

5. テーブルで、 **[エラー]** と **[警告]** の重大度を選択します。

6. ページの最上部で **[保存]** を選択します。

7. **[Windows パフォーマンス カウンター]** を選択して、Windows コンピューターでのパフォーマンス カウンターの収集を有効にします。

8. 新しい Log Analytics ワークスペースの Windows パフォーマンス カウンターを初めて構成する場合は、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。 各オプションが一覧表示され、その横にチェック ボックスがあります。

    ![Windows パフォーマンス カウンター](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    **[選択したパフォーマンス カウンターを追加する]** を選択します。 カウンターが追加され、10 秒間の収集サンプル間隔でプリセットされます。

9. ページの最上部で **[保存]** を選択します。

## <a name="view-collected-data"></a>収集したデータを表示する

これでデータ収集は有効になりました。次は、簡単なログ検索を実行して、ターゲット コンピューターのデータを表示してみましょう。  

1. 選択したワークスペースで、左側のウィンドウから **[ログ]** を選択します。

2. ログ クエリ ページでクエリ エディターに「`Perf`」と入力し、 **[実行]** を選択します。
 
    ![Log Analytics のログ検索](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    たとえば、次の画像のクエリでは、10,000 件のパフォーマンス レコードが返されています。 実際の結果はかなり少なくなります。

    ![Log Analytics のログ検索の結果](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合、コンピューターからエージェントを削除し、Log Analytics ワークスペースを削除できます。  

エージェントを削除するには、次の手順を完了します。

1. [コントロール パネル] を開きます。

2. **[プログラムと機能]** を開きます。

3. **[プログラムと機能]** で、 **[Microsoft Monitoring Agent]** をクリックし、 **[アンインストール]** を選択します。

以前に作成した Log Analytics ワークスペースを削除するには、それを選択し、リソース ページで **[削除]** を選択します。

![Log Analytics ワークスペースを削除する](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>次のステップ

これで Windows コンピューターからオペレーション データとパフォーマンス データが収集されているので、収集したデータの調査と分析、およびデータに対するアクションの実行を *無料* で簡単に開始することができます。  

データの表示および分析方法を学習する場合は、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Log Analytics でのデータの表示と分析](../logs/log-analytics-tutorial.md)