---
title: Blitzz を使用して Oracle から Azure Cosmos DB Cassandra API にデータを移行する
description: Blitzz を使用して Oracle データベースから Azure Cosmos DB Cassandra API にデータを移行する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336443"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Blitzz を使用して Oracle から Azure Cosmos DB Cassandra API アカウントにデータを移行する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra API は、次のようなさまざまな理由により、Oracle 上で実行されているエンタープライズ ワークロードに適した選択肢になりました。

* **より優れたスケーラビリティと可用性:** 単一障害点がなくなり、スケーラビリティとお使いのアプリケーションの可用性が向上します。

* **大幅なコスト削減:** Azure Cosmos DB によってコストを節約できます。これには、VM、帯域幅、適用されるすべての Oracle ライセンスのコストが含まれます。 さらに、データ センター、サーバー、SSD ストレージ、ネットワーク、電気代を管理する必要がありません。

* **管理と監視のオーバーヘッドなし:** Azure Cosmos DB は、フル マネージドのクラウド サービスとして、無数の設定を管理および監視する際のオーバーヘッドを解消します。

データベースのワークロードをプラットフォーム間で移行するには、さまざまな方法があります。 [Blitzz](https://www.blitzz.io) とは、さまざまなデータベースから Azure Cosmos DB への移行をダウンタイムなしで実行するための安全で信頼性の高い方法を提供するツールです。 この記事では、Blitzz を使用して Oracle データベースから Azure Cosmos DB Cassandra API にデータを移行するために必要な手順について説明します。

## <a name="benefits-using-blitzz-for-migration"></a>移行に Blitzz を使用する利点

Blitzz の移行ソリューションでは、段階的なアプローチに従って複雑な運用ワークロードを移行します。 Blitzz のダウンタイムなしの移行プランの主な特徴は次のとおりです。

* Oracle データベースから Azure Cosmos DB へのビジネス ロジック (テーブル、インデックス、ビュー) の自動移行を提供します。 スキーマを手動で作成する必要はありません。

* Blitzz は、大量の並列データベース レプリケーションを提供します。 これにより、変更データ キャプチャ (CDC) という手法を使用することで、移行中にソースとターゲット両方のプラットフォームを同期することができます。 CDC を使用することで、Blitzz は継続的に、ソース データベース (Oracle) から変更のストリームをプルし、それを移行先データベース (Azure Cosmos DB) に適用します。

* フォールトトレラントであるため、システムでハードウェアまたはソフトウェアの障害が発生している間でもデータの配信が 1 回だけ保証されます。

* TLS/SSL、暗号化などのさまざまなセキュリティ手法を使用して、転送中のデータを保護します。

* これは、PL または SQL で記述された複雑なビジネス ロジックを Azure Cosmos DB の同等のビジネス ロジックに変換するサービスを提供します。

## <a name="steps-to-migrate-data"></a>データを移行する手順

このセクションでは、Blitzz を設定して Oracle データベースから Azure Cosmos DB にデータを移行するために必要な手順について説明します。

1. Blitzz replicant をインストールする予定のコンピューターから、セキュリティ証明書を追加します。 この証明書は、指定された Azure Cosmos DB アカウントを使用して TLS 接続を確立するために Blitzz replicant で必要になります。 この証明書は、次の手順を使用して追加できます。

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Blitzz のインストール ファイルとバイナリ ファイルを入手するには、[Blitzz Web サイト](https://www.blitzz.io)でデモをリクエストします。 または、チームに[メール](mailto:success@blitzz.io)を送信することもできます。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz replicant ツールのダウンロード":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz replicant のファイル":::

1. CLI ターミナルから、ソース データベースの構成を設定します。 **`vi conf/conn/oracle.yml`** コマンドを使用して構成ファイルを開き、Oracle ノードの IP アドレス、ポート番号、ユーザー名、パスワードなどの必要な詳細から成るコンマ区切りリストを追加します。 次のコードでは、構成ファイルの例を示します。

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="Oracle の接続エディターを開く":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Oracle の接続の構成":::

   構成の詳細を入力したら、そのファイルを保存して閉じます。

1. 必要に応じて、ソース データベースのフィルター ファイルを設定できます。 このフィルター ファイルでは、移行するスキーマまたはテーブルを指定します。 **`vi filter/oracle_filter.yml`** コマンドを使用して構成ファイルを開き、次の構成の詳細を入力します。

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   データベース フィルターの詳細を入力したら、そのファイルを保存して閉じます。

1. 次に、移行先データベースの構成を設定します。 構成を定義する前に、[Azure Cosmos DB Cassandra API アカウントを作成](create-cassandra-dotnet.md#create-a-database-account)します。 データから[適切なパーティション キーを選択](partitioning-overview.md#choose-partitionkey)し、キースペースと、移行されたデータを格納するテーブルを作成します。

1. データを移行する前に、コンテナーのスループットを、お使いのアプリケーションで迅速に移行するために必要な量に引き上げます。 たとえば、このスループットを 100,000 RU に引き上げることができます。 移行を開始する前にスループットをスケーリングすると、データの移行にかかる時間を短縮するのに役立ちます。 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Azure Cosmos のコンテナーのスループットをスケーリングする":::

   移行の完了後、スループットを下げる必要があります。 格納されたデータの量と各操作に必要な RU に基づいて、データの移行後に必要なスループットを見積もることができます。 必要な RU の見積もり方法の詳細については、「[コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)」と「[Azure Cosmos DB Capacity Planner を使用して RU/秒を見積もる](estimate-ru-with-capacity-planner.md)」の記事を参照してください。

1. **[接続文字列]** ウィンドウで、ご利用の Azure Cosmos アカウントの **コンタクト ポイント、ポート、ユーザー名**、および **プライマリ パスワード** を取得します。 これらの値は構成ファイルで使用します。

1. CLI ターミナルから、移行先データベースの構成を設定します。 **`vi conf/conn/cosmosdb.yml`** コマンドを使用して構成ファイルを開き、ホスト URI、ポート番号、ユーザー名、パスワードなどの必要なパラメーターから成るコンマ区切りリストを追加します。 構成ファイルの内容の例を次に示します。

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. 次に、Blitzz を使用してデータを移行します。 Blitzz replicant は、**フル** または **スナップショット** モードで実行できます。

   * **フル モード** - このモードでは、replicant が移行後も実行され続け、ソースの Oracle システムでの変更を待機します。 変更が検出されると、ターゲットの Azure Cosmos アカウントにリアルタイムでレプリケートされます。

   * **スナップショット モード** - このモードでは、スキーマの移行と 1 回限りのデータ レプリケーションを実行できます。 このオプションでは、リアルタイムのレプリケーションがサポートされていません。


   上記の 2 つのモードを使用すると、ダウンタイムなしで移行を実行できます。

1. データを移行するには、Blitzz replicant CLI ターミナルから次のコマンドを実行します。

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   replicant UI には、レプリケーションの進行状況が表示されます。 スキーマの移行とスナップショットの操作が完了すると、進行状況には 100% と表示されます。 移行が完了したら、ターゲットの Azure Cosmos データベースのデータを確認できます。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Oracle のデータ移行に関する出力":::

1. 移行にはフル モードを使用したため、ソースの Oracle データベースに対してデータの挿入、更新、削除などの操作を実行することができます。 後で、それらがターゲットの Azure Cosmos データベースにリアルタイムでレプリケートされていることを確認できます。 移行後は、Azure Cosmos コンテナー用に構成されているスループットを必ず下げるようにしてください。

1. replicant は、どの時点でも停止でき、 **--resume** スイッチを使用して再開できます。 replicant は、データの整合性を損なうことなく、停止した時点から再開されます。 次のコマンドは、resume スイッチの使用方法を示しています。

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

移行先へのデータ移行、リアルタイムの移行の詳細については、[Blitzz replicant のデモ](https://www.youtube.com/watch?v=y5ZeRK5A-MI)を参照してください。

## <a name="next-steps"></a>次のステップ

* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md) 
* [パーティション キーのベスト プラクティス](partitioning-overview.md#choose-partitionkey)
* 「[Azure Cosmos DB Capacity Planner を使用して RU/秒を見積もる](estimate-ru-with-capacity-planner.md)」の記事
