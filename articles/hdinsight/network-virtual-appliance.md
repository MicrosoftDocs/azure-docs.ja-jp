---
title: Azure HDInsight でネットワーク仮想アプライアンスを構成する
description: Azure HDInsight でネットワーク仮想アプライアンスに関するいくつかの追加機能を構成する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943981"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Azure HDInsight でネットワーク仮想アプライアンスを構成する

> [!Important]
> 次の情報が必要なのは、[Azure Firewall](./hdinsight-restrict-outbound-traffic.md) 以外のネットワーク仮想アプライアンス (NVA) を構成する場合 **のみ** です。

Azure Firewall FQDN タグは、多くの一般的で重要な FQDN のトラフィックを許可するように、自動的に構成されます。 別のネットワーク仮想アプライアンスを使用するには、いくつかの追加機能を構成する必要があります。 ネットワーク仮想アプライアンスを構成する場合は、次の要因にご注意ください。

* サービス エンドポイント対応のサービスは、サービス エンドポイントを使用して構成できます。これにより、通常はコストやパフォーマンスに関する考慮事項のために NVA がバイパスされます。
* ResourceProviderConnection が "*アウトバウンド*" に設定されている場合は、メタストア用のストレージと SQL Server でプライベート エンドポイントを使用することができます。この場合、それらを NVA を追加する必要はありません。
* IP アドレスの依存関係が HTTP/S 以外のトラフィック (TCP トラフィックと UDP トラフィックの両方) に対応しています。
* FQDN HTTP/HTTPS エンドポイントは、NVA デバイスでの承認が可能です。
* 作成したルート テーブルを HDInsight サブネットに割り当てます。

## <a name="service-endpoint-capable-dependencies"></a>サービス エンドポイント対応の依存関係

必要に応じて、次の 1 つ以上のサービス エンドポイントを有効にすることができます。これにより、NVA がバイパスされます。 このオプションは、大量のデータ転送のコストを節約するだけでなく、パフォーマンスの最適化にも役立ちます。 

| **エンドポイント** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP アドレスの依存関係

| **エンドポイント** | **詳細** |
|---|---|
| [こちら](hdinsight-management-ip-addresses.md)で発行された IP | これらの IP は、HDInsight リソース プロバイダー用であり、非対称ルーティングを回避するために UDR に含める必要があります。 このルールは、ResourceProviderConnection が "*インバウンド*" に設定されている場合にのみ必要です。 ResourceProviderConnection が "*アウトバウンド*" に設定されている場合は、UDR でこれらの IP が必要となることはありません。  |
| AAD-DS プライベート IP | VNET がピアリングされていない場合は、ESP クラスターに対してのみ必要です。|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS の依存関係

NVA を構成するための FQDN の依存関係の一覧 (ほとんどは Azure Storage と Azure Service Bus) は、[このリポジトリで](https://github.com/Azure-Samples/hdinsight-fqdn-lists/)取得できます。 リージョンの一覧については、[こちら](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional)を参照してください。 これらの依存関係は、クラスターを正常に作成および監視/管理するために HDInsight リソース プロバイダー (RP) によって使用されます。 具体的には、テレメトリまたは診断ログ、プロビジョニング メタデータ、クラスター関連の構成、スクリプトなどがあります。FQDN の依存関係の一覧は、HDInsight の今後の更新プログラムのリリースで変更される可能性があります。

以下の一覧には、クラスターの作成 "*後*"およびクラスター操作の有効期間中に、OS とセキュリティの修正プログラムの適用または証明書の検証に必要となる可能性のある、いくつかの FQDN のみが示されています。

| **ランタイム依存関係の FQDN**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>次のステップ

* [ファイアウォールを使用したアウトバウンド トラフィックの制限](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 仮想ネットワーク アーキテクチャ](hdinsight-virtual-network-architecture.md)
