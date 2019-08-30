---
title: Azure ストレージ コンテナーに Azure HDInsight のファイルが多数含まれる場合、Apache Spark ジョブの実行速度が低下する
description: Azure ストレージ コンテナーに Azure HDInsight のファイルが多数含まれる場合、Apache Spark ジョブの実行速度が低下する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/21/2019
ms.openlocfilehash: 635b7adb8753b7e9490e8f14a0699c09297fdbbb
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899095"
---
# <a name="scenario-apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>シナリオ: Azure ストレージ コンテナーに Azure HDInsight のファイルが多数含まれる場合、Apache Spark ジョブの実行速度が低下する

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときの問題のトラブルシューティングの手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDInsight クラスターの実行時にファイルやサブフォルダーが多数あると、Azure ストレージ コンテナーに書き込みを行う Apache Spark ジョブが低速になる。 たとえば、新しいコンテナーへの書き込みでは 20 秒かかる一方で、20 万個のファイルが含まれるコンテナーへの書き込みでは約 2 分かかります。

## <a name="cause"></a>原因

これは Spark の既知の問題です。 この速度低下は、Spark ジョブの実行中に `ListBlob` および `GetBlobProperties` 操作が原因で発生します。

パーティションを追跡するために、Spark はディレクトリ構造に関する情報を格納する `FileStatusCache` を保持する必要があります。 このキャッシュを使用して、Spark はパスを解析し、使用可能なパーティションを認識できます。 パーティションを追跡することの利点は、データを読み取るときに、Spark が必要なファイルにのみアクセスすることです。 この情報を最新の状態に保つために、ユーザーが新しいデータを書き込むときに Spark はディレクトリにあるすべてのファイルをリストし、このキャッシュを更新する必要があります。

Spark 2.1 では、書き込みのたびにキャッシュを更新する必要はありませんが、Spark は、既存のパーティション列が現在の書き込み要求で示されている列と一致するかどうかをチェックします。そのため、書き込みのたびに最初にリスト操作が行われます。

Spark 2.2 では、追加モードでデータを書き込むときに、このパフォーマンスの問題を修正する必要があります。

## <a name="resolution"></a>解決策

パーティション分割されたデータ セットを作成する場合、重要なのは、`FileStatusCache` を更新するために Spark がリストする必要があるファイルの数を制限するパーティション構成を使用することです。

N % 100 == 0 (100 は単なる例です) である N 番目のマイクロ バッチごとに、既存のデータを、Spark で読み込むことができる別ディレクトリに移動します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
