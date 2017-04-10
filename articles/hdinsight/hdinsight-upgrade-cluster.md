---
title: "Windows ベースの HDInsight から Linux ベースの HDInsight への移行 -Azure | Microsoft Docs"
description: "Windows ベースの HDInsight クラスターから Linux ベースの HDInsight クラスターへの移行方法について説明します。"
services: hdinsight
documentationcenter: 
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: bhanupr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: e249d2859f135bf1a49b152ce206dc66ddebb75f
ms.lasthandoff: 03/25/2017


---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight クラスターを新しいバージョンにアップグレードする
最新の HDInsight 機能を利用するために、HDInsight クラスターを最新バージョンにアップグレードすることをお勧めします。 HDInsight クラスターのバージョンをアップグレードするには、下のガイドラインに従います。

> [!NOTE]
> HDInsight クラスターのバージョン 3.2 および 3.3 は非推奨となる日が近づいています。 HDInsight のサポートされているバージョンについては、[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md#supported-hdinsight-versions)に関するページを参照してください。
>
>

## <a name="upgrade-tasks"></a>アップグレード タスク
HDInsight クラスターをアップグレードするワークフローは次のとおりです。

![アップグレード ワークフロー図](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. このドキュメントの各セクションを読んで、HDInsight クラスターに移行するときに必要になる場合がある変更について理解します。
2. テスト/品質保証環境として、クラスターを作成します。 クラスターの作成の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
3. 既存のジョブ、データ ソース、およびシンクを新しい環境にコピーします。 詳細については、「[テスト環境にデータをコピーする](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment)」を参照してください。
4. 検証テストを実行し、新しいクラスターで予期したとおりにジョブが動作していることを確認します。


すべて予期したとおりに動作していることを確認したら、移行のダウンタイムをスケジュールします。 このダウンタイム中に、次の操作を実行します。

1.    クラスター ノードでローカルに格納されている一時的なデータをバックアップします。 たとえば、ヘッド ノードに直接データを格納している場合です。
2.    既存のクラスターを削除します。
3.    同じ VNET サブネットの中に、前のクラスターで使用していたのと同じ既定のデータ ストアを使用する、最新の (またはサポートされている) バージョンの HDI クラスターを作成します。 これで、新しいクラスターで既存の運用データを引き続き使用できます。
4.    バックアップしたすべての一時的なデータをインポートします。
5.    新しいクラスターを使用して、ジョブを開始または処理を続行します。

## <a name="next-steps"></a>次のステップ
* [Linux ベースの HDInsight クラスターを作成する方法を確認する](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH を使用して HDInsight に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Ambari を使用して Linux ベースのクラスターを管理する](hdinsight-hadoop-manage-ambari.md)


