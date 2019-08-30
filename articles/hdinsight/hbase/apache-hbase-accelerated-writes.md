---
title: Azure HDInsight の Apache HBase 用書き込みアクセラレータ
description: Premium マネージド ディスクを使用して Apache HBase のログ先行書き込みのパフォーマンスを向上させる、Azure HDInsight の書き込みアクセラレータ機能の概要を示します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 8b24c7517402aa6f29c95c0cd0f58bb1d51e1082
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876480"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight の Apache HBase 用書き込みアクセラレータ

この記事では、Azure HDInsight の Apache HBase 用**書き込みアクセラレータ**機能の背景情報と、書き込みパフォーマンスを向上させるために効果的に使用する方法を示します。 **書き込みアクセラレータ**では、[Azure Premium SSD マネージド ディスク](../../virtual-machines/linux/disks-types.md#premium-ssd)を使用して、Apache HBase のログ先行書き込み (WAL) のパフォーマンスを向上させます。 Apache HBase の詳細については、「[HDInsight の Apache HBase とは](apache-hbase-overview.md)」を参照してください。

## <a name="overview-of-hbase-architecture"></a>HBase アーキテクチャの概要

HBase では、**行**は 1 つまたは複数の**列**で構成され、**行キー**によって識別されます。 複数の行によって**テーブル**が構成されます。 列には**セル**が含まれ、セルはその列内のタイムスタンプ付きバージョンの値です。 列は**列ファミリ**にグループ化され、列ファミリ内のすべての列は、**HFiles** という名前のストレージ ファイルにまとめて保存されます。

HBase の**リージョン**は、データ処理の負荷分散のために使用されます。 HBase では、最初にテーブルの行は 1 つのリージョンに格納されます。 テーブルのデータ量が増えると、行は複数のリージョン間に分散されます。 **リージョン サーバー**では、複数のリージョンに対する要求を処理できます。

## <a name="write-ahead-log-for-apache-hbase"></a>Apache HBase のログ先行書き込み

HBase では、最初に、データ更新はログ先行書き込み (WAL) と呼ばれる種類のコミット ログに書き込まれます。 更新が WAL に保存された後は、メモリ内の **MemStore** に書き込まれます。 メモリ内のデータがその最大容量に達すると、**HFile** としてディスクに書き込まれます。

MemStore がフラッシュされる前に **RegionServer** がクラッシュするか、または使用不可になった場合は、ログ先行書き込みを使用して更新を再生できます。 WAL がない場合、更新が **HFile** にフラッシュされる前に **RegionServer** がクラッシュすれば、これらのすべての更新が失われます。

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Azure HDInsight の Apache HBase 用書き込みアクセラレータ機能

書き込みアクセラレータ機能では、クラウド ストレージ内にあるログ先行書き込みを使用することで書き込み待機時間が長くなるという問題が解決されます。  HDInsight の Apache HBase クラスター用書き込みアクセラレータ機能では、Premium SSD マネージド ディスクをすべての RegionServer (ワーカー ノード) にアタッチします。 その後、ログ先行書き込みは、クラウド ストレージの代わりに、これらの Premium マネージド ディスクにマウントされている Hadoop ファイル システム (HDFS) に書き込まれます。  Premium マネージド ディスクでは、ソリッド ステート ディスク (SSD) を使用し、フォールト トレランスを備えた優れた I/O パフォーマンスを提供します。  アンマネージド ディスクとは異なり、1 つのストレージ ユニットがダウンしでも、同じ可用性セット内の他のストレージ ユニットには影響を与えません。  その結果、マネージド ディスクでは、書き込み待機時間が短縮され、アプリケーションの回復性が向上します。 Azure マネージド ディスクの詳細については、「[Azure マネージド ディスクの概要](../../virtual-machines/windows/managed-disks-overview.md)」を参照してください。 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>HDInsight の HBase 用書き込みアクセラレータを有効にする方法

書き込みアクセラレータ機能を使用して新しい HBase クラスターを作成するには、**手順 3 の「ストレージ」** に達するまで、[HDInsight のクラスターの設定](../hdinsight-hadoop-provision-linux-clusters.md)手順に従ってください。 **[metastore の設定]** で、 **[Enable Accelerated Writes (preview)]\(書き込みアクセラレータを有効にする (プレビュー)\)** の横にあるチェックボックスをオンにします。 次に、クラスターの作成の残りの手順に進みます。

![HDInsight の Apache HBase 用書き込みアクセラレータ オプションを有効にする](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>その他の考慮事項

データの持続性を維持するには、少なくとも 3 つのワーカー ノードを使用してクラスターを作成します。 クラスターを作成した後は、2 つ以下のワーカー ノードにスケール ダウンすることはできません。

ログ先行書き込みデータが失われるのを防ぐために、クラスターを削除する前に、HBase テーブルをフラッシュするか、または無効にします。

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>次の手順

* [機能](https://hbase.apache.org/book.html#wal)に関する Apache HBase の公式ドキュメント
* 書き込みアクセラレータを使用するよう HDInsight の Apache HBase クラスターをアップグレードするには、「[Apache HBase クラスターを新しいバージョンに移行する](apache-hbase-migrate-new-version.md)」を参照してください。
