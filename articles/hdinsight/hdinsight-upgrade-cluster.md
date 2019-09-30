---
title: HDInsight クラスターを新しいバージョンにアップグレードする - Azure
description: Azure HDInsight クラスターを新しいバージョンにアップグレードするためのガイドラインを説明します。
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: a62da0d27d21589f7b75a89cc184ce9f456f1fed
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960694"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight クラスターを新しいバージョンにアップグレードする
最新の HDInsight 機能を利用するために、HDInsight クラスターを最新バージョンにアップグレードすることをお勧めします。 HDInsight クラスターのバージョンをアップグレードするには、下のガイドラインに従います。

> [!NOTE]  
> HDInsight のサポートされているバージョンについては、[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md#supported-hdinsight-versions)に関するページを参照してください。

## <a name="upgrade-tasks"></a>アップグレード タスク
HDInsight クラスターをアップグレードするワークフローは次のとおりです。

![アップグレード ワークフロー図](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. このドキュメントの各セクションを読んで、HDInsight クラスターに移行するときに必要になる場合がある変更について理解します。
2. テスト/品質保証環境として、クラスターを作成します。 クラスターの作成の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
3. 既存のジョブ、データ ソース、およびシンクを新しい環境にコピーします。
4. 検証テストを実行し、新しいクラスターで予期したとおりにジョブが動作していることを確認します。

すべて予期したとおりに動作していることを確認したら、移行のダウンタイムをスケジュールします。 このダウンタイム中に、次の操作を実行します。

1.  クラスター ノードでローカルに格納されている一時的なデータをバックアップします。 たとえば、ヘッド ノードに直接データを格納している場合です。
2.  既存のクラスターを削除します。
3.  同じ VNET サブネットの中に、前のクラスターで使用していたのと同じ既定のデータ ストアを使用する、最新の (またはサポートされている) バージョンの HDI クラスターを作成します。 これで、新しいクラスターで既存の運用データを引き続き使用できます。
4.  バックアップしたすべての一時的なデータをインポートします。
5.  新しいクラスターを使用して、ジョブを開始または処理を続行します。

## <a name="next-steps"></a>次の手順
* [Linux ベースの HDInsight クラスターを作成する方法を確認する](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH を使用して HDInsight に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache Ambari を使用して Linux ベースのクラスターを管理する](hdinsight-hadoop-manage-ambari.md)

