---
title: Azure HDInsight で Apache Hive テーブルを作成しようとしているときのアクセス拒否エラー
description: Azure HDInsight で Apache Hive テーブルを作成しようとしているときのアクセス拒否エラー
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8440bbc1cf7019e77c6274cbb11c013fb4e3e69d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091391"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で Apache Hive テーブルを作成しようとしているときのアクセス拒否エラー

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

テーブルを作成しようとすると、次のエラーが表示されます。

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

次の HDFS ストレージ コマンドを実行すると、同様のエラー メッセージが表示されます。

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>原因

Apache Hive でテーブルを作成する能力は、クラスターのストレージ アカウントに適用されるアクセス許可によって決定されます。 クラスター ストレージ アカウントのアクセス許可が正しくない場合、テーブルを作成することはできません。 これは、テーブルを作成するための適切な Ranger ポリシーを持っていても、"アクセス許可が拒否されました" というエラーが表示される可能性があることを意味します。

## <a name="resolution"></a>解決策

これは、使用されているストレージ コンテナーに対する十分なアクセス許可がないことが原因で発生します。 Hive テーブルを作成するユーザーには、コンテナーに対する読み取り、書き込み、および実行のアクセス許可が必要です。 詳細については、「[HDP 2.2 での Apache Ranger を使用した Hive 認証のベスト プラクティス](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)」を参照してください。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
