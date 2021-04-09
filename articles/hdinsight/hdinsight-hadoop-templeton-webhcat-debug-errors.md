---
title: HDInsight の WebHCat エラーの説明と解決策 - Azure
description: HDInsight で WebHCat から返される一般的なエラーの原因とその解決策について説明します。
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ce7a21a2b5401c68c6b58bc934fb20b0225ba8ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945466"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>HDInsight で WebHCat から受信したエラーの説明と解決策

HDInsight で WebHCat を使用しているときに受信するエラーとその解決策について説明します。 WebHCat は、Azure PowerShell や Visual Studio 用 Data Lake Tools などのクライアント側ツールによって内部使用されます。

## <a name="what-is-webhcat"></a>WebHCat とは

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) は、Apache Hadoop のテーブルおよびストレージ管理層である [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog) 用の REST API です。 WebHCat は、HDInsight クラスターで既定で有効になっており、ジョブの送信、ジョブの状態の取得などの操作をクラスターにログインすることなく行うために、さまざまなツールで使用されます。

## <a name="modifying-configuration"></a>構成の変更

このドキュメントに示すエラーのいくつかは、構成されている最大値が超過したことが原因で発生します。 解決手順に値が変更可能であることが示されている場合は、Apache Ambari (web または REST API) を使用して値を変更します。 詳細については、「[Apache Ambari を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)」を参照してください

### <a name="default-configuration"></a>既定の構成

次の既定値を超えた場合、WebHCat のパフォーマンスが低下したり、エラーが発生したりします。

| 設定 | 実行内容 | 既定値 |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |同時にアクティブにできるジョブの最大数 (保留中または実行中) |10,000 |
| [templeton.exec.max-procs][max-procs] |同時に処理できる要求の最大数 |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |ジョブの履歴が保持される日数 |7 日 |

## <a name="too-many-requests"></a>要求が多すぎます

**HTTP 状態コード**:429

| 原因 | 解像度 |
| --- | --- |
| WebHCat によって処理される 1 分間あたりの最大同時要求数を超過しました (既定値は 20)。 |最大同時要求数を超える数の要求を送信しないようにワークロードを減らすか、`templeton.exec.max-procs` を変更して同時要求の上限を増やします。 詳細については、「[構成の変更](#modifying-configuration)」を参照してください。 |

## <a name="server-unavailable"></a>Server unavailable

**HTTP 状態コード**:503

| 原因 | 解像度 |
| --- | --- |
| この状態コードは、通常、クラスターのプライマリ ヘッドノードとセカンダリ ヘッドノードの間のフェールオーバー時に発生します。 |2 分待ってから操作をやり直してください。 |

## <a name="bad-request-content-could-not-find-job"></a>Bad request Content:Could not find job

**HTTP 状態コード**:400

| 原因 | 解像度 |
| --- | --- |
| ジョブ履歴クリーナーによってジョブの詳細がクリーンアップされた。 |ジョブ履歴の既定の保有期間は 7 日間です。 既定の保持期間は、`mapreduce.jobhistory.max-age-ms` を修正することで変更できます。 詳細については、「[構成の変更](#modifying-configuration)」を参照してください。 |
| フェールオーバーによってジョブが強制終了した |最大で 2 分間にわたってジョブの送信を再試行してください。 |
| 無効なジョブ ID が使用された |ジョブ ID が正しいことを確認してください |

## <a name="bad-gateway"></a>Bad gateway

**HTTP 状態コード**:502

| 原因 | 解像度 |
| --- | --- |
| WebHCat プロセス内で内部ガベージ コレクションが行われている。 |ガベージ コレクションが終了するまで待つか、または WebHCat サービスを再起動します。 |
| ResourceManager サービスからの応答の待機中にタイムアウトが発生した。 このエラーは、アクティブなアプリケーションの数が構成された最大値 (既定値は 10,000) に達した場合に発生することがあります。 |現在実行中のジョブが完了するまで待つか、または `yarn.scheduler.capacity.maximum-applications`を変更して同時実行ジョブの制限値をより大きな値にします。 詳細については、「[構成の変更](#modifying-configuration)」セクションをご覧ください。 |
| `Fields` が `*` に設定されているときに、[GET/jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) 呼び出しですべてのジョブを取得しようとした。 |"*すべて*" のジョブの詳細を取得しないでください。 代わりに、`jobid` を使用して、特定のジョブ ID より大きいジョブの詳細のみを取得してください。 または、`Fields` を使用しないでください。 |
| ヘッドノードのフェールオーバー中に WebHCat サービスがダウンした。 |2 分待ってから操作をやり直してください。 |
| WebHCat を通じて送信された 500 個を超えるジョブが保留中になっている。 |現在保留中のジョブが完了するのを待ってから、次のジョブを送信します。 |

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml