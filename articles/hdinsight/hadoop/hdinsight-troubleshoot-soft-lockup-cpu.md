---
title: Azure HDInsight クラスターからのウォッチドッグ バグ ソフト ロックアップ CPU エラー
description: Azure HDInsight クラスターからのウォッチドッグ バグ ソフト ロックアップ CPU がカーネルの syslogs に出現する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 8f9b60c6e181c9f47635e7d46ce103032d395028
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087345"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>シナリオ: "watchdog:BUG: soft lockup CPU" (ウォッチドッグ: バグ: ソフト ロックアップ CPU) エラーが Azure HDInsight クラスターから発生する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

カーネルの syslogs に `watchdog: BUG: soft lockup - CPU` というエラー メッセージがあります。

## <a name="cause"></a>原因

Linux カーネルの[バグ](https://bugzilla.kernel.org/show_bug.cgi?id=199437)により、CPU ソフト ロックアップが発生しています。

## <a name="resolution"></a>解決策

カーネルの修正プログラムを適用します。 次のスクリプトでは、linux カーネルをアップグレードし、24 時間にわたってさまざまな時間にコンピューターを再起動します。 2 つのバッチでスクリプト アクションを実行します。 最初のバッチは、ヘッド ノードを除くすべてのノード上にあります。 2 つ目のバッチはヘッド ノード上にあります。 ヘッド ノードとその他のノード上で同時に実行しないでください。

1. Azure portal から HDInsight クラスターに移動します。

1. スクリプト アクションに移動します。

1. **[新規で送信]** を選択し、次のように入力を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | スクリプトの種類 | -Custom |
    | 名前 |カーネル ソフト ロックの問題の修正 |
    | Bash スクリプト URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | ノードの種類 |Worker、Zookeeper |
    | parameters |該当なし |

    新しいノードが追加されたときにスクリプトを実行するには、 **[このスクリプト操作は保持され、...]** を選択します。

1. **作成** を選択します。

1. 実行が成功するまで待ちます。

1. 手順 3 と同じ手順に従い、ヘッド ノードに対してスクリプト アクションを実行します。ただし、今回はノードの種類をHead にします。

1. 実行が成功するまで待ちます。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
