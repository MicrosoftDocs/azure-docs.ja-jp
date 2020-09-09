---
title: Azure HDInsight のクラスター作成に失敗する問題のトラブルシューティング
description: Azure HDInsight の Apache クラスター作成に関する問題をトラブルシューティングする方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: 14b449590f6ffc5e735faa26baadfcc4e526450c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996405"
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

Azure Data Lake Storage Gen2 を使用しているときに、エラー `AmbariClusterCreationFailedErrorCode`: ":::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: (要求の処理中に内部サーバー エラーが発生しました。要求を再試行するか、サポートにお問い合わせください。)" が発生した場合は、Azure portal を開いてストレージ アカウントにアクセスし、Access Control (IAM) で、**ストレージ BLOB データ共同作成者**または**ストレージ BLOB データ所有者**ロールに、サブスクリプションの**ユーザー割り当てマネージド ID** へのアクセス権が確実に割り当てられているようにします。 詳しい手順については、「[Data Lake Storage Gen2 アカウントにマネージド ID のアクセス許可を設定する](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account)」を参照してください。

Azure Data Lake Storage Gen1 を使用している場合は、[こちら](../hdinsight-hadoop-use-data-lake-store.md)で設定と構成の手順をご覧ください。 Data Lake Storage Gen1 は、HBase クラスターではサポートされず、HDInsight バージョン 4.0 ではサポートされません。

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

[ご利用の仮想ネットワークとリソース グループがロック](../../azure-resource-manager/management/lock-resources.md)されていないことを確認します。 リソース グループがロックされている場合、クラスターを作成または削除することはできません。 

## <a name="unsupported-component-versions"></a>コンポーネントのバージョンがサポート対象外

[サポートされているバージョンの Azure HDInsight](../hdinsight-component-versioning.md) と [Apache Hadoop コンポーネント](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions)をソリューションで使用していることを確認してください。  

## <a name="storage-account-name-restrictions"></a>ストレージ アカウント名の制限

ストレージ アカウント名に使用できる文字数は 24 文字までで、特殊文字は使用できません。 これらの制限は、ストレージ アカウントの既定のコンテナー名にも適用されます。

クラスター作成の場合、その他の命名規則制限も適用されます。 詳細は、[クラスターの命名規則制限](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)に関するページを参照してください。

## <a name="service-outages"></a>サービスの停止

[Azure の状態](https://status.azure.com)をチェックし、潜在的な障害またはサービスの問題がないか調べてください。

## <a name="next-steps"></a>次のステップ

* [Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Azure HDInsight クラスターで Azure Storage を使用する](../hdinsight-hadoop-use-blob-storage.md)
* [Apache Hadoop、Apache Spark、Apache Kafka などを使用して HDInsight でクラスターを設定する](../hdinsight-hadoop-provision-linux-clusters.md)
