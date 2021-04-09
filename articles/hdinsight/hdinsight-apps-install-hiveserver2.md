---
title: Azure HDInsight 上で HiveServer2 をスケーリングする
description: エッジノードを使用して Azure HDInsight クラスター上で HiveServer2 を水平方向にスケーリングし、フォールト トレランスと可用性を向上させます。
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88226911"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>高可用性を実現するために Azure HDInsight クラスター上で HiveServer2 をスケーリングする

ご利用のクラスターに追加の HiveServer2 をデプロイして可用性と負荷分散を向上させる方法について説明します。 ヘッドノードのサイズを大きくする必要がない場合は、エッジ ノードを使用して HiveServer2 をデプロイすることもできます。 

> [!NOTE]
> 使用状況によっては、HiveServer2 の数を増やすと、Hive メタストアへの接続数が増加する可能性があります。 また、ご利用の Azure SQL データベースを確実に適切なサイズに設定してください。

## <a name="prerequisites"></a>前提条件

このガイドを使用するには、次の記事を理解する必要があります。
- [HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>HiveServer2 をインストールする

このセクションでは、ターゲット ホストに追加の HiveServer2 をインストールします。

1. ご利用のブラウザーで Ambari を開き、ターゲット ホストをクリックします。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Ambari の [ホスト] メニュー。":::

2. [追加] ボタンをクリックし、[HiveServer2] をクリックします。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="ホストの HiveServer2 パネルを追加します。":::

3. 確定して、プロセスを実行します。 必要なすべてのホストに対して 1 から 3 を繰り返します。

4. インストールが完了したら、古い構成ですべてのサービスを再起動し、HiveServer2 を開始します。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="HiveServer2 パネルを起動します。":::

## <a name="next-steps"></a>次のステップ

この記事では、ご利用のクラスターに HiveServer2 をインストールする方法について説明しました。 エッジ ノードとアプリケーションの詳細については、次の記事を参照してください。

* [エッジノードをインストールする](hdinsight-apps-use-edge-node.md): ご利用の HDInsight クラスターにエッジ ノードをインストールする方法について説明します。
* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md):HDInsight アプリケーションをクラスターにインストールする方法について確認します。
* [Azure SQL DTU 接続の制限](../azure-sql/database/resource-limits-dtu-single-databases.md): DTU を使用した Azure SQL データベースの制限について説明します。
* [Azure SQL 仮想コア接続の制限](../azure-sql/database/resource-limits-vcore-elastic-pools.md): 仮想コアを使用した Azure SQL データベースの制限について説明します。
