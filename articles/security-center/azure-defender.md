---
title: Azure Defender の概要と使用可能なプラン
description: Azure Defender のプラン、保護、アラートについて説明します。 その後、サブスクリプションで Azure Defender を有効にして高度なセキュリティを実現します。
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: aafd4c6695101042cb30a44e1d2bd30611256779
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096158"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender の概要

Azure Security Center の機能は、クラウド セキュリティの 2 つの大きな柱をカバーしています。

- **クラウド セキュリティ態勢管理 (CSPM)** - Security Center は、すべての Azure ユーザーが **無料** で利用できます。 無料のエクスペリエンスには、セキュリティ スコア、Azure マシンのセキュリティ構成の誤りの検出、資産インベントリなどの CSPM 機能が含まれます。 これらの CSPM 機能を使用して、ハイブリッド クラウドの態勢を強化し、組み込みポリシーへの準拠を追跡します。

- **クラウド ワークロード保護 (CWP)** - Security Center の統合クラウド ワークロード保護プラットフォーム (CWPP) である **Azure Defender** は、Azure とハイブリッド リソースおよびワークロードの高度でインテリジェントな保護を実現します。 Azure Defender を有効にすると、このページで説明するさまざまな追加のセキュリティ機能が提供されます。 組み込みポリシーに加え、Azure Defender プランを有効にすると、カスタム ポリシーおよびイニシアティブを追加できます。 NIST や Azure CIS などの規制基準と Azure セキュリティ ベンチマークを追加して、コンプライアンスの完全にカスタマイズされたビューを実現できます。

Security Center の Azure Defender ダッシュボードにより、環境の CWP 機能を可視化し、制御できます。

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender ダッシュボードの例" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender によって、どのような種類のリソースがセキュリティ保護されますか?

Azure Defender では、仮想マシン、SQL データベース、コンテナー、Web アプリケーション、ネットワークなどに対して、セキュリティ アラートと高度な脅威保護が提供されます。

Azure Security Center の **価格と設定** の領域から Azure Defender を有効にすると、次の Defender プランが同時にすべて有効になり、環境のコンピューティング、データ、およびサービス レイヤーに対する包括的な防御が提供されます。

- [Azure Defender for servers](defender-for-servers-introduction.md)
- [Azure Defender for App Service](defender-for-app-service-introduction.md)
- [Azure Defender for Storage](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender for container registries](defender-for-container-registries-introduction.md)
- [Azure Defender for Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md)
- [Azure Defender for DNS](defender-for-dns-introduction.md)

これらのプランについてはそれぞれ、Security Center のドキュメントで個別に説明されています。

> [!TIP]
> Azure Defender for IoT (プレビュー) は、個別の製品です。 詳細については、「[Azure Defender for IoT (プレビュー) の概要](../defender-for-iot/overview.md)」を参照してください。 

## <a name="hybrid-cloud-protection"></a>ハイブリッド クラウド保護

Azure 環境の防護に加えて、次のような Azure Defender 機能をハイブリッド クラウド環境に追加することができます。

- 非 Azure サーバーを保護する
- 他のクラウド (AWS、GCP など) 内の仮想マシンを保護する

特定の環境に応じてカスタマイズされた脅威インテリジェンスと優先順位が付けられたアラートを取得して、最も重要な問題に専念できるようにします。

他のクラウドまたはオンプレミスの仮想マシンと SQL データベースに保護を拡張するには、[Azure Arc](https://azure.microsoft.com/services/azure-arc/) をデプロイし、Azure Defender を有効にします。 Azure Arc for servers は無料のサービスですが、Arc 対応サーバー (Azure Defender など) で使用されるサービスは、そのサービスの価格に従って課金されます。 詳細については、「[Azure Arc を使用して Azure 以外のマシンを追加する](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)」をご覧ください。

> [!TIP]
> AWS のネイティブ コネクタにより、Azure Arc デプロイが透過的に処理されます。 詳細については、「[Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)」をご覧ください。



## <a name="azure-defender-security-alerts"></a>Azure Defender のセキュリティ アラート 

Azure Defender によって環境のいずれかの領域で脅威が検出されると、セキュリティ アラートが生成されます。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。

アラートは、Security Center によって生成されたか、統合されているセキュリティ製品から Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、「[SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)」の手順に従ってください。

> [!NOTE]
> アラートの出力元によって、表示されるまでの時間が変わる場合があります。 たとえば、ネットワーク トラフィックの分析を必要とするアラートは、仮想マシンで実行されている疑わしいプロセスに関連するアラートよりも、表示されるまでより長い時間がかかる可能性があります。


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender の高度な保護機能

Azure Defender では、リソースに関連する、調整された推奨事項を得るために、高度な分析を使用します。 

保護には、ジャスト インタイム アクセスと適応型アプリケーション制御を使用した、VM の管理ポートのセキュリティ保護が含まれます。これにより、コンピューター上で実行すべきアプリや実行する必要がないアプリを示す許可リストが作成されます。 

Azure Defender ダッシュボードの [高度な保護] タイルを使用して、これらの保護をそれぞれ監視し、構成します。 

## <a name="vulnerability-assessment-and-management"></a>脆弱性の評価と管理

Azure Defender には、仮想マシンとコンテナー レジストリの脆弱性スキャンが追加費用なしで含まれています。 スキャナーは Qualys を使用していますが、Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Security Center 内でシームレスに処理されます。 

これらの脆弱性スキャナーの結果を確認し、すべての結果に対して Security Center 内から対応します。 これにより、Security Center では、クラウド セキュリティのすべての取り組みを、より一元的に確認できます。

詳細については、次のページを参照してください。

- [Azure 仮想マシンに対する Security Center の統合脆弱性評価](deploy-vulnerability-assessment-vm.md)
- [Azure コンテナー レジストリでイメージの脆弱性を識別する](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>次の手順

この記事では、Azure Defender の利点について説明しました。 

> [!div class="nextstepaction"]
> [Azure Defender を有効にする](enable-azure-defender.md)