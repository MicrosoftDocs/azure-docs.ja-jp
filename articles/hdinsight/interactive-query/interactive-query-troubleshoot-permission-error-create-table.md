---
title: Azure HDInsight の Apache Hive テーブル アクセス拒否エラー
description: Azure HDInsight で Apache Hive テーブルを作成しようとしているときのアクセス拒否エラー
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 6d91ca747bb264b4c7262eec2bb0745b8a40b7a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930916"
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

## <a name="resolution"></a>解決方法

これは、使用されているストレージ コンテナーに対する十分なアクセス許可がないことが原因で発生します。 Hive テーブルを作成するユーザーには、コンテナーに対する読み取り、書き込み、および実行のアクセス許可が必要です。 詳細については、「[HDP 2.2 での Apache Ranger を使用した Hive 認証のベスト プラクティス](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)」を参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]