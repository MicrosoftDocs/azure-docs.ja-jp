---
title: Azure HDInsight のクラスター作成に失敗する問題のトラブルシューティング
description: Azure HDInsight の Apache クラスター作成に関する問題をトラブルシューティングする方法について説明します。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: f909f5714a049f63032c8a23ca1731777a40d332
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702863"
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

Azure Data Lake Storage Gen2 を使用しているときに、エラー `AmbariClusterCreationFailedErrorCode`: ":::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: (要求の処理中に内部サーバー エラーが発生しました。要求を再試行するか、サポートにお問い合わせください。)" が発生した場合は、Azure portal を開いてストレージ アカウントにアクセスし、Access Control (IAM) で、**ストレージ BLOB データ共同作成者** または **ストレージ BLOB データ所有者** ロールに、サブスクリプションの **ユーザー割り当てマネージド ID** へのアクセス権が確実に割り当てられているようにします。 詳しい手順については、「[Data Lake Storage Gen2 でマネージド ID のアクセス許可を設定する](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2)」を参照してください。

Azure Data Lake Storage Gen1 を使用している場合は、[Azure HDInsight クラスターで Azure Data Lake Storage Gen1 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen1.md)方法に関するページで設定と構成の手順を確認してください。 Data Lake Storage Gen1 は、HBase クラスターではサポートされず、HDInsight バージョン 4.0 ではサポートされません。

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

[サポートされているバージョンの Azure HDInsight と Apache Hadoop コンポーネント](../hdinsight-component-versioning.md)をソリューションで使用していることを確認してください。  

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
