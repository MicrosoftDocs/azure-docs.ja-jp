---
title: Striim を使用して Azure Cosmos DB Cassandra API アカウントにデータを移行する
description: Striim を使用して Oracle データベースから Azure Cosmos DB Cassandra API アカウントにデータを移行する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 31273105c2f4de6950eae6a66c50264803197642
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981876"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Striim を使用して Azure Cosmos DB Cassandra API アカウントにデータを移行する

Azure Marketplace の Striim イメージは、データウェアハウスとデータベースから Azure への継続的なリアルタイム データ移動を提供します。 データの移動中に、インライン非正規化とデータ変換の実行、およびリアルタイム分析とデータ レポートのシナリオの有効化を行うことができます。 Striim を使用してエンタープライズ データを Azure Cosmos DB Cassandra API に継続的に移動することは簡単に開始できます。 Azure には、Striim のデプロイと Azure Cosmos DB へのデータの移行を容易にするマーケットプレース オファリングが用意されています。 

この記事では、Striim を使用して **Oracle データベース**から **Azure Cosmos DB Cassandra API アカウント**にデータを移行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

* オンプレミスで実行されている、データが含まれた Oracle データベース。

## <a name="deploy-the-striim-marketplace-solution"></a>Striim マーケットプレース ソリューションをデプロイする

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[リソースの作成]** を選択し、Azure Marketplace で "**Striim**" を検索します。 最初のオプションを選択し、 **[作成]** します。

   ![Striim の Marketplace 項目を見つける](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. 次に、Striim インスタンスの構成プロパティを入力します。 Striim 環境が仮想マシンにデプロイされます。 **[基本]** ウィンドウから、**VM ユーザー名**、**VM パスワード**を入力します (このパスワードは、VM への SSH 接続に使用されます)。 ご使用の**サブスクリプション**、**リソース グループ**、および Striim をデプロイする**場所の詳細**を選択します。 完了したら、 **[OK]** を選択します。

   ![Striim の基本設定を構成する](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. **[Striim Cluster settings]\(Striim クラスターの設定\)** ウィンドウで、Striim のデプロイの種類と仮想マシンのサイズを選択します。

   |Setting | 値 | 説明 |
   | ---| ---| ---|
   |Striim のデプロイの種類 |スタンドアロン | Striim は、 **[スタンドアロン]** または **[クラスター]** のデプロイの種類で実行できます。 スタンドアロン モードでは、Striim サーバーは単一の仮想マシンにデプロイされます。データ ボリュームに応じて VM のサイズを選択できます。 クラスター モードでは、Striim サーバーは、選択されたサイズの 2 つ以上の VM にデプロイされます。 ノードの数が 2 つを超えるクラスター環境では、自動の高可用性とフェールオーバーが提供されます。</br></br> このチュートリアルでは、[スタンドアロン] オプションを選択できます。 既定の “Standard_F4s” サイズの VM を使用します。 | 
   | Striim クラスターの名前|    <Striim_cluster_Name>|  Striim クラスターの名前。|
   | Striim クラスターのパスワード|   <Striim_cluster_password>|  クラスターのパスワード。|

   フォームに入力したら、 **[OK]** を選択して続行します。

1. **[Striim access settings]\(Striim のアクセス設定\)** ウィンドウで、Striim UI にログインするために使用する **[Public IP address]\(パブリック IP アドレス\)** (既定の値を選択)、 **[Domain name for Striim]\(Striim のドメイン名\)** 、 **[Admin password]\(管理者パスワード\)** を構成します。 VNET とサブネットを構成します (既定の値を選択)。 詳細を入力したら、 **[OK]** を選択して続行します。

   ![Striim のアクセス設定](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure はデプロイを検証し、すべてが適切であることを確認します。検証の完了までには数分かかります。 検証が完了したら、 **[OK]** を選択します。
  
1. 最後に使用条件を確認し、 **[作成]** を選択して Striim インスタンスを作成します。 

## <a name="configure-the-source-database"></a>ソース データベースを構成する

このセクションでは、データ移動のソースとして Oracle データベースを構成します。  Oracle に接続するために [Oracle JDBC ドライバー](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)が必要になります。 ソースの Oracle データベースから変更を読み取るには、[LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) または [XStream API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) のいずれかを使用できます。 Oracle データベースのデータの読み取り、書き込み、または永続化を行うには、Oracle JDBC ドライバーが Striim の Java クラスパスにある必要があります。

[ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) ドライバーをローカル マシンにダウンロードします。 これは、後で Striim クラスターにインストールします。

## <a name="configure-target-database"></a>ターゲット データベースを構成する

このセクションでは、データ移動のターゲットとして Azure Cosmos DB Cassandra API アカウントを構成します。

1. Azure portal を使用して [Azure Cosmos DB Cassandra API アカウント](create-cassandra-dotnet.md#create-a-database-account)を作成します。

1. Azure Cosmos アカウントで **[データ エクスプローラー]** ウィンドウに移動します。 **[新しいテーブル]** を選択して、新しいコンテナーを作成します。 "*製品*" と "*注文*" のデータを Oracle データベースから Azure Cosmos DB に移行するとします。 Orders コンテナーを使用して、**StriimDemo** という名前の新しいキースペースを作成します。 **1000 RU** でコンテナーをプロビジョニングします (この例では 1000 RU を使用しますが、ご自分のワークロード用に推定されるスループットを使用してください)。そして、主キーとして **/ORDER_ID** を使用します。 これらの値は、ソース データによって異なります。 

   ![Cassandra API アカウントを作成する](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Oracle から Azure Cosmos DB へのデータ フローを構成する

1. ここで、Striim に戻ります。 Striim と対話する前に、先ほどダウンロードした Oracle JDBC ドライバーをインストールします。

1. Azure portal でデプロイした Striim インスタンスに移動します。 上部メニュー バーの **[接続]** ボタンを選択し、 **[SSH]** タブから **[VM ローカル アカウントを使用してログインする]** フィールドに URL をコピーします。

   ![SSH URL を取得する](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. 新しいターミナル ウィンドウを開き、Azure portal からコピーした SSH コマンドを実行します。 この記事では、MacOS でターミナルを使用しますが、PuTTY または Windows コンピューター上の別の SSH クライアントを使用して、同様の手順を実行できます。 プロンプトが表示されたら、**yes** と入力して続行し、前の手順で仮想マシンに対して設定した**パスワード**を入力します。

   ![Striim VM に接続する](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. 次に、新しいターミナル タブを開いて、前にダウンロードした **ojdbc8.jar** ファイルをコピーします。 次の SCP コマンドを使用して、jar ファイルをローカル コンピューターから Azure で実行されている Striim インスタンスの tmp フォルダーにコピーします。

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![ローカル コンピューターから Striim に Jar ファイルをコピーする](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. 次に、Striim インスタンスへの SSH 接続を実行したウィンドウに戻り、sudo としてログインします。 次のコマンドを使用して、**ojdbc8.jar** ファイルを **/tmp** ディレクトリから、Striim インスタンスの **lib** ディレクトリに移動します。

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Jar ファイルを lib フォルダーに移動する](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


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

   ![Striim VM の IP アドレスをコピーする](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Striim の Web UI に移動するために、ブラウザーで新しいタブを開き、パブリック IP をコピーし、その後に9080 を指定します。 **admin** ユーザー名と、Azure portal で指定した管理者パスワードを使用してサインインします。

   ![Striim にサインインする](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. これで、Striim のホーム ページが表示されます。 3 つの異なるウィンドウ (**ダッシュボード**、**アプリ**、**SourcePreview**) があります。 [ダッシュボード] ウィンドウでは、リアルタイムでデータを移動し、視覚化することができます。 [アプリ] ウィンドウには、ストリーミング データ パイプライン (データ フロー) が含まれています。 このページの右側には SourcePreview があります。ここでは、データを移動する前にプレビューすることができます。

1. **[アプリ]** ウィンドウを選択します。今はこのウィンドウに焦点を当てます。 Striim について学ぶために使用できるさまざまなサンプル アプリがありますが、この記事では、独自のものを作成します。 右上隅にある **[アプリの追加]** ボタンを選択します。

   ![Striim アプリを追加する](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Striim アプリケーションを作成するには、いくつかの異なる方法があります。 このシナリオでは、 **[最初から行う]** を選択します。

   ![アプリを最初から開始する](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. **oraToCosmosDB** のような分かりやすい名前をアプリケーションに指定し、 **[保存]** を選択します。

   ![新しいアプリケーションを作成する](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. Flow Designer が表示されます。ここでは、既存のコネクタのドラッグ アンド ドロップによってストリーミング アプリケーションを作成できます。 検索バーに **[Oracle]** と入力し、**Oracle CDC** ソースをアプリのキャンバスにドラッグ アンド ドロップします。  

   ![Oracle CDC ソース](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Oracle インスタンスのソース構成プロパティを入力します。 ソース名は、単に Striim アプリケーションの名前付け規則です。**src_onPremOracle** のような名前を使用できます。 さらに、アダプターの種類、接続 URL、ユーザー名、パスワード、テーブル名などの詳細を入力します。 **[保存]** を選択して続行します。

   ![ソース パラメーターを構成する](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. 次に、ストリームのウェーブ アイコンをクリックして、ターゲット Azure Cosmos DB インスタンスを接続します。 

   ![Connect to target](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. ターゲット Azure Cosmos DB インスタンスの構成プロパティを入力し、 **[保存]** を選択して続行します。

   ![ターゲット プロパティを構成する](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![ターゲット プロパティを構成する](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. 次に、続行して、Striim アプリケーションを実行します。 上部のメニュー バーで、 **[Created]\(作成済み\)** を選択し、次に **[Deploy App]\(アプリをデプロイ\)** を選択します。 [Deployment]\(デプロイ\) ウィンドウで、アプリケーションのある特定の部分をデプロイ トポロジの特定の部分で実行するかどうかを指定できます。 Azure を介して単純なデプロイ トポロジで実行しているので、既定のオプションを使用します。

   ![アプリケーションのデプロイ](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. 次に、ストリームをプレビューして、Striim を経由するデータ フローを確認します。 ウェーブのアイコンをクリックし、その横の目のアイコンをクリックします。 デプロイ後、ストリームをプレビューして、データ フローを確認できます。 **ウェーブ** アイコンとその横の**眼球**を選択します。 上部メニュー バーの **[Deployed]\(デプロイ済み\)** ボタンを選択し、 **[Start App]\(アプリの開始\)** を選択します。

   ![アプリを起動する](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. **CDC (変更データ キャプチャ)** リーダーを使用することにより、Striim は、データベースの新しい変更のみを取得します。 ソース テーブルを経由するデータ フローがある場合は、それが表示されます。 ただし、これはサンプル テーブルであるため、ソースはどのアプリケーションにも接続されていません。 サンプル データ ジェネレーターを使用する場合は、Oracle データベースに一連のイベントを挿入できます。

1. Striim プラットフォームを経由するデータ フローが表示されます。 Striim は、お使いのテーブルに関連するすべてのメタデータも取得します。これは、データを監視し、データが正しいターゲットに到着することを確認するのに役立ちます。

   ![CDC パイプラインを設定する](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. 最後に、Azure にサインインし、Azure Cosmos アカウントに移動します。 データ エクスプローラーを更新すると、データが到着していることを確認できます。 

Azure の Striim ソリューションを使用することにより、Oracle、Cassandra、MongoDB、およびその他のさまざまなソースから Azure Cosmos DB にデータを継続的に移行することができます。 Striim で移行パスを設定する際の問題については、[Striim Web サイト](https://go2.striim.com/request-support-striim)でサポート要求を提出してください。


## <a name="next-steps"></a>次の手順

* データを Azure Cosmos DB SQL API に移行する場合は、[Striim を使用して Cassandra API アカウントにデータを移行する方法](cosmosdb-sql-api-migrate-data-striim.md)に関するページを参照してください。

* [Azure Cosmos DB メトリックを使用したデータの監視とデバッグ](use-metrics.md)