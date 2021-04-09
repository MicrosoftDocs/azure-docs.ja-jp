---
title: Azure HDInsight のサービス開始時のポートの競合
description: Azure HDInsight クラスターと対話するときのポートの競合問題のトラブルシューティング手順と可能な解決策。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f42e84d5d9c1dd49d9bf5604fe2f967eae0b6276
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943096"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>シナリオ:Azure HDInsight のサービス開始時のポートの競合

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

サービスを開始できない。

## <a name="cause"></a>原因

ポートの競合が存在します。

## <a name="resolution"></a>解像度

### <a name="method-1"></a>方法 1

次のコマンドを使用して、ポートの問題の影響を受けている実行中のすべてのプロセスを取得/終了します。

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

その後、サービスを開始します。

### <a name="method-2"></a>方法 2

ノードを再起動します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。