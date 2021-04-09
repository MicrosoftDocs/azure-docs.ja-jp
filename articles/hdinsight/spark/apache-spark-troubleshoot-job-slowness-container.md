---
title: Azure HDInsight ストレージに多数のファイルが含まれている場合に Apache Spark の速度が低下する
description: Azure ストレージ コンテナーに Azure HDInsight のファイルが多数含まれる場合、Apache Spark ジョブの実行速度が低下する
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: c26baec66248ca00ef212acf3d773c2566b3aea9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946365"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Azure ストレージ コンテナーに Azure HDInsight のファイルが多数含まれる場合、Apache Spark ジョブの実行速度が低下する

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

HDInsight クラスターの実行時にファイルやサブフォルダーが多数あると、Azure ストレージ コンテナーに書き込みを行う Apache Spark ジョブが低速になる。 たとえば、新しいコンテナーへの書き込みでは 20 秒かかる一方で、20 万個のファイルが含まれるコンテナーへの書き込みでは約 2 分かかります。

## <a name="cause"></a>原因

これは Spark の既知の問題です。 この速度低下は、Spark ジョブの実行中に `ListBlob` および `GetBlobProperties` 操作が原因で発生します。

パーティションを追跡するために、Spark はディレクトリ構造に関する情報を格納する `FileStatusCache` を保持する必要があります。 このキャッシュを使用して、Spark はパスを解析し、使用可能なパーティションを認識できます。 パーティションを追跡することの利点は、データを読み取るときに、Spark が必要なファイルにのみアクセスすることです。 この情報を最新の状態に保つために、ユーザーが新しいデータを書き込むときに Spark はディレクトリにあるすべてのファイルをリストし、このキャッシュを更新する必要があります。

Spark 2.1 では、書き込みのたびにキャッシュを更新する必要はありませんが、Spark は、既存のパーティション列が現在の書き込み要求で示されている列と一致するかどうかをチェックします。そのため、書き込みのたびに最初にリスト操作が行われます。

Spark 2.2 では、追加モードでデータを書き込むときに、このパフォーマンスの問題を修正する必要があります。

## <a name="resolution"></a>解決方法

パーティション分割されたデータ セットを作成する場合、重要なのは、`FileStatusCache` を更新するために Spark がリストする必要があるファイルの数を制限するパーティション構成を使用することです。

N % 100 == 0 (100 は単なる例です) である N 番目のマイクロ バッチごとに、既存のデータを、Spark で読み込むことができる別ディレクトリに移動します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]