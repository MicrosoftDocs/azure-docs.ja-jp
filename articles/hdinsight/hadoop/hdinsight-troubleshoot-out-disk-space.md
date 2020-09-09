---
title: Azure HDInsight 内でのクラスター ノードのディスク領域の不足
description: Azure HDInsight での Apache Hadoop クラスター ノードのディスク領域に関する問題のトラブルシューティング。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: ead79ca0a37a270f03a305064c80426553db59ca
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628539"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>シナリオ:Azure HDInsight 内でのクラスター ノードのディスク領域の不足

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

次のようなエラー メッセージが表示されてジョブが失敗することがあります: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

または、`local-dirs usable space is below configured utilization percentage` のような Apache Ambari アラートが表示される場合があります。

## <a name="cause"></a>原因

Apache Yarn アプリケーション キャッシュによって、使用可能なすべてのディスク領域が消費されている可能性があります。 Spark アプリケーションが効率よく実行されていない可能性があります。

## <a name="resolution"></a>解決策

1. Ambari UI を使用して、ディスク領域が不足しているノードを特定します。

1. 問題となっているノード内のどのフォルダーがディスク領域に最も影響を与えているかを判断します。 まずノードに SSH で接続し、`df` を実行して、すべてのマウントのディスク使用量を一覧表示します。 通常、これは OSS によって使用される一時ディスクである `/mnt` です。 フォルダーに入り、「`sudo du -hs`」と入力すると、フォルダーの下にファイル サイズの概要が表示されます。 `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` のようなフォルダーが表示される場合、これはアプリケーションがまだ実行されていることを意味します。 これは、RDD の永続化または中間シャッフル ファイルが原因である可能性があります。

1. この問題を軽減するには、アプリケーションを強制終了します。これにより、そのアプリケーションによって使用されているディスク領域が解放されます。

1. ワーカー ノードで問題が頻繁に発生する場合は、クラスターの YARN ローカル キャッシュ設定を調整できます。

    Ambari UI を開き、[YARN] --> [Configs] (構成) --> [Advanced] (詳細) の順に選択します。  
    次の 2 つのプロパティをカスタム yarn-site.xml セクションに追加して保存します。

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. 上記の手順で問題が完全に解決されない場合は、アプリケーションを最適化します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
