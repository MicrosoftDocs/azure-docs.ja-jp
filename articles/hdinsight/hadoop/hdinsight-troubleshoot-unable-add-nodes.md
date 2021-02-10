---
title: ノードを Azure HDInsight クラスターに追加できない
description: Azure HDInsight で Apache Hadoop クラスターにノードを追加できない理由のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944328"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>シナリオ:ノードを Azure HDInsight クラスターに追加できない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

ノードを Azure HDInsight クラスターに追加できません。

## <a name="cause"></a>原因

さまざまな理由が考えられます。

## <a name="resolution"></a>解像度

[クラスター サイズ](../hdinsight-scaling-best-practices.md)機能を使用して、クラスターに必要な追加のコア数を計算します。 これは、新しい worker ノードのコアの合計数に基づきます。 その後、次の手順を 1 つ以上試します。

* クラスターの場所に使用可能なコアがあるかどうかを確認します。

* 他の場所の使用可能なコアの数を確認します。 使用可能なコアが十分にある別の場所でクラスターを再作成することを検討します。

* 特定の場所のコア クォータを引き上げるには、HDInsight コア クォータの引き上げに関するサポート チケットを提出してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]