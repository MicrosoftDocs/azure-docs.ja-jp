---
title: Azure HDInsight の Apache Phoenix の接続に関する問題
description: Azure HDInsight での Apache HBase と Apache Phoenix 間の接続に関する問題
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 2cc6556f681ece170bdfe02b985f56274c0faa1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936975"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>シナリオ: Azure HDInsight の Apache Phoenix の接続に関する問題

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Phoenix で Apache HBase に接続できません。 さまざまな理由が考えられます。

## <a name="cause-incorrect-ip"></a>原因: IP が正しくない

アクティブな Zookeeper ノードの IP が正しくありません。

### <a name="resolution"></a>解決方法

アクティブな Zookeeper ノードの IP は、 **[HBase]**  >  **[Quick Links]\(クイック リンク\)**  >  **[ZK (Active)]\(ZK (アクティブ)\)**  >  **[Zookeeper Info]\(Zookeeper 情報\)** へのリンクをたどることで、Ambari UI から識別できます。 必要に応じて IP を修正します。

---

## <a name="cause-systemcatalog-table-offline"></a>原因: SYSTEM.CATALOG テーブルがオフライン

`!tables` などのコマンドを実行すると、次のようなエラー メッセージが表示されます。

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

`count 'SYSTEM.CATALOG'` などのコマンドを実行すると、次のようなエラー メッセージが表示されます。

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>解決方法

Apache Ambari UI から、次の手順に従って、すべての ZooKeeper ノード上で HMaster サービスを再起動します。

1. HBase の **[Summary]\(概要\)** セクションで、**[HBase]** > **[Active HBase Master]\(アクティブ HBase Master\)** に移動します。

1. **[Components]\(コンポーネント\)** セクションで、HBase Master サービスを再起動します。

1. 残りのすべての **Standby HBase Master** サービスに対して、上記の手順を繰り返します。

HBase Master サービスの動作が安定し、回復が完了するまでに最大 5 分かかることがあります。 `SYSTEM.CATALOG` テーブルが正常な状態に戻った後、Apache Phoenix に対する接続の問題は自動的に解決されます。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。