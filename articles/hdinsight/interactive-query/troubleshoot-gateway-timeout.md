---
title: Azure HDInsight で Apache Ambari Hive ビューからクエリを実行するときの例外
description: Azure HDInsight で Apache Ambari Hive ビューから Apache Hive クエリを実行するときのトラブルシューティング手順。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933003"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Azure HDInsight で Apache Ambari Hive ビューからクエリを実行するときの例外

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari Hive ビューから Apache Hive クエリを実行すると、次のエラー メッセージが間欠的に表示されます。

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>原因

ゲートウェイ タイムアウト。

ゲートウェイ タイムアウト値は 2 分です。 Ambari Hive ビューからのクエリは、ゲートウェイを介して `/hive2` エンドポイントに送信されます。 クエリが正常にコンパイルされて受け入れられると、HiveServer によって `queryid` が返されます。 その後、クライアントが、クエリ状態をポーリングし続けます。 このプロセスで、HiveServer が 2 分以内に HTTP 応答を返さない場合、HDI ゲートウェイは、呼び出し元に 502.3 ゲートウェイ タイムアウト エラーをスローします。 このエラーは、多くの場合、処理のためにクエリが送信されたときに発生します。また、可能性は低くなりますが、状態の取得呼び出しで発生することもあります。 ユーザーには、このいずれかが表示されます。

HTTP ハンドラー スレッドは迅速にジョブを準備し、`queryid` を返すことが想定されます。 ただし、複数の理由により、すべてのハンドラー スレッドがビジー状態になり、新しいクエリと状態の取得呼び出しのタイムアウトが発生することがあります。

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP ハンドラー スレッドの役割

クライアントは HiveServer にクエリを送信するときに、フォアグラウンド スレッドで次のことを実行します。

* 要求を解析し、セマンティック検証を実行する
* ロックを取得する
* 必要に応じてメタストア参照を行う
* クエリをコンパイルする (DDL または DML)
* クエリ プランを準備する
* 承認を実行する (セキュリティで保護されたクラスターで該当するすべての Rangers ポリシーを実行する)

## <a name="resolution"></a>解決方法

状況を改善するための一般的な推奨事項をいくつか次に示します。

* 外部の Hive メタストアを使用している場合は、DB メトリックを確認し、データベースが過負荷になっていないことを確かめます。 メタストア データベース レイヤーをスケーリングすることを検討してください。

* 並列操作が有効になっていることを確認します (これにより、HTTP ハンドラー スレッドを並列で実行できます)。 値を確認するには、[Apache Ambari](../hdinsight-hadoop-manage-ambari.md) を起動し、**[Hive]** > **[Configs]\(構成\)** > **[詳細設定]** > **[Custom hive-site]\(カスタム Hive サイト\)** の順に移動します。 `hive.server2.parallel.ops.in.session` の値は `true` にする必要があります。

* クラスターの VM SKU が、負荷に対して小さくなりすぎないようにします。 複数のクラスターで作業を分割することを検討してください。 詳細については、「[クラスターの種類の選択](../hdinsight-capacity-planning.md#choose-a-cluster-type)」を参照してください。

* クラスターに Rangers がインストールされている場合は、クエリごとに評価する必要がある Rangers ポリシーが多すぎないかどうかを確認します。 重複しているポリシーまたは不要なポリシーを探してください。

* Ambari の **HiveServer2 ヒープ サイズ** の値を確認します。 **[Hive]**  >  **[Configs]\(構成\)**  >  **[設定]**  >  **[最適化]** に移動します。 値が 10 GB よりも大きいことを確認してください。 必要に応じて調整して、パフォーマンスを最適化します。

* Hive クエリが適切にチューニングされていることを確認します。 詳細については、「[Azure HDInsight での Apache Hive クエリの最適化](../hdinsight-hadoop-optimize-hive-query.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。