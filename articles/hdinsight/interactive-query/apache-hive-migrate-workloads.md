---
title: Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する
description: HDInsight 3.6 上の Apache Hive のワークロードを HDInsight 4.0 に移行する方法について説明します。
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: c1809885c930c4d22dff3f30d6e874aacf0b540e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672554"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する

このドキュメントでは、HDInsight 3.6 上の Apache Hive と LLAP のワークロードを HDInsight 4.0 に移行する方法について説明します。 HDInsight 4.0 では、具体化されたビューやクエリ結果のキャッシュなどの新しい Hive と LLAP の機能が提供されます。 ワークロードを HDInsight 4.0 に移行すると、HDInsight 3.6 で利用できない Hive 3 の多くの新しい機能を使用できます。

この記事に含まれるサブジェクトは次のとおりです。

* HDInsight 4.0 への Hive メタデータの移行
* ACID テーブルと ACID 以外のテーブルの安全な移行
* HDInsight バージョン間での Hive セキュリティ ポリシーの保持
* HDInsight 3.6 から HDInsight 4.0 へのクエリの実行とデバッグ

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Apache Hive メタデータを HDInsight 4.0 に移行する

Hive の利点の 1 つは、外部データベース (Hive Metastore と呼ばれます) にメタデータをエクスポートする機能です。 **Hive Metastore** は、テーブル ストレージの場所、列名、テーブルのインデックス情報を含む、テーブルの統計情報の格納を担当します。 メタストア データベース スキーマは、Hive のバージョンによって異なります。 HDInsight 4.0 と互換になるように HDInsight 3.6 Hive Metastore をアップグレードするには、次の操作を行います。

1. 外部メタストアの新しいコピーを作成します。 HDInsight 3.6 と HDInsight 4.0 には異なるメタストア スキーマが必要で、1 つのメタストアを共有することはできません。 HDInsight クラスターへの外部メタストアのアタッチについて詳しくは、「[Azure HDInsight での外部メタデータ ストアの使用](../hdinsight-use-external-metadata-stores.md)」をご覧ください。 
2. 実行のノードの種類として "ヘッド ノード" を使用して、HDI 3.6 クラスターに対してスクリプト アクションを起動します。 [https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh](https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh ) の URI を、"Bash スクリプト URI" とマークされたテキスト ボックスに貼り付けます。"引数" とマークされたテキストボックスに、**コピーした** Hive metastore のサーバー名、データベース、ユーザー名、およびパスワードをスペース区切りで入力します。 サーバー名を指定するときに ".database.windows.net" を含めないでください。

> [!Warning]
> HDInsight 3.6 のメタデータ スキーマを HDInsight 4.0 のスキーマに変換するアップグレードを元に戻すことはできません。

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Hive テーブルを HDInsight 4.0 に移行する

前の Hive Metastore を HDInsight 4.0 に移行する一連の手順を完了すると、クラスター内から `show tables` または `show databases` を実行することによって、メタストアに記録されているテーブルとデータベースを HDInsight 4.0 クラスター内から表示できます。 HDInsight 4.0 クラスターでのクエリの実行について詳しくは、「[HDInsight バージョン間でのクエリの実行](#query-execution-across-hdinsight-versions)」をご覧ください。

ただし、テーブルからの実際のデータは、クラスターが必要なストレージ アカウントへのアクセス権を持つようになるまでアクセスできません。 HDInsight 4.0 クラスターが以前の HDInsight 3.6 クラスターと同じデータにアクセスできるようにするには、次の手順を実行します。

1. 書式設定された説明を使用して、テーブルまたはデータベースの Azure ストレージ アカウントを決定します。
2. HDInsight 4.0 クラスターが既に実行されている場合は、Ambari を使用してクラスターに Azure ストレージ アカウントをアタッチします。 まだ HDInsight 4.0 クラスターを作成していない場合は、Azure ストレージ アカウントがプライマリまたはセカンダリのクラスター ストレージ アカウントとして指定されていることを確認します。 HDInsight クラスターへのストレージ アカウントの追加について詳しくは、「[HDInsight にストレージ アカウントを追加する](../hdinsight-hadoop-add-storage.md)」をご覧ください。

> [!Note]
> テーブルは、HDInsight 3.6 と HDInsight 4.0 とで異なる方法で扱われます。 このため、異なるバージョンのクラスターで同じテーブルを共有することはできません。 HDInsight 3.6 を HDInsight 4.0 と同時に使用する場合は、バージョンごとに別々のデータのコピーが必要です。

Hive ワークロードには、ACID テーブルと ACID 以外のテーブルが混在している場合があります。 HDInsight 3.6 (Hive 2) 上の Hive と HDInsight 4.0 (Hive 3) 上の Hive の 1 つ主要な違いは、テーブルの ACID コンプライアンスです。 HDInsight 3.6 では、Hive ACID コンプライアンスを有効にするには追加の構成が必要ですが、HDInsight 4.0 テーブルは既定で ACID 準拠になります。 移行前に必要なアクションは、3.6 クラスター上の ACID テーブルに対して主要な圧縮を実行することだけです。 Hive ビューまたは Beeline から、次のクエリを実行します。

```bash
alter table myacidtable compact 'major';
```

HDInsight 3.6 と HDInsight 4.0 の ACID テーブルでは ACID のデルタの解釈が異なるので、この圧縮が必要です。 圧縮により、一貫性を保証する白紙の状態が適用されます。 [Hive の移行に関するドキュメント](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)のセクション 4 には、HDInsight 3.6 ACID テーブルの一括圧縮に関するガイダンスが含まれています。

metastore の移行と圧縮の手順が完了したら、実際のウェアハウスを移行することができます。 Hive ウェアハウスの移行を完了すると、HDInsight 4.0 ウェアハウスは次のプロパティを持つようになります。

* HDInsight 3.6 での外部テーブルは HDInsight 4.0 では外部テーブルとなります
* HDInsight 3.6 での非トランザクション マネージド テーブルは、HDInsight 4.0 では外部テーブルとなります
* HDInsight 3.6 でのトランザクション マネージド テーブルは、HDInsight 4.0 ではマネージド テーブルとなります

移行を実行する前に、ご利用のウェアハウスのプロパティを調整することが必要となる場合があります。 たとえば、何らかのテーブルがサード パーティ (HDInsight 3.6 クラスターなど) によってアクセスされることが予想される場合、そのテーブルは移行が完了したら外部テーブルとなる必要があります。 HDInsight の 4.0 では、マネージド テーブルのすべてがトランザクション テーブルです。 そのため、HDInsight 4.0 でのマネージド テーブルは、HDInsight 4.0 クラスターでのみアクセスされる必要があります。

ご利用のテーブルのプロパティが正しく設定されたら、SSH シェルを使用してクラスター ヘッドノードのいずれかから Hive ウェアハウス移行ツールを実行します。

1. SSH を使用して、そのクラスター ヘッドノードに接続します。 手順については、「[SSH を使用して HDInsight に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。
1. Hive ユーザーとして、`sudo su - hive` を実行することで、ログイン シェルを開きます。
1. `ls /usr/hdp` を実行することで、Hortonworks Data Platform スタック バージョンを特定します。 これにより、次のコマンドで使用する必要があるバージョン文字列が表示されます。
1. シェルから次のコマンドを実行します。 `${{STACK_VERSION}}` を、前の手順でのバージョン文字列に置き換えてください。

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

移行ツールが完了すると、Hive ウェアハウスは HDInsight 4.0 で利用できるようになります。 

> [!Important]
> HDInsight 4.0 でのマネージド テーブル (3.6 から移行されたテーブルを含む) に対して、その他のサービスまたはアプリケーション (HDInsight 3.6 クラスターを含む) からアクセスすることはできません。

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight バージョン間で Hive をセキュリティ保護する

HDInsight 3.6 以降では、HDInsight は、HDInsight Enterprise セキュリティ パッケージ (ESP) を使用して Azure Active Directory と統合されます。 ESP では、Kerberos と Apache Ranger を使用して、クラスター内の特定のリソースのアクセス許可が管理されます。 次の手順で、HDInsight 3.6 内で Hive に対してデプロイした Ranger ポリシーを HDInsight 4.0 に移行できます。

1. HDInsight 3.6 クラスター内の Ranger Service Manager パネルに移動します。
2. **HIVE** という名前のポリシーに移動し、json ファイルにポリシーをエクスポートします。
3. エクスポートされたポリシーの json 内で参照されているすべてのユーザーが新しいクラスターに存在することを確認します。 ユーザーがポリシーの json 内で参照されているのに新しいクラスターに存在しない場合は、新しいクラスターにユーザーを追加するか、ポリシーから参照を削除します。
4. HDInsight 4.0 クラスター内の **Ranger Service Manager** パネルに移動します。
5. **HIVE** という名前のポリシーに移動し、手順 2 からの ranger ポリシーの json をインポートします。

## <a name="query-execution-across-hdinsight-versions"></a>HDInsight バージョン間でのクエリの実行

HDInsight 3.6 クラスター内で Hive/LLAP クエリを実行し、デバッグする方法は 2 つあります。 HiveCLI ではコマンドライン エクスペリエンスが提供され、Tez ビュー/Hive ビューでは GUI ベースのワークフローが提供されます。

HDInsight 4.0 では、Hive CLI は BeeLine に置き換えられています。 HiveCLI は Hiveserver 1 用の Thrift クライアントであり、Beeline は Hiveserver 2 へのアクセスを提供する JDBC クライアントです。 Beeline は、その他の JDBC 互換のデータベース エンドポイントへの接続にも使用できます。 Beeline は、HDInsight 4.0 上ですぐに使用可能で、インストールは必要ありません。

HDInsight 3.6 では、Hive サーバーと対話するための GUI クライアントは Ambari Hive ビューです。 HDInsight 4.0 では、Hive ビューが Hortonworks Data Analytics Studio (DAS) に置き換えられます。 DAS では、HDInsight クラスターは標準で付属せず、公式にサポートされているパッケージではありません。 ただし、次のようにして DAS をクラスターにインストールできます。

実行のノードの種類として "ヘッド ノード" を使用して、ご利用のクラスターに対してスクリプト アクションを起動します。 次の URI を、"Bash スクリプト URI" とマークされたテキスト ボックスに貼り付けます: https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

https://<clustername>.azurehdinsight.net/das/ という URL を使用して Data Analytics Studio を起動できます



DAS がインストールされた後、クエリ ビューアーで実行したクエリが表示されない場合は、次の手順を実行します。

1. [DAS のインストールのトラブルシューティングに関するこのガイド](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)の説明に従って、Hive、Tez、DAS の構成を設定します。
2. 次の Azure ストレージ ディレクトリ構成がページ BLOB であり、`fs.azure.page.blob.dirs` の下に表示されることを確認します。
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 両方のヘッドノード上の HDFS、Hive、Tez、DAS を再起動します。

## <a name="next-steps"></a>次の手順

* [HDInsight 4.0 のお知らせ](../hdinsight-version-release.md)
* [HDInsight 4.0 の詳細情報](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID テーブル](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
