---
title: Azure Security Center のコンテナーのセキュリティ | Microsoft Docs
description: Azure Security Center のコンテナーのセキュリティ機能について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: 3b5204f1d390388c2dc9a10ac2ca0234f6b0499b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101343"
---
# <a name="container-security-in-security-center"></a>Security Center のコンテナーのセキュリティ

Azure Security Center は、コンテナーをセキュリティ保護するための Azure ネイティブ ソリューションです。

Security Center では、次のコンテナー リソースの種類を保護できます。

| リソースの種類 | Security Center によって提供される保護 |
|:--------------------:|-----------|
| ![Kubernetes サービス](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Azure Kubernetes Service (AKS) クラスター** | - AKS クラスター構成の継続的な評価。構成ミスを可視化し、検出された問題を解決するためのガイドラインを提供します。<br>[詳細については、セキュリティ推奨事項による環境のセキュリティ強化に関する情報を参照してください](#environment-hardening)。<br><br>- AKS クラスターおよび Linux ノードの脅威の防止。 不審なアクティビティのアラートは、オプションの [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) によって提供されます。<br>[AKS ノードとクラスターの実行時の保護に関する詳細情報](#run-time-protection-for-aks-nodes-and-clusters)。|
| ![コンテナー ホスト](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**コンテナー ホスト**<br>(Docker を実行している VM) | - お使いの Docker 構成の継続的な評価。構成ミスを可視化し、オプションの[Azure Defender for servers](defender-for-servers-introduction.md) を使用して、検出された問題を解決するためのガイドラインを提供します。<br>[詳細については、セキュリティ推奨事項による環境のセキュリティ強化に関する情報を参照してください](#environment-hardening)。|
| ![コンテナー レジストリ](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry (ACR) レジストリ** | - オプションの[Azure Defender for container registries](defender-for-container-registries-introduction.md) を使用した Azure Resource Manager ベースの ACR レジストリ内のイメージを対象とした脆弱性評価および管理ツール。<br>[詳細については、コンテナー イメージの脆弱性スキャンに関するページを参照してください](#vulnerability-management---scanning-container-images)。 |
|||

この記事では、コンテナー レジストリ、サーバー、および Kubernetes 用のオプションの Azure Defender プランと共に Security Center を使用して、コンテナーとそのアプリのセキュリティを改善、監視、および維持する方法について説明します。

ここでは、コンテナーのセキュリティのこうした主要な側面について、Security Center がどのように役立つかを説明します。

- [脆弱性管理 - コンテナー イメージのスキャン](#vulnerability-management---scanning-container-images)
- [環境のセキュリティ強化](#environment-hardening)
- [AKS ノードとクラスターの実行時の保護](#run-time-protection-for-aks-nodes-and-clusters)

次のスクリーンショットは、資産インベントリ ページと、Security Center によって保護されるさまざまなコンテナー リソースの種類を示しています。

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Security Center の資産インベントリ ページのコンテナー関連リソース" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>脆弱性管理 - コンテナー イメージのスキャン

Azure Resource Manager ベースの Azure コンテナー レジストリ内のイメージを監視するには、[Azure Defender for container registries](defender-for-container-registries-introduction.md) を有効にします。 過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージが、Security Center によってスキャンされます。 統合されたスキャナーは、業界トップレベルの脆弱性スキャン ベンダー Qualys によって提供されます。

Qualys または Security Center によって問題が見つかった場合は、[Azure Defender ダッシュボード](azure-defender-dashboard.md)で通知されます。 Security Center には、すべての脆弱性について、実行可能な推奨事項、重大度の分類、および問題の修正方法に関するガイダンスが表示されます。 コンテナーの Security Center の推奨事項の詳細については、[推奨事項の参照リスト](recommendations-reference.md#recs-compute)を参照してください。

Security Center では、スキャナーによる検出結果がフィルター処理および分類されます。 イメージが正常な場合、Security Center ではそのように示されます。 Security Center では、解決の必要な問題があるイメージに対してのみ、セキュリティに関する推奨事項が生成されます。 問題があるときにだけ通知することにより、Security Center では不要な情報アラートの可能性が減ります。

## <a name="environment-hardening"></a>環境のセキュリティ強化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 構成の継続的な監視

Azure Security Center では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Security Center によって、こうしたコンテナーの構成が継続的に評価されます。 その後、[Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/) と比較されます。

Security Center には CIS Docker Benchmark のルールセット全体が含まれており、コンテナーがいずれかのコントロールを満たしていない場合は警告が表示されます。 不適切な構成が検出されると、Security Center によってセキュリティの推奨事項が生成されます。 Security Center の **推奨事項ページ** を使用して、推奨事項を表示したり、問題を修復したりします。 CIS ベンチマーク チェックは、AKS マネージド インスタンスまたは Databricks マネージド VM では実行されません。

この機能に表示される可能性がある、Security Center の関連する推奨事項の詳細については、推奨事項参照テーブルの[コンピューティング セクション](recommendations-reference.md#recs-compute)をご覧ください。

VM のセキュリティ問題を調査している場合、Security Center にはマシン上のコンテナーに関する追加情報が表示されます。 こうした情報には、Docker のバージョンとホストで実行されているイメージの数が含まれます。 

IaaS Linux VM でホストされているアンマネージド コンテナーを監視するには、オプションの[Azure Defender for servers](defender-for-servers-introduction.md) を有効にします。


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes クラスターの継続的な監視
Security Center は Azure Kubernetes Service (AKS) と連携して機能します。これは、コンテナー化されたアプリケーションを開発、デプロイ、および管理するための Microsoft のマネージド コンテナー オーケストレーション サービスです。

AKS には、クラスターのセキュリティ体制をセキュリティで制御し、把握する機能があります。 Security Center では、これらの機能を使用して、AKS クラスターの構成を常に監視し、業界標準に沿ったセキュリティの推奨事項を生成します。

Azure Security Center、Azure Kubernetes Service、Azure Policy 間の相互作用の概要図を次に示します。

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure Security Center、Azure Kubernetes Service、Azure Policy 間の相互作用のアーキテクチャ概要" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Security Center が受信して分析する項目には、以下が含まれていることがわかります。

- API サーバーからの監査ログ
- Log Analytics エージェントからの生のセキュリティ イベント

    > [!NOTE]
    > 仮想マシン スケール セットで実行されている Azure Kubernetes Service クラスターへの Log Analytics エージェントのインストールは、現在サポートされていません。

- AKS クラスターからのクラスター構成情報
- Azure Policy からのワークロード構成 (**Kubernetes 用の Azure Policy アドオン** を使用)

この機能に表示される可能性がある、Security Center の関連する推奨事項の詳細については、推奨事項参照テーブルの[コンピューティング セクション](recommendations-reference.md#recs-compute)をご覧ください。


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Kubernetes 受付制御を使用したワークロード保護のベストプラクティス

Kubernetes コンテナーのワークロードを保護する推奨事項のバンドルを取得するには、**Kubernetes 用 Azure Policy アドオン** をインストールします。 「[Log Analytics エージェントと拡張機能の自動プロビジョニングの有効化](security-center-enable-data-collection.md#auto-provision-mma)」の説明に従って、このアドオンを自動でデプロイすることもできます。 アドオンの自動プロビジョニングが "オン" に設定されている場合は、既存のクラスターおよび今後作成されるクラスターすべて (アドオンのインストール要件を満たすもの) で、拡張機能が既定で有効になります。

[こちらの Kubernetes 用 Azure Policy ページ](../governance/policy/concepts/policy-for-kubernetes.md)で説明するように、アドオンによって、 [Open Policy Agent](https://www.openpolicyagent.org/) 用オープンソース [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)  アドミッション コントローラー Webhook が拡張されます。 Kubernetes アドミッション コントローラーは、クラスターの使用方法を規定するプラグインです。 アドオンは Webhook として Kubernetes 受付制御に登録され、大規模な実施内容と安全対策を、一元的で一貫性のある方法でお使いのクラスターに適用できるようにします。 

AKS クラスターにアドオンが存在する場合、Kubernetes API サーバーに対するすべての要求が、クラスターに保存される前に、事前定義された一連のベスト プラクティスを基準にして監視されます。 その後、ベスト プラクティスを **適用** し、それを将来のワークロードに対して要求するように構成できます。 

たとえば、特権コンテナーが作成されないように要求することができます。また、今後の特権コンテナー作成要求はすべてブロックされます。

詳細については、「[Kubernetes ワークロードを保護する](kubernetes-workload-protections.md)」を参照してください。


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>AKS ノードとクラスターの実行時の保護

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>次のステップ

この概要では、Azure Security Center でのコンテナー セキュリティの中核となる要素について説明しました。 関連資料については、以下を参照してください。

- [Azure Defender for Kubernetes の概要](defender-for-kubernetes-introduction.md)
- [Azure Defender for container registries の概要](defender-for-container-registries-introduction.md)