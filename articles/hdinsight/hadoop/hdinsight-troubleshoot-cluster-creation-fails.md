---
title: Azure HDInsight のクラスター作成に失敗する問題のトラブルシューティング
description: Azure HDInsight のクラスター作成に関する問題をトラブルシューティングする方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/06/2019
ms.openlocfilehash: c7092b2cbcef01ef71261b6f5498cde56a40c358
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856534"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Azure HDInsight のクラスター作成に失敗する問題のトラブルシューティング

クラスターの作成に失敗する最も一般的な根本原因として、次の問題が挙げられます。

- アクセス許可の問題
- リソース ポリシーの制限
- ファイアウォール
- リソース ロック
- コンポーネントのバージョンがサポート対象外
- ストレージ アカウント名の制限
- サービスの停止

## <a name="permissions-issues"></a>アクセス許可の問題

Data Lake Storage Gen 2 を使用している場合は、HDInsight クラスターに割り当てられたユーザー割り当てマネージド ID が、 **ストレージ BLOB データ共同作成者**ロールまたは**ストレージ BLOB データ所有者ロール**に存在することを確認します。 詳細な設定手順については、「[Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account)」を参照してください。

Data Lake Storage Gen 1 を使用している場合は、[こちら](../hdinsight-hadoop-use-data-lake-store.md)で設定と構成の手順をご覧ください。 Data Lake Storage Gen 1 は、HBase クラスターではサポートされず、HDInsight バージョン 4.0 ではサポートされません。

Azure Storage を使用している場合、ストレージ アカウント名がクラスター作成中に有効であることを確認します。

## <a name="resource-policy-restrictions"></a>リソース ポリシーの制限

サブスクリプションベースの Azure ポリシーによって、パブリック IP アドレスの作成が拒否されている可能性があります。 HDInsight クラスターの作成には、2 つのパブリック IP が必要です。  

一般に、クラスターの作成に影響を及ぼす可能性があるのは、次のポリシーです。

* サブスクリプション内の IP アドレスおよびロード バランサーの作成を妨げるポリシー。
* ストレージ アカウントの作成を妨げるポリシー。
* ネットワーク リソース (IP アドレスやロード バランサー) の削除を妨げるポリシー。

## <a name="firewalls"></a>ファイアウォール

HDInsight の管理 IP アドレスとの通信が、仮想ネットワークまたはストレージ アカウントのファイアウォールによって拒否されている可能性があります。

次の表の IP アドレスを送信元とするトラフィックを許可してください。

| 送信元 IP アドレス | 宛先 | Direction |
|---|---|---|
| 168.61.49.99 | *:443 | 受信 |
| 23.99.5.239 | *:443 | 受信 |
| 168.61.48.131 | *:443 | 受信 |
| 138.91.141.162 | *:443 | 受信 |

さらに、クラスター作成先のリージョン固有の IP アドレスを追加します。 Azure リージョンごとのアドレス一覧については、「[HDInsight の管理 IP アドレス](../hdinsight-management-ip-addresses.md)」を参照してください。

Express Route または独自のカスタム DNS サーバーを使用している場合は、[「Azure HDInsight 用の仮想ネットワークを計画する」の「複数のネットワークの接続」](../hdinsight-plan-virtual-network-deployment.md#multinet)を参照してください。

## <a name="resources-locks"></a>リソース ロック  

[ご利用の仮想ネットワークとリソース グループがロック](../../azure-resource-manager/resource-group-lock-resources.md)されていないことを確認します。  

## <a name="unsupported-component-versions"></a>コンポーネントのバージョンがサポート対象外

[サポートされているバージョンの Azure HDInsight](../hdinsight-component-versioning.md) と [Apache Hadoop コンポーネント](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)をソリューションで使用していることを確認してください。  

## <a name="storage-account-name-restrictions"></a>ストレージ アカウント名の制限

ストレージ アカウント名に使用できる文字数は 24 文字までで、特殊文字は使用できません。 これらの制限は、ストレージ アカウントの既定のコンテナー名にも適用されます。

## <a name="service-outages"></a>サービスの停止

[Azure の状態](https://status.azure.com/status)をチェックし、潜在的な障害またはサービスの問題がないか調べてください。

## <a name="next-steps"></a>次の手順

* [Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Azure HDInsight クラスターで Azure Storage を使用する](../hdinsight-hadoop-use-blob-storage.md)
* [Apache Hadoop、Apache Spark、Apache Kafka などを使用して HDInsight でクラスターを設定する](../hdinsight-hadoop-provision-linux-clusters.md)
