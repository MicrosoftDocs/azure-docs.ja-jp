---
title: Apache Hive ビューにエラー メッセージが表示されない - Azure HDInsight
description: Azure HDInsight クラスターの Apache Hive ビューで詳細情報が表示されずに、クエリが失敗します。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930994"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>シナリオ: Azure HDInsight の Apache Hive ビューにクエリのエラー メッセージが表示されない

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive ビューのクエリのエラー メッセージが次のように表示され、詳細情報がありません。

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

クエリ エラーのエラー メッセージが大きすぎて、Hive ビューのメイン ページに表示できないことがあります。

## <a name="resolution"></a>解決方法

Hive_view の右上にある [通知] タブで、完全なスタックトレースとエラー メッセージを確認します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]