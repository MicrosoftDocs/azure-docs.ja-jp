---
title: Azure HDInsight で接続をリセットした後のストレージ例外
description: Azure HDInsight で接続をリセットした後のストレージ例外
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: 82cad7fc68d650e5f525a8722d3e2f3e9865f456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936756"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で接続をリセットした後のストレージ例外

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

新しい Apache HBase テーブルを作成できません。

## <a name="cause"></a>原因

テーブルの切り捨て処理中に、ストレージ接続の問題が発生しました。 HBase メタデータ テーブルでテーブル エントリが削除されました。 1 つを除くすべての BLOB ファイルが削除されました。

ストレージ内に `/hbase/data/default/ThatTable` という名前のフォルダー BLOB がありませんでした。 WASB ドライバーで上記の BLOB ファイルの存在が検出され、親フォルダーが存在すると想定されたため `/hbase/data/default/ThatTable` という BLOB の作成が許可されなかったため、テーブルの作成は失敗します。

## <a name="resolution"></a>解決方法

1. Apache Ambari UI から、アクティブな HMaster を再起動します。 これにより、2 つのスタンバイ HMaster の 1 つがアクティブになり、新しいアクティブな HMaster でメタデータ テーブルの情報が再読み込みされます。 これにより、HMaster UI に `already-deleted` テーブルが表示されなくなります。

1. Cloud Explorer などの UI ツールを使用するか、または `hdfs dfs -ls /xxxxxx/yyyyy` のようなコマンドを実行して、孤立した BLOB ファイルを見つけることができます。 その BLOB を削除するには、`hdfs dfs -rmr /xxxxx/yyyy` を実行します。 たとえば、「 `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile` 」のように入力します。

これで、HBase に同じ名前の新しいテーブルを作成できるようになります。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。