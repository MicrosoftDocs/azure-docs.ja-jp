---
title: Azure アプリケーション オファーのソリューション テンプレートを計画する
description: Microsoft パートナー センターのコマーシャル マーケットプレース ポータルを使用して新しい Azure アプリケーション オファーのソリューション テンプレート プランを作成するために必要なことを説明します。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: aab856b1e1d2d991cc4964d061a990dbedbeddb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98876513"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Azure アプリケーション オファーのソリューション テンプレートを計画する

この記事では、Azure アプリケーション オファーのソリューション テンプレート プランを発行するための要件について説明します。 ソリューション テンプレート プランは、Azure アプリケーション オファーでサポートされる 2 種類のプランのうちの 1 つです。 これらの 2 種類のプランの違いについては、「[プランの種類](plan-azure-application-offer.md#plans)」を参照してください。 まだそうしていない場合は、[Azure アプリケーション オファーの計画](plan-azure-application-offer.md)に関するページを参照してください。

ソリューション テンプレート プランの種類には、ソリューション インフラストラクチャを自動的にデプロイするために [Azure Resource Manager テンプレート (ARM テンプレート)](../azure-resource-manager/templates/overview.md) が必要です。

## <a name="solution-template-requirements"></a>ソリューション テンプレートの要件

| 必要条件 | 詳細 |
| ------------ | ------------- |
| 請求/メータリング | ソリューション テンプレート プランは取引可能ではありませんが、Microsoft のコマーシャル マーケットプレースを通じて課金される有料 VM プランをデプロイするために利用できます。 ソリューションの Resource Manager テンプレートによってデプロイされるリソースは、顧客の Azure サブスクリプション内に設定されます。 従量課金制の仮想マシンが Microsoft 経由で顧客とトランザクション処理され、顧客の Azure サブスクリプションにより課金されます。 <br><br> ライセンス持ち込み (BYOL) 請求の場合、顧客サブスクリプションで発生するインフラストラクチャ コストについては Microsoft が請求しますが、ソフトウェア ライセンス料金についてはユーザーが顧客に対して直接処理します。 |
| Azure と互換性がある仮想ハード ディスク (VHD) | VM は、Windows または Linux 上に構築されている必要があります。 詳細については、次を参照してください。<ul><li>[Azure VM の技術資産を作成する](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (Windows VHD 用)</li><li>[Azure で動作保証済みの Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md) (Linux VHD 用)。</li></ul> |
| 顧客の利用状況属性 | Azure Marketplace に発行されるすべてのソリューション テンプレートで、顧客の利用状況属性を有効にする必要があります。 顧客の利用状況属性とそれを有効にする方法の詳細については、「[Azure パートナーの顧客の使用状況の属性](azure-partner-customer-usage-attribution.md)」をご覧ください。 |
| マネージド ディスクの使用 | [マネージド ディスク](../virtual-machines/managed-disks-overview.md)は、Azure のサービスとしてのインフラストラクチャ (IaaS) VM の永続化ディスクの既定オプションです。 ソリューション テンプレートではマネージド ディスクを使用する必要があります。<ul><li>ソリューション テンプレートを更新するには、「[Azure Resource Manager テンプレートでの管理ディスクの使用](../virtual-machines/using-managed-disks-template-deployments.md)」のガイダンスに従って、提供されている[サンプル](https://github.com/Azure/azure-quickstart-templates)を使用します。</li><li>VHD をイメージとして Azure Marketplace に発行するには、[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) または [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) のいずれかを使用して、マネージド ディスクの基になっている VHD をストレージ アカウントにインポートします</ul> |
| 展開パッケージ | お客様がプランをデプロイできるようにするデプロイ パッケージが必要になります。 同じ技術的構成が必要なプランを複数作成する場合は、同じプラン パッケージを使用できます。 詳細については、次のセクションの「デプロイ パッケージ」を参照してください。 |
|||

## <a name="deployment-package"></a>展開パッケージ

デプロイ パッケージには、このプランに必要なすべてのテンプレート ファイルに加え、すべての追加リソースが、.zip ファイルとしてパッケージ化されて含まれています。

すべての Azure アプリケーションで、.zip アーカイブのルート フォルダーに次の 2 つのファイルが含まれている必要があります。

- [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template) という名前の Resource Manager テンプレート ファイル。 このテンプレートでは、顧客の Azure サブスクリプションにデプロイするリソースが定義されます。 Resource Manager テンプレートの例については、[Azure クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/)または対応する [GitHub: Azure Resource Manager クイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates) リポジトリをご覧ください。
- [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md) という名前の、Azure アプリケーション作成エクスペリエンス用のユーザー インターフェイス定義。 ユーザー インターフェイスでは、コンシューマーがパラメーター値を入力できるようにする要素を指定します。

サポートされる最大ファイル サイズは次のとおりです。

- 圧縮された .zip アーカイブの合計サイズで最大 1 GB
- .zip アーカイブ内の個々の未圧縮ファイルすべてで最大 1 GB

すべての新しい Azure アプリケーション オファーには、[Azure パートナーの顧客の使用状況の属性 GUID](azure-partner-customer-usage-attribution.md) も含まれている必要があります。

## <a name="azure-regions"></a>Azure Azure リージョン

プランは、Azure パブリック リージョン、Azure Government リージョン、または両方に発行できます。 特定のエンドポイントが異なる可能性があるため、[Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md) に発行する前に、環境でプランをテストおよび検証します。 プランを設定してテストするには、[Microsoft Azure Government 試用版](https://azure.microsoft.com/global-infrastructure/government/request/)に試用版アカウントを申請します。

発行元は、コンプライアンス管理、セキュリティ対策、ベスト プラクティスについて責任を持ちます。 Azure Government では、物理的に離れた場所にあるデータ センターとネットワークが使用されます (場所は米国のみ)。

コマーシャル マーケットプレースでサポートされている国と地域の一覧については、[利用可能な地域と通貨サポート](marketplace-geo-availability-currencies.md)に関するページを参照してください。

Azure Government サービスでは、特定の政府の規制および要件の対象となるデータが処理されます。 FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS などです。 これらのプログラムの認定資格を認識させるため、認定資格について説明するリンクを 100 個まで提供することができます。 これらでは、プログラムでの一覧に直接リンクすることも、独自の Web サイトでのそれらについてのコンプライアンスに関する説明にリンクすることもできます。 これらのリンクは、Azure Government の顧客にのみ表示されます。

## <a name="choose-who-can-see-your-plan"></a>プランを表示できるユーザーを選択する

各プランは、すべてのユーザーに (パブリック)、または特定の対象ユーザーにのみ (プライベート) 表示されるように構成できます。 最大 100 個のプランを作成でき、そのうち最大 45 個をプライベートにできます。 特定の顧客に対してさまざまな価格オプションや技術的な構成を提供するために、プライベート プランを作成することをお勧めします。

プライベート プランには、Azure サブスクリプション ID を使用してアクセスを付与します。割り当てる各サブスクリプション ID の説明を含めるオプションがあります。 手動では最大 10 個のサブスクリプションを、CSV ファイルを使用すると最大 10,000 個のサブスクリプション ID を追加できます。 Azure サブスクリプション ID は GUID として表されます。文字は小文字にする必要があります。

> [!NOTE]
> プライベート プランを発行する場合は、後でその可視性をパブリックに変更できます。 ただし、パブリック プランを発行した後に、その可視性をプライベートに変更することはできません。

ソリューション テンプレート プランの場合は、Azure Marketplace でプランを非表示にすることもできます。 プランが、別のソリューション テンプレートまたはマネージド アプリケーションを通して間接的にのみデプロイされる場合は、これを行うことをお勧めします。

> [!NOTE]
> プライベート プランは、クラウド ソリューション プロバイダー プログラム (CSP) のリセラーを通じて確立された Azure サブスクリプションではサポートされていません。

詳細については、「[Microsoft 商業マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースで Azure アプリケーション オファーを作成する方法](create-new-azure-apps-offer.md)