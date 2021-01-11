---
title: Azure HDInsight で Apache Yarn ログを読み取ることができない
description: Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776040"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>シナリオ:Azure HDInsight で Apache Yarn ログを読み取ることができない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

ストレージ アカウントから検出された Apache Yarn ログは、人間が読めるものではありません。 ファイル パーサーが動作せず、次のエラー メッセージが生成されます。

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn ログは `IndexFile` 形式に集約されますが、これはファイル パーサーでサポートされていません。

## <a name="resolution"></a>解決策

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. Ambari UI から **[YARN]**  >  **[Configs]\(構成\)**  >  **[Advanced]\(拡張\)**  >  **[Advanced yarn-site]\(拡張 YARN-サイト\)** に移動します。

1. WASB ストレージの場合:`yarn.log-aggregation.file-formats` の既定値は `IndexedFormat,TFile` です。 値を `TFile` に変更します。

1. ADLS ストレージの場合:`yarn.nodemanager.log-aggregation.compression-type` の既定値は `gz` です。 値を `none` に変更します。

1. 変更を保存し、影響を受けるすべてのサービスを再起動します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
