---
title: Striim を使用して Azure Cosmos DB SQL API アカウントにデータを移行する
description: Striim を使用して Oracle データベースから Azure Cosmos DB SQL API アカウントにデータを移行する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 136853182e353ad5cd71981db5935fc3babe162e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339605"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Striim を使用して Azure Cosmos DB SQL API アカウントにデータを移行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Azure Marketplace の Striim イメージは、データウェアハウスとデータベースから Azure への継続的なリアルタイム データ移動を提供します。 データの移動中に、インライン非正規化とデータ変換の実行、およびリアルタイム分析とデータ レポートのシナリオの有効化を行うことができます。 Striim を使用してエンタープライズ データを Azure Cosmos DB SQL API に継続的に移動することは簡単に開始できます。 Azure には、Striim のデプロイと Azure Cosmos DB へのデータの移行を容易にするマーケットプレース オファリングが用意されています。 

この記事では、Striim を使用して **Oracle データベース** から **Azure Cosmos DB SQL API アカウント** にデータを移行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

* オンプレミスで実行されている、データが含まれた Oracle データベース。

## <a name="deploy-the-striim-marketplace-solution"></a>Striim マーケットプレース ソリューションをデプロイする

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[リソースの作成]** を選択し、Azure Marketplace で "**Striim**" を検索します。 最初のオプションを選択し、 **[作成]** します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Striim の Marketplace 項目を見つける":::

1. 次に、Striim インスタンスの構成プロパティを入力します。 Striim 環境が仮想マシンにデプロイされます。 **[基本]** ウィンドウから、**VM ユーザー名**、**VM パスワード** を入力します (このパスワードは、VM への SSH 接続に使用されます)。 ご使用の **サブスクリプション**、**リソース グループ**、および Striim をデプロイする **場所の詳細** を選択します。 完了したら、 **[OK]** を選択します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Striim の基本設定を構成する":::

1. **[Striim Cluster settings]\(Striim クラスターの設定\)** ウィンドウで、Striim のデプロイの種類と仮想マシンのサイズを選択します。

   |設定 | 値 | 説明 |
   | ---| ---| ---|
   |Striim のデプロイの種類 |スタンドアロン | Striim は、 **[スタンドアロン]** または **[クラスター]** のデプロイの種類で実行できます。 スタンドアロン モードでは、Striim サーバーは単一の仮想マシンにデプロイされます。データ ボリュームに応じて VM のサイズを選択できます。 クラスター モードでは、Striim サーバーは、選択されたサイズの 2 つ以上の VM にデプロイされます。 ノードの数が 2 つを超えるクラスター環境では、自動の高可用性とフェールオーバーが提供されます。</br></br> このチュートリアルでは、[スタンドアロン] オプションを選択できます。 既定の “Standard_F4s” サイズの VM を使用します。  | 
   | Striim クラスターの名前|    <Striim_cluster_Name>|  Striim クラスターの名前。|
   | Striim クラスターのパスワード|   <Striim_cluster_password>|  クラスターのパスワード。|

   フォームに入力したら、 **[OK]** を選択して続行します。

1. **[Striim access settings]\(Striim のアクセス設定\)** ウィンドウで、Striim UI にログインするために使用する **[Public IP address]\(パブリック IP アドレス\)** (既定の値を選択)、 **[Domain name for Striim]\(Striim のドメイン名\)** 、 **[Admin password]\(管理者パスワード\)** を構成します。 VNET とサブネットを構成します (既定の値を選択)。 詳細を入力したら、 **[OK]** を選択して続行します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Striim のアクセス設定":::

1. Azure はデプロイを検証し、すべてが適切であることを確認します。検証の完了までには数分かかります。 検証が完了したら、 **[OK]** を選択します。
  
1. 最後に使用条件を確認し、 **[作成]** を選択して Striim インスタンスを作成します。 

## <a name="configure-the-source-database"></a>ソース データベースを構成する 

このセクションでは、データ移動のソースとして Oracle データベースを構成します。  Oracle に接続するために [Oracle JDBC ドライバー](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)が必要になります。 ソースの Oracle データベースから変更を読み取るには、[LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) または [XStream API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) のいずれかを使用できます。 Oracle データベースのデータの読み取り、書き込み、または永続化を行うには、Oracle JDBC ドライバーが Striim の Java クラスパスにある必要があります。

[ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) ドライバーをローカル マシンにダウンロードします。 これは、後で Striim クラスターにインストールします。

## <a name="configure-the-target-database"></a>ターゲット データベースを構成する

このセクションでは、データ移動のターゲットとして Azure Cosmos DB SQL API アカウントを構成します。

1. Azure portal を使用して [Azure Cosmos DB SQL API アカウント](create-cosmosdb-resources-portal.md)を作成します。

1. Azure Cosmos アカウントで **[データ エクスプローラー]** ウィンドウに移動します。 **[新しいコンテナー]** を選択して、新しいコンテナーを作成します。 "*製品*" と "*注文*" のデータを Oracle データベースから Azure Cosmos DB に移行するとします。 **Orders** という名前のコンテナーを持つ、**StriimDemo** という名前の新しいデータベースを作成します。 **1000 RU** でコンテナーをプロビジョニングします (この例では 1000 RU を使用しますが、ご自分のワークロード用に推定されるスループットを使用してください)。そして、パーティション キーとして **/ORDER_ID** を使用します。 これらの値は、ソース データによって異なります。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="SQL API アカウントの作成":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Oracle から Azure Cosmos DB へのデータ フローを構成する

1. ここで、Striim に戻ります。 Striim と対話する前に、先ほどダウンロードした Oracle JDBC ドライバーをインストールします。

1. Azure portal でデプロイした Striim インスタンスに移動します。 上部メニュー バーの **[接続]** ボタンを選択し、 **[SSH]** タブから **[VM ローカル アカウントを使用してログインする]** フィールドに URL をコピーします。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="SSH URL を取得する":::

1. 新しいターミナル ウィンドウを開き、Azure portal からコピーした SSH コマンドを実行します。 この記事では、MacOS でターミナルを使用しますが、PuTTY または Windows コンピューター上の別の SSH クライアントを使用して、同様の手順を実行できます。 プロンプトが表示されたら、**yes** と入力して続行し、前の手順で仮想マシンに対して設定した **パスワード** を入力します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Striim VM に接続する":::

1. 次に、新しいターミナル タブを開いて、前にダウンロードした **ojdbc8.jar** ファイルをコピーします。 次の SCP コマンドを使用して、jar ファイルをローカル コンピューターから Azure で実行されている Striim インスタンスの tmp フォルダーにコピーします。

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="ローカル コンピューターから Striim に Jar ファイルをコピーする":::

1. 次に、Striim インスタンスへの SSH 接続を実行したウィンドウに戻り、sudo としてログインします。 次のコマンドを使用して、**ojdbc8.jar** ファイルを **/tmp** ディレクトリから、Striim インスタンスの **lib** ディレクトリに移動します。

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Jar ファイルを lib フォルダーに移動する":::


1. 同じターミナル ウィンドウで次のコマンドを実行して、Striim サーバーを再起動します。

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim の起動には 1 分かかります。 状態を確認するには、次のコマンドを実行します。 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 次に、Azure に戻り、Striim VM のパブリック IP アドレスをコピーします。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Striim VM の IP アドレスをコピーする":::

1. Striim の Web UI に移動するために、ブラウザーで新しいタブを開き、パブリック IP をコピーし、その後に9080 を指定します。 **admin** ユーザー名と、Azure portal で指定した管理者パスワードを使用してサインインします。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Striim にサインインする":::

1. これで、Striim のホーム ページが表示されます。 3 つの異なるウィンドウ (**ダッシュボード**、**アプリ**、**SourcePreview**) があります。 [ダッシュボード] ウィンドウでは、リアルタイムでデータを移動し、視覚化することができます。 [アプリ] ウィンドウには、ストリーミング データ パイプライン (データ フロー) が含まれています。 このページの右側には SourcePreview があります。ここでは、データを移動する前にプレビューすることができます。

1. **[アプリ]** ウィンドウを選択します。今はこのウィンドウに焦点を当てます。 Striim について学ぶために使用できるさまざまなサンプル アプリがありますが、この記事では、独自のものを作成します。 右上隅にある **[アプリの追加]** ボタンを選択します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Striim アプリを追加する":::

1. Striim アプリケーションを作成するには、いくつかの異なる方法があります。 **[テンプレートを使って開始する]** を選択して、既存のテンプレートを使用して開始します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="テンプレートを使用してアプリを開始する":::

1. **[テンプレートの検索]** フィールドに「Cosmos」と入力し、 **[ターゲット:Azure Cosmos DB]** を選択し、 **[Oracle CDC to Azure Cosmos DB]\(Oracle CDC から Azure Cosmos DB へ\)** を選択します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Oracle CDC から Cosmos DB を選択する":::

1. 次のページで、アプリケーションに名前を付けます。 **oraToCosmosDB** などの名前を指定できます。その後、 **[保存]** を選択します。

1. 次に、ソース Oracle インスタンスのソース構成を入力します。 **[ソース名]** に値を入力します。 ソース名は、単に Striim アプリケーションの名前付け規則です。**src_onPremOracle** などを使用できます。 残りのソース パラメーター **[URL]** 、 **[ユーザー名]** 、 **[パスワード]** に値を入力し、Oracle からデータを読み取るリーダーとして **LogMiner** を選択します。 **[次へ]** をクリックして続行します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="ソース パラメーターを構成する":::

1. Striim によりユーザーの環境がチェックされ、ソース Oracle インスタンスに接続でき、適切な権限を持ち、CDC が適切に構成されていることが確認されます。 すべての値を検証した後、 **[次へ]** を選択します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="ソース パラメーターを検証する":::

1. 移行する Oracle データベースからテーブルを選択します。 たとえば、Orders テーブルを選択し、 **[次へ]** を選択します。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="ソース テーブルの選択":::

1. ソース テーブルを選択した後、マッピングやフィルター処理など、より複雑な操作を行うことができます。 この場合は、Azure Cosmos DB でソース テーブルのレプリカを作成するだけです。 **[次へ]** を選択してターゲットを構成します。

1. 次のようにターゲットを構成します。

   * **[ターゲット名]** - ターゲットのフレンドリ名を指定します。 
   * **[Input From]\(入力元\)** - ドロップダウン リストから、ソース Oracle 構成で作成した対象からの入力ストリームを選択します。 
   * **[コレクション]** - ターゲット Azure Cosmos DB の構成プロパティを入力します。 コレクションの構文は **SourceSchema.SourceTable, TargetDatabase.TargetContainer** です。 この例では、値は “SYSTEM.ORDERS, StriimDemo.Orders” です。 
   * **[AccessKey]** - Azure Cosmos アカウントの PrimaryKey です。
   * **[ServiceEndpoint]** – Azure Cosmos アカウントの URI です。これらは、Azure portal の **[キー]** セクションで見つけることができます。 

   **[保存]** を選択し、 **[次へ]** を選択します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="ターゲット パラメーターを構成する":::


1. 次に、Flow Designer が表示されます。ここでは、既存のコネクタのドラッグ アンド ドロップによってストリーミング アプリケーションを作成できます。 この時点では、フローには何も変更を加えません。 そのため、続行し、 **[アプリのデプロイ]** ボタンを選択してアプリケーションをデプロイします。
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="アプリケーションのデプロイ":::

1. [Deployment]\(デプロイ\) ウィンドウで、アプリケーションのある特定の部分をデプロイ トポロジの特定の部分で実行するかどうかを指定できます。 Azure を介して単純なデプロイ トポロジで実行しているので、既定のオプションを使用します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="既定のオプションを使用する":::

1. デプロイ後、ストリームをプレビューして、データ フローを確認できます。 **ウェーブ** アイコンとその横の眼球を選択します。 上部メニュー バーの **[Deployed]\(デプロイ済み\)** ボタンを選択し、 **[Start App]\(アプリの開始\)** を選択します。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="アプリを起動する":::

1. **CDC (変更データ キャプチャ)** リーダーを使用することにより、Striim は、データベースの新しい変更のみを取得します。 ソース テーブルを経由するデータ フローがある場合は、それが表示されます。 ただし、これはデモ テーブルであるため、ソースはどのアプリケーションにも接続されていません。 サンプル データ ジェネレーターを使用する場合は、Oracle データベースに一連のイベントを挿入できます。

1. Striim プラットフォームを経由するデータ フローが表示されます。 Striim は、お使いのテーブルに関連するすべてのメタデータも取得します。これは、データを監視し、データが正しいターゲットに到着することを確認するのに役立ちます。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="CDC パイプラインを構成する":::

1. 最後に、Azure にサインインし、Azure Cosmos アカウントに移動します。 データ エクスプローラーを更新すると、データが到着していることを確認できます。  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Azure で移行されたデータを検証する":::

Azure の Striim ソリューションを使用することにより、Oracle、Cassandra、MongoDB、およびその他のさまざまなソースから Azure Cosmos DB にデータを継続的に移行することができます。 詳細については、[Striim の Web サイト](https://www.striim.com/)や [Striim の 30 日間無料お試し版のダウンロード](https://go2.striim.com/download-free-trial) ページにアクセスしてください。Striim で移行パスを設定しようとして問題が発生した場合、[サポート リクエスト](https://go2.striim.com/request-support-striim)を提出してください。

## <a name="next-steps"></a>次のステップ

* データを Azure Cosmos DB SQL API に移行する場合は、[Striim を使用して Cassandra API アカウントにデータを移行する方法](cosmosdb-cassandra-api-migrate-data-striim.md)に関するページを参照してください。

* [Azure Cosmos DB メトリックを使用したデータの監視とデバッグ](use-metrics.md)