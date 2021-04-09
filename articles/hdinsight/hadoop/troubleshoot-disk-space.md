---
title: Azure HDInsight でディスク領域を管理する
description: Azure HDInsight クラスターと対話する際のディスク領域の管理に関する問題のトラブルシューティング手順と可能な解決策。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 7164494cb08c4b419b9e4d96075ace3e52187497
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944822"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Azure HDInsight でディスク領域を管理する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="hive-log-configurations"></a>Hive ログの構成

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. **[Hive]**  >  **[構成]**  >  **[詳細]**  >  **[Advanced hive-log4j]\(詳細な hive-log4j\)** の順に移動します。 以下の設定を確認します。

    * `hive.root.logger=DEBUG,RFA`. これは既定値です。[ログ レベル](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html)を `INFO` に変更して、より少ないログ エントリを印刷するようにします。

    * `log4jhive.log.maxfilesize=1024MB`. これは既定値です。必要に応じて変更します。

    * `log4jhive.log.maxbackupindex=10`. これは既定値です。必要に応じて変更します。 パラメーターが省略されている場合、生成されるログ ファイルは無限になります。

## <a name="yarn-log-configurations"></a>Yarn ログ構成

次の構成を確認します。

* Apache Ambari

    1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

    1. **[Hive]**  >  **[構成]**  >  **[詳細]**  >  **[Resource Manager]** の順に移動します。 **[Enable Log Aggregation]\(ログの集計を有効にする\)** がオンになっているを確認します。 無効になっている場合、名前ノードではログがローカルに保持され、アプリケーションの完了や強制終了時にリモート ストアで集計されません。

* クラスター サイズがワークロードに適していることを確認します。 ワークロードが最近変更されたか、クラスターのサイズが変更された可能性があります。 クラスターを[スケール アップ](../hdinsight-scaling-best-practices.md)して、より高いワークロードに一致させます。

* `/mnt/resource` は孤立ファイルでいっぱいになることがあります (リソース マネージャーの再起動の場合と同様)。 必要に応じて、`/mnt/resource/hadoop/yarn/log` と `/mnt/resource/hadoop/yarn/local` を手動で消去します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。