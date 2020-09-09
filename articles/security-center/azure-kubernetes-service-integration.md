---
title: Azure Security Center と Azure Kubernetes Service
description: Azure Security Center と Azure Kubernetes Service の統合について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800181"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes Service と Security Center の統合

Azure Kubernetes Service (AKS) は、コンテナー化されたアプリケーションを開発、デプロイ、管理するための Microsoft のマネージド サービスです。 

Azure Security Center の Standard レベルを使用している場合は、AKS バンドル ([価格](security-center-pricing.md)に関するページを参照) を追加して、AKS ノード、クラウド トラフィック、セキュリティ制御をより詳細に把握することができます。

Security Center と AKS を組み合わせることで、最高レベルのクラウドネイティブな Kubernetes セキュリティ オファリングが形成されます。

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Security Center の Kubernetes 保護のコンポーネントは何ですか。

Kubernetes に対する Security Center の保護は、次の 2 つの要素の組み合わせによって提供されます。

- **仮想マシンに対する Azure Security Center の脅威保護** - Security Center が他の VM で使用しているものと同じ Log Analytics エージェントを使用して、Security Center は、AKS ノードで発生しているセキュリティの問題を示すことができます。 エージェントは、コンテナー固有の分析についても監視します。

- **Azure Security Center のオプションの Kubernetes バンドル** - Kubernetes バンドルは、AKS サービスを介して Kubernetes サブシステムからログと情報を受け取ります。 これらのログは、AKS サービスを介して Azure で既に利用できます。 Security Center の Kubernetes バンドルを有効にすると、Security Center にログへのアクセスが付与されます。 そのため、Security Center は、AKS マスター ノードによって既に収集されたデータを使用して、AKS クラスターにセキュリティ上の利点をもたらします。 Kubernetes 環境から Azure Security Center によってスキャンされたデータには、機密情報が含まれている場合があります。

    ![Azure Security Center と Azure Kubernetes Service (AKS) の概要](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>どのような保護が提供されますか。

2 つのサービスを組み合わせて使用すると、次の利点が得られます。

* **セキュリティに関する推奨事項** - Security Center では、クラスターから個々の仮想マシンまで、AKS リソースが識別され、分類されます。 これにより、セキュリティに関する推奨事項をリソースごとに表示できます。 詳細については、[推奨事項の参照一覧](recommendations-reference.md#recs-containers)にあるコンテナーの推奨事項を確認してください。 

* **環境の強化機能** - Security Center では、Kubernetes クラスターの構成および Docker 構成を常に監視します。 また、業界標準を反映したセキュリティの推奨事項を生成します。

* **実行時の保護** - Security Center では、次の AKS ソースの継続的な分析によって、ホスト "*および*" AKS クラスター レベルで検出された脅威や悪意のあるアクティビティについてアラートを通知します。 [コンテナーの脅威保護の詳細については、こちらを参照してください](threat-protection.md#azure-containers)。


     

![Azure Security Center と Azure Kubernetes Service (AKS) の詳細](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>Security Center と AKS のよく寄せられる質問

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Log Analytics エージェントを使用しなくても AKS 保護を利用できますか。

前述のように、オプションの Kubernetes バンドルはクラスター レベルでの保護を提供し、Azure Security Center Standard レベルの Log Analytics エージェントはノードを保護します。 

可能なかぎり最も完全な保護を実現するためには、両方をデプロイすることをお勧めします。

ホストにエージェントをインストールしないことを選択した場合、脅威保護の利点とセキュリティ アラートの一部だけを受け取ることになります。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。



## <a name="next-steps"></a>次のステップ

Security Center のコンテナーのセキュリティ機能の詳細については、以下をご覧ください。

* [Azure Security Center とコンテナーのセキュリティ](container-security.md)

* [Azure Container Registry との統合](azure-container-registry-integration.md)

* [Microsoft でのデータ管理](https://www.microsoft.com/trust-center/privacy/data-management) - Microsoft サービス (Azure、Intune、Microsoft 365 など) のデータ ポリシー、Microsoft のデータ管理の詳細、お客様のデータに影響する保持ポリシーについて説明します。
