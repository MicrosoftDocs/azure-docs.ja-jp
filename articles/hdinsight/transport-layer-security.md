---
title: Azure HDInsight でのトランスポート層セキュリティ
description: TLS (トランスポート層セキュリティ) と SSL (Secure Sockets Layer) は、コンピューター ネットワーク上の通信にセキュリティを確保する暗号プロトコルです。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 648f24e87e7d8a49c9c495a78e56afb8ae9e8aae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944705"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight でのトランスポート層セキュリティ

パブリック クラスター エンドポイント `https://CLUSTERNAME.azurehdinsight.net` 経由での HDInsight クラスターへの接続は、クラスター ゲートウェイ ノードを介してプロキシされます。 これらの接続は、TLS と呼ばれるプロトコルを使用してセキュリティ保護されます。 ゲートウェイでより高いバージョンの TLS を適用すると、これらの接続のセキュリティが向上します。 新しいバージョンの TLS を使用する必要がある理由について詳しくは、「[TLS 1.0 の問題の解決](/security/solving-tls1-problem)」をご覧ください。

既定では、Azure HDInsight クラスターのパブリック HTTPS エンドポイントで、TLS 1.2 接続と、下位互換性のために古いバージョンが受け入れられます。 クラスターの作成時に、Azure portal または Resource Manager テンプレートを使用して、ゲートウェイ ノードでサポートされる最小 TLS バージョンを制御できます。 ポータルでは、クラスターの作成時に **[セキュリティとネットワーク]** タブから TLS バージョンを選択します。 Resource Manager テンプレートでは、デプロイ時に **minSupportedTlsVersion** プロパティを使用します。 サンプル テンプレートについては、[HDInsight の最小 TLS 1.2 クイックスタートのテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)を参照してください。 このプロパティでは、次の 3 つの値がサポートされています: "1.0"、"1.1"、"1.2"。それぞれ、TLS 1.0+、TLS 1.1+、TLS 1.2+ に対応します。


## <a name="next-steps"></a>次のステップ

* [Azure HDInsight 用の仮想ネットワークを計画する](./hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight クラスターの仮想ネットワークの作成](hdinsight-create-virtual-network.md)。
* [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)。