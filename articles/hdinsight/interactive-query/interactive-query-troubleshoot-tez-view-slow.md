---
title: Azure HDInsight 内での Apache Ambari Tez ビューの読み込みに時間がかかる
description: Azure HDInsight で Apache Ambari Tez ビューの読み込みに時間がかかる場合や、まったく読み込まれない場合があります
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930766"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>シナリオ: Azure HDInsight での Apache Ambari Tez ビューの読み込みに時間がかかる

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari Tez ビューの読み込みに時間がかかる場合や、まったく読み込まれない場合があります。 Ambari Tez ビューを読み込むときに、応答しなくなっているプロセスがヘッドノードに表示されることがあります。

## <a name="cause"></a>原因

Yarn ATS API にアクセスすると、クラスター上で多数の Hive ジョブが実行されている場合に、2017 年 10 月より前に作成されたクラスター上でパフォーマンスが低下することがあります。

## <a name="resolution"></a>解決方法

これは、2017 年 10 月に修正された問題です。 クラスターを再作成すると、この問題が再び発生することはなくなります。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]