---
title: Azure HDInsight で hbase hbck から不一致が返される
description: Azure HDInsight で hbase hbck から不一致が返される
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: cbe4231bbecdf279c637cd334336437a020188d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936994"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で `hbase hbck` コマンドから不一致が返される

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue-region-is-not-in-hbasemeta"></a>問題: リージョンが `hbase:meta` に存在しない

リージョン xxx は HDFS 上にありますが、`hbase:meta` に登録されておらず、どのリージョン サーバーにもデプロイされていません。

### <a name="cause"></a>原因

状況に応じて異なります。

### <a name="resolution"></a>解決策

1. 次を実行してメタ テーブルを修正します。

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 次を実行してリージョンを RegionServer に割り当てます。

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>問題: リージョンがオフライン

リージョン xxx は、どの RegionServer にもデプロイされていません。 これは、リージョンは `hbase:meta` 内にありますがオフラインであることを意味します。

### <a name="cause"></a>原因

状況に応じて異なります。

### <a name="resolution"></a>解決策

次を実行してリージョンをオンラインにします。

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>問題: リージョンの開始/終了キーが同じである

### <a name="cause"></a>原因

状況に応じて異なります。

### <a name="resolution"></a>解決策

これらの重複するリージョンを手動でマージします。 HBase HMaster Web UI テーブル セクションに移動し、問題が発生しているテーブル リンクを選択します。 このテーブルに属している各リージョンの開始キー/終了キーが表示されます。 次に、これらの重複するリージョンをマージします。 HBase シェルで`merge_region 'xxxxxxxx','yyyyyyy', true` を実行します。 次に例を示します。

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

このシナリオでは、RegionA と RegionC をマージし、RegionD を Regionc と同じキー範囲にして、RegionC と RegionD をマージする必要があります。 xxxxxxx と yyyyyy は、各リージョン名の末尾にあるハッシュ文字列です。 ここでは、連続した 2 つのリージョンをマージしないように注意してください。 A と C のマージなど、各マージの後、HBase によって RegionD 上で圧縮が開始されます。 圧縮が完了するのを待ってから、もう 1 つの RegionD とのマージを実行します。 圧縮の状態は、HBase HMaster UI のリージョン サーバー ページで確認できます。

---

## <a name="issue-cant-load-regioninfo"></a>問題: `.regioninfo` を読み込めない

リージョン `/hbase/data/default/tablex/regiony` の `.regioninfo` を読み込めません。

### <a name="cause"></a>原因

最も可能性の高い原因は、RegionServer のクラッシュ時または VM の再起動時のリージョンの部分的な削除です。 現時点で Azure Storage はフラットな BLOB ファイル システムであり、一部のファイル操作はアトミックではありません。

### <a name="resolution"></a>解決方法

以下の残りのファイルとフォルダーを手動でクリーンアップします。

1. `hdfs dfs -ls /hbase/data/default/tablex/regiony` を実行し、その下にまだ残っているフォルダー/ファイルを確認します。

1. `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` を実行し、すべての子ファイル/フォルダーを削除します。

1. `hdfs dfs -rmr /hbase/data/default/tablex/regiony` を実行し、リージョン フォルダーを削除します。

---

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。