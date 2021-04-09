---
title: クイック スタート:Azure Monitor を使用してハイブリッド Linux コンピューターからデータを収集する
description: このクイックスタートでは、Azure の外部で実行中の Linux コンピューター用に Log Analytics エージェントをデプロイし、Azure Monitor ログを使用してデータを収集できるようにする方法について説明します。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: dbe51930ec92ec4f89738dc5d543003f45acebf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733824"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>クイック スタート:Azure Monitor を使用してハイブリッド環境の Linux コンピューターからデータを収集する

[Azure Monitor](../overview.md) は、詳細な分析と相関のために、環境内の物理的または仮想的な Linux コンピューターから直接データを Log Analytics ワークスペースに収集することができます。 [Log Analytics エージェント](../agents/log-analytics-agent.md)をインストールすることにより、Azure Monitor でデータセンターまたは他のクラウド環境からデータを収集できるようになります。 このクイックスタートでは、いくつかの簡単な手順で、Linux サーバーを構成し、データを収集する方法を示します。 Azure Linux VM については、[Azure 仮想マシンに関するデータの収集](./quick-collect-azurevm.md)のページを参照してください。  

サポートされている構成を確認するには、「[サポートされているオペレーティング システム](../agents/agents-overview.md#supported-operating-systems)」と[ネットワーク ファイアウォールの構成](../agents/log-analytics-agent.md#network-requirements)に関するセクションを参照してください。
 
Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。 

## <a name="create-a-workspace"></a>ワークスペースの作成

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

    ![Azure portal で Log Analytics ワークスペースを見つける](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. **[作成]** を選択し、次の項目について選択します。

   * 新しい **Log Analytics ワークスペース** の名前 (*DefaultLAWorkspace* など) を指定します。  
   * 関連付ける **サブスクリプション** をドロップダウン リストから選択します (既定値が適切でない場合)。
   * **[リソース グループ]** では、1 つ以上の Azure Virtual Machines を含む既存のリソース グループを選択します。  
   * VM のデプロイ先となる **場所** を選択します。  詳細については、[Log Analytics を使用できるリージョン](https://azure.microsoft.com/regions/services/)に関するページを参照してください。
   * 2018 年 4 月 2 日より後に作成された新しいサブスクリプションでワークスペースを作成した場合は、自動的に "*1 GB あたり*" の価格プランが使用され、価格レベルを選択するためのオプションは利用できなくなります。  4 月 2 日より前に作成された既存のサブスクリプションのワークスペースを作成している場合、または既存の EA 登録に関連付けられたサブスクリプションに対してワークスペースを作成している場合は、希望の価格レベルを選択します。  特定のレベルの詳細については、[Log Analytics の価格の詳細](https://azure.microsoft.com/pricing/details/log-analytics/)に関するページを参照してください。
  
        ![Azure portal で Log Analytics ワークスペースを作成する](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. **[Log Analytics ワークスペース]** ウィンドウで必要な情報を入力したら、 **[OK]** を選択します。  

情報が検証され、ワークスペースが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。 

## <a name="obtain-workspace-id-and-key"></a>ワークスペース ID とキーを取得する

Linux 用 Log Analytics エージェントをインストールする前に、Log Analytics ワークスペースのワークスペース ID とキーが必要です。 この情報は、エージェント ラッパー スクリプトがエージェントを適切に構成し、そのエージェントを Azure Monitor と正常に通信できるようにするために必要です。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Azure portal の左上隅にある **[すべてのサービス]** を選択します。 検索ボックスに「**Log Analytics**」と入力します。 入力すると、入力内容に基づいて一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

2. Log Analytics ワークスペースの一覧で、前の手順で作成したワークスペースを選択します。 (**DefaultLAWorkspace** という名前を付けた可能性があります。)

3. **[エージェント管理]** を選択します。
 
4. 次に、 **[Linux サーバー]** を選択します。

5. **[ワークスペース ID]** と **[主キー]** の右側に値が表示されます。 両方をコピーしてお使いのエディターに貼り付けます。

## <a name="install-the-agent-for-linux"></a>Linux 用エージェントのインストール

次の手順では、Azure および Azure Government クラウドで Log Analytics 用のエージェントのセットアップを構成します。  

>[!NOTE]
>Linux 用 Log Analytics エージェントは、複数の Log Analytics ワークスペースにレポートするように構成することはできません。  

Linux コンピューターと Log Analytics との通信をプロキシ サーバーを介して行う必要がある場合、コマンド ラインから「`-p [protocol://][user:password@]proxyhost[:port]`」を入力することでプロキシの構成を指定できます。  *proxyhost* プロパティは、プロキシ サーバーの完全修飾ドメイン名または IP アドレスを受け取ります。 

例: `https://user01:password@proxy01.contoso.com:30443`

1. Log Analytics ワークスペースに接続できるように Linux コンピューターを構成するには、前にコピーしたワークスペース ID と主キーを指定した次のコマンドを実行します。 次のコマンドは、エージェントをダウンロードし、そのチェックサムを検証してインストールします。 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    次のコマンドには、`-p` プロキシ パラメーターと、お使いのプロキシ サーバーで認証が必要な場合の構文例が含まれています。

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government クラウドの Log Analytics ワークスペースに接続できるように Linux コンピューターを構成するには、前にコピーしたワークスペース ID と主キーを指定した次のコマンドを実行します。 次のコマンドは、エージェントをダウンロードし、そのチェックサムを検証してインストールします。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    次のコマンドには、`-p` プロキシ パラメーターと、お使いのプロキシ サーバーで認証が必要な場合の構文例が含まれています。

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. 次のコマンドを実行してエージェントを再起動します。 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>イベントとパフォーマンス データを収集する

Azure Monitor では、Linux Syslog からイベントを収集できるほか、特定のパフォーマンス カウンターを収集することによって、より長期的な分析とレポート作成を行えます。 特定の条件を検出したときにアクションを実行することもできます。 まず、以下の手順に従って、Linux Syslog およびいくつかの一般的なパフォーマンス カウンターからのイベント収集を構成します。  

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「Log Analytics」と入力します。 入力すると、入力内容に基づいて一覧がフィルター処理されます。 **Log Analytics ワークスペース** を選択し、Log Analytics ワークスペースの一覧で探しているワークスペースを選択し、その **Log Analytics** ワークスペースの **[詳細設定]** を選択します。

2. **[データ]** 、 **[Syslog]** の順に選択します。  

3. ログの名前を入力して Syslog を追加します。 「**Syslog**」と入力し、プラス記号 **+** を選択します。  

4. テーブルで、重大度の **[情報]** 、 **[通知]** および **[デバッグ]** の各チェック ボックスをオフにします。 

5. ページの上部にある **[保存]** を選択して構成を保存します。

6. **[Linux パフォーマンス データ]** を選択して、Linux コンピューターでのパフォーマンス カウンターの収集を有効にします。 

7. 新しい Log Analytics ワークスペースの Linux パフォーマンス カウンターを初めて構成する場合は、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。 それぞれのオプションの横には、チェック ボックスが表示されます。

    ![Azure Monitor で選択された既定の Linux パフォーマンス カウンター](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    **[下の構成をコンピューターに適用する]** を選択し、 **[選択したパフォーマンス カウンターを追加する]** を選択します。 カウンターが追加され、10 秒間の収集サンプル間隔でプリセットされます。  

8. ページの上部にある **[保存]** を選択して構成を保存します。

## <a name="view-data-collected"></a>収集されたデータを表示する

これでデータ収集は有効になりました。次は、簡単なログ検索の例を実行して、ターゲット コンピューターのデータを表示してみましょう。  

1. 選択したワークスペースで、左側のウィンドウから **[ログ]** を選択します。

2. ログ クエリ ページでクエリ エディターに「`Perf`」と入力し、 **[実行]** を選択します。
 
    ![Log Analytics のログ検索](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    たとえば、次の画像のクエリでは、10,000 件のパフォーマンス レコードが返されています。 実際の結果はかなり少なくなります。

    ![Log Analytics のログ検索の結果](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合、Linux コンピューターからエージェントを削除し、Log Analytics ワークスペースを削除できます。  

エージェントを削除するには、Linux コンピューターで次のコマンドを実行します。 引数 *--purge* を指定することにより、エージェントとその構成が完全に削除されます。

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

ワークスペースを削除するには、作成した Log Analytics ワークスペースを選択し、リソース ページで **[削除]** を選択します。

![Log Analytics リソースを削除する](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>次のステップ

これでオンプレミスの Linux コンピューターからオペレーション データとパフォーマンス データが収集されているので、収集したデータの調査と分析、およびデータに対するアクションの実行を *無料* で簡単に開始することができます。  

データの表示および分析方法を学習する場合は、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Log Analytics でのデータの表示と分析](../logs/log-analytics-tutorial.md)