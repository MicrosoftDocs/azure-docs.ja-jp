---
title: Apache Spark での IllegalArgumentException エラー - Azure HDInsight
description: Azure Data Factory 用の Azure HDInsight での Apache Spark アクティビティのための IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 429659d605cdaf8aad978841e486a17da321cce4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929396"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>シナリオ:Azure HDInsight での Apache Spark アクティビティのための IllegalArgumentException

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

Azure Data Factory パイプラインで Spark アクティビティを実行しようとすると、次の例外が発生します。

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

アプリケーション jar ファイルが Spark クラスターの既定のストレージまたはプライマリ ストレージに配置されていない場合、Spark ジョブは失敗します。

これは、このバグで追跡されている Spark オープンソース フレームワークに関する既知の問題です。[fs.defaultFS とアプリケーション jar の URL が異なる場合に Spark のジョブが失敗する](https://issues.apache.org/jira/browse/SPARK-22587)。

この問題は Spark 2.3.0 で解決されました。

## <a name="resolution"></a>解像度

アプリケーション jar が、HDInsight クラスターの既定のストレージまたはプライマリ ストレージに格納されていることを確認します。 Azure Data Factory 場合は、ADF のリンクされたサービスが、HDInsight の既定のコンテナー (セカンダリ コンテナーではなく) を指していることを確認します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]