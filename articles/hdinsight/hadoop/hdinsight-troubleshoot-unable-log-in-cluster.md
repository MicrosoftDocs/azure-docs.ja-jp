---
title: Azure HDInsight クラスターにログインできない
description: Azure HDInsight で Apache Hadoop クラスターにログインできない理由のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944310"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>シナリオ:Azure HDInsight クラスターにログインできない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Azure HDInsight クラスターにログインできません。

## <a name="cause"></a>原因

さまざまな理由が考えられます。 クラスターまたはアプリのダッシュボードにログインする場合は、"クラスター ログイン" または HTTP 資格情報を使用することに注意してください。 リモート接続する場合は、Secure Shell (SSH) またはリモート デスクトップの資格情報を使用します。

## <a name="resolution"></a>解像度

一般的な問題を解決するには、次の手順のうち 1 つ以上を試してください。

* プライバシー モードの新しいブラウザー タブでクラスター ダッシュボードを開いてみます。

* SSH 資格情報を思い出すことができない場合、[Ambari UI 内で資格情報をリセット](../hdinsight-administer-use-portal-linux.md#change-passwords)できます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]