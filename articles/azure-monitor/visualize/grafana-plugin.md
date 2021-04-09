---
title: Grafana を使用して Azure のサービスとアプリケーションを監視する
description: Grafana で表示できるように Azure Monitor および Application Insights データをルーティングします。
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: decd0353bcb2cf048fd5591ea8c895cf22944809
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048660"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Grafana での Azure サービスの監視
[Azure Monitor データ ソース プラグイン](https://grafana.com/plugins/grafana-azure-monitor-datasource)を使用して [Grafana](https://grafana.com/) から Azure のサービスとアプリケーションを監視できるようになりました。 このプラグインを使用して、Azure Monitor によって収集されたアプリケーションのパフォーマンス データ (さまざまなログやメトリックなど) を一か所にまとめることができます。 その後、このデータを Grafana ダッシュボードで表示できます。

Grafana サーバーをセットアップし、Azure Monitor のメトリックとログ用のダッシュボードを構築するには、次の手順を使用します。

## <a name="set-up-a-grafana-server"></a>Grafana サーバーをセットアップする

### <a name="set-up-grafana-locally"></a>Grafana をローカルにセットアップする
Grafana サーバーをローカルにセットアップするには、[Grafana をダウンロードし、ご自分のローカル環境にインストール](https://grafana.com/grafana/download)します。 プラグインの Azure Monitor の統合を使用するには、Grafana バージョン 5.3 以降をインストールします。

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Azure Marketplace を使用して Azure 上に Grafana をセットアップする
1. Azure Marketplace に移動し、Grafana Labs のそばの Grafana を選択します。

2. 名前と詳細を入力します。 新しいリソース グループを作成します。 VM ユーザー名、VM パスワード、および Grafana サーバー管理者のパスワードとして選択した値を記録してください。  

3. VM サイズとストレージ アカウントを選択します。

4. ネットワーク構成設定を構成します。

5. 概要を表示し、使用条件を受け入れた後、 **[作成]** を選択します。

6. デプロイが完了したら、 **[Go to Resource Group] \(リソース グループに移動する)** を選択します。 新しく作成されたリソースの一覧が表示されます。

    ![Grafana リソース グループ オブジェクト](media/grafana-plugin/grafana1.png)

    ネットワーク セキュリティ グループ (この場合は *grafana-nsg*) を選択すると、Grafana サーバーへのアクセスのためにポート 3000 が使用されることを確認できます。

7. Grafana サーバーのパブリック IP アドレスを取得します。リソースの一覧に戻り、 **[パブリック IP アドレス]** を選択します。

## <a name="sign-in-to-grafana"></a>Grafana にサインインする

1. ブラウザーで、サーバーの IP アドレスを使用して [ログイン] ページを開きます (*http://\<IP address\>:3000* または *\<DNSName>\:3000*)。 3000 は既定のポートです。セットアップ中にご自身で別のポートを選択している場合があることに注意してください。 構築した Grafana サーバーのログイン ページが表示されます。

    ![Grafana ログイン画面](./media/grafana-plugin/grafana-login-screen.png)

2. ユーザー名 *admin* と、作成済みの Grafana サーバー管理者のパスワードを使用してサインインします。 ローカル セットアップを使用している場合、既定のパスワードは *admin* であり、初回のログイン時に変更することを要求されます。

## <a name="configure-data-source-plugin"></a>データ ソース プラグインを構成する

正常にログインすると、Azure Monitor データ ソース プラグインが既に含まれていることが確認されます。

![Grafana に Azure Monitor プラグインが含まれている](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Azure Monitor データソースを追加するには、 **[Add data source]\(データ ソースの追加\)** を選択します。

2. データ ソースの名前を選択し、種類としてドロップダウンから **[Azure Monitor]** を選択します。

3. サービス プリンシパルを作成します。Grafana では、Azure Active Directory サービス プリンシパルを使用して Azure Monitor API に接続してメトリック データを収集します。 Azure リソースへのアクセスを管理するには、サービス プリンシパルを作成するか、既存のものを使用する必要があります。
    * サービス プリンシパルを作成するには、[この手順](../../active-directory/develop/howto-create-service-principal-portal.md)を参照してください。 ご自分のテナント ID (ディレクトリ ID)、クライアント ID (アプリケーション ID)、およびクライアント シークレット (アプリケーション キー値) をコピーして保存します。
    * [アプリケーションへのロールの割り当て](../../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照して、監視するサブスクリプション内の Azure Active Directory アプリケーション、リソース グループ、またはリソースに閲覧者ロールを割り当てます。 
    Log Analytics API には、閲覧者ロールのアクセス許可を含む[Log Analytics 閲覧者ロール](../../role-based-access-control/built-in-roles.md#log-analytics-reader)が必要なので、それを追加します。

4. 使用する API への接続の詳細を指定します。 それらのすべてまたは一部に接続できます。 
    * Azure Monitor のメトリックとログの両方に接続する場合は、 **[Same details as Azure Monitor API]\(Azure Monitor API の詳細と同じ\)** を選択することで、同じ資格情報を再利用できます。
    * プラグインを構成するときに、このプラグインで監視する Azure クラウド (パブリック、Azure US Government、Azure Germany、または Azure China) を指定できます。
    * Application Insights を使用する場合は、Application Insights ベースのメトリックを収集するための Application Insights API とアプリケーション ID も含めることができます。 詳細については、「[Getting your API key and Application ID (API キーとアプリケーション ID の取得)](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)」を参照してください。

        > [!NOTE]
        > 一部のデータ ソース フィールドの名前は、相関関係にある Azure の設定の名前とは異なっています。
        > * [Tenant ID]\(テナント ID\) は Azure ディレクトリ ID です。
        > * [Client ID]\(クライアント ID\) は Azure Active Directory のアプリケーション ID です。
        > * [Client Secret]\(クライアント シークレット\) は Azure Active Directory のアプリケーション キー値です。

5. Application Insights を使用する場合は、Application Insights ベースのメトリックを収集するための Application Insights API とアプリケーション ID も含めることができます。 詳細については、「[Getting your API key and Application ID (API キーとアプリケーション ID の取得)](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)」を参照してください。

6. **[保存]** を選択します。Grafana によって各 API の資格情報がテストされます。 次のようなメッセージが表示されます。  
    ![Grafana でのデータ ソース構成が承認された](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Grafana ダッシュボードを構築する

1. Grafana のホーム ページに移動し、 **[New Dashboard]\(新しいダッシュボード\)** を選択します。

2. 新しいダッシュボードで、 **[Graph] \(グラフ)** を選択します。 他のチャート作成オプションを試すこともできますが、この記事では *[Graph] \(グラフ)* を例として使用します。

3. ダッシュボードに空白のグラフが表示されます。 パネルのタイトルをクリックし、 **[編集]** を選択して、このグラフ チャートにプロットするデータの詳細を入力します。
    ![Grafana の新しいグラフ](./media/grafana-plugin/grafana-new-graph-dark.png)

4. 構成した Azure Monitor データ ソースを選択します。
   * Azure Monitor のメトリックの収集 - [Service]\(サービス\) のドロップダウンから **[Azure Monitor]** を選択します。 セレクターの一覧が表示されます。この一覧から、このグラフで監視するリソースとメトリックを選択できます。 VM からメトリックを収集するには、**Microsoft.Compute/VirtualMachines** 名前空間を使用します。 VM とメトリックを選択したら、ダッシュボードでのそれらのデータの表示を開始できます。
     ![Grafana での Azure Monitor 用のグラフの構成](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Azure Monitor ログ データの収集 - サービスのドロップダウンから **[Azure Log Analytics]** を選択します。 クエリを実行するワークスペースを選択し、クエリ テキストを設定します。 設定済みのログ クエリをここにコピーすることも、新しく作成することもできます。 クエリの入力中は、IntelliSense が起動し、オートコンプリートによる選択肢が表示されます。 視覚化タイプ **[タイム シリーズ]** **[テーブル]** を選択し、クエリを実行します。
    
     > [!NOTE]
     >
     > プラグインに用意されている既定のクエリでは、2 つのマクロ ($__timeFilter() と $__interval) が使用されます。 
     > Grafana では、これらのマクロによって、グラフの一部を拡大するときに、時間範囲と時間グレインを動的に計算できます。 これらのマクロを削除し、*TimeGenerated > ago(1h)* などの標準的な時間フィルターを使用できますが、それを行うと、グラフで拡大機能はサポートされなくなります。
    
     ![Grafana での Azure Log Analytics 用のグラフの構成](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. 2 つのグラフを含む単純なダッシュボードを次に示します。 左側のグラフは、2 つの VM の CPU の割合を示します。 右側のグラフは、トランザクション API の種類で分類された Azure Storage アカウント内のトランザクションを示します。
    ![Grafana の 2 つのグラフの例](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>省略可能:同じ Grafana サーバーでカスタム メトリックを監視する

Telegraf と InfluxDB をインストールして、同じ Grafana インスタンスでカスタム メトリックとエージェント ベースのメトリックの両方を収集してプロットすることもできます。 これらのメトリックをダッシュボードにまとめて表示するために使用できる多くのデータ ソース プラグインがあります。

この設定を再利用して、Prometheus サーバーからのメトリックを含めることもできます。 Grafana のプラグイン ギャラリーにある Prometheus データ ソース プラグインを使用します。

Telegraf、InfluxDB、Prometheus、および Docker の使用方法に関する優れた参照記事を次に示します。
 - [How To Monitor System Metrics with the TICK Stack on Ubuntu 16.04 (Ubuntu 16.04 で TICK スタックを使用してシステム メトリックを監視する方法)](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [A monitoring solution for Docker hosts, containers, and containerized services (Docker ホスト、コンテナー、およびコンテナー化されたサービスの監視ソリューション)](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Azure Monitor および Application Insights からのメトリックを含む完全な Grafana ダッシュボードのイメージを次に示します。
![Grafana Example Metrics (Grafana のメトリックの例)](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Grafana の高度な機能

### <a name="variables"></a>変数
いくつかのクエリの値は、UI のドロップダウン リストから選択してクエリ内で更新できます。 例として次のクエリを検討します。
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

使用可能なすべての **ソリューション** の値を一覧表示する変数を構成し、それを使用するようにクエリを更新できます。
新しい変数を作成するには、ダッシュボードの右上の領域にある [Settings]\(設定\) をクリックし、 **[Variables]\(変数\)** 、 **[New]\(新規\)** の順に選択します。
[Variable]\(変数\) ページで、データ ソースと、値の一覧を取得するために実行するクエリを定義します。
![Grafana での変数の構成](./media/grafana-plugin/grafana-configure-variable-dark.png)

作成したら、選択した値を使用するようにクエリを調整します。グラフがそれに応答して変化します。
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana での変数の使用](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>ダッシュボード再生リストを作成する

Grafana の多くの有効な機能の 1 つに、ダッシュボード再生リストがあります。 複数のダッシュボードを作成し、それらを再生リストに追加して、表示するダッシュボードごとに間隔を構成できます。 ダッシュボードのサイクル全体を表示するには、 **[再生]** を選択します。 それらを大きな壁のモニタに表示して、グループの状態ボードを提供できます。

![Grafana の再生リストの例](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure 上に Grafana 環境をセットアップした場合、VM を使用しているかどうかにかかわらず、VM が実行されたときに課金されます。 追加料金が発生しないようにするには、この記事で作成されたリソース グループをクリーンアップします。

1. Azure Portal の左側のメニューから、 **[リソース グループ]** 、 **[Grafana]** の順にクリックします。
2. リソース グループのページで、 **[削除]** をクリックし、テキスト ボックスに「**Grafana**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ
* [Azure Monitor メトリックの概要](../data-platform.md)
