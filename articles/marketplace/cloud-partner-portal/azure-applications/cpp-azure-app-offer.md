---
title: Azure アプリケーション オファー | Microsoft Docs
description: Azure Marketplace で Azure アプリケーション オファーを発行するプロセスの概要です。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 9faa38a23b2039902366e5b885ab73c68a2a3d80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076022"
---
# <a name="azure-application-offer"></a>Azure アプリケーション プラン

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> このセクションでは、新しい Azure アプリケーション オファーを [Azure Marketplace](https://azuremarketplace.microsoft.com) に発行する方法について説明します。  各 Azure アプリケーションには、アプリケーションで使用されるすべての技術資産が定義されている Azure Resource Manager テンプレートが 1 つ含まれています。このテンプレートには通常、1 つ以上の仮想マシンとその他のサポートする Azure ベースまたは Web ベースのサービスが含まれています。 すべての Azure アプリ オファーでは、[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) を使用してアクセスのセキュリティを有効にする必要があります。  </div> | ![Azure アプリ アイコン](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>公開の概要

次のビデオ「[Building Solution Templates, and Managed Applications for the Azure Marketplace (Azure Marketplace 向けのソリューション テンプレートとマネージド アプリケーションの作成)](https://channel9.msdn.com/Events/Build/2018/BRK3603)」では、使用可能なオファーの種類、必要な技術資産、Azure Resource Manager テンプレートの作成方法、アプリ UI の開発とテスト、アプリ オファーの発行方法、アプリのレビュー プロセスなどの概要が説明されています。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Azure アプリケーションの種類

Azure アプリケーションには、マネージド アプリケーションとソリューション テンプレートの 2 種類があります。 

- ソリューション テンプレートは、Marketplace でソリューションを発行するための 1 つの手段です。 このオファーの種類は、単一の仮想マシン (VM) の範囲を超えてデプロイと構成のオートメーションを追加する必要があるソリューションで使用します。 ソリューション テンプレートを使用して複数の VM の提供を自動化することができます。 この自動化には、複雑な IaaS ソリューションを提供するためのネットワーク リソースとストレージ リソースのプロビジョニングが含まれます。 ソリューション テンプレートの要件と課金モデルの概要については、[Azure アプリケーションのソリューション テンプレート](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)に関するページをご覧ください。

- マネージド アプリケーションはソリューション テンプレートと似ていますが、大きな違いが 1 つあります。 マネージド アプリケーションでは、アプリの発行元によって管理されているリソース グループが、リソースのデプロイ先となります。 リソース グループはユーザーのサブスクリプション内に存在しますが、そのリソース グループにアクセスできるのは、発行元のテナントの ID です。 発行元は、ソリューションの継続的なサポートにかかるコストを指定します。 フル マネージドのターンキー アプリケーションを簡単に構築して顧客に提供するには、Azure マネージド アプリケーションを使用します。

Azure Marketplace だけでなく、サービス カタログでマネージド アプリケーションを提供することもできます。 サービス カタログは、組織内のユーザー向けの承認済みのソリューションの内部カタログです。 組織の基準を満たしながら、組織内のグループ用のソリューションを提供するには、カタログを使用します。 従業員は、カタログを使用して、IT 部門が推奨し、承認しているアプリケーションを簡単に見つけることができます。

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](../../cloud-solution-providers.md)」を参照してください。

マネージド アプリケーションの利点と種類について詳しくは、「[Azure マネージド アプリケーションの概要](https://docs.microsoft.com/azure/managed-applications/overview)」をご覧ください。


## <a name="publishing-process-workflow"></a>発行プロセスのワークフロー

次の図は、Azure アプリケーション オファーを発行するプロセスの概要を示したものです。

![オファー発行のワークフロー](./media/new-offer-process.png)

Azure アプリケーション オファーを発行する手順の概要は次のとおりです。

1. [前提条件](./cpp-prerequisites.md)を満たす - (示されていません) Azure Marketplace に Azure アプリを発行するためのビジネス要件と技術的要件を満たしていることを確認します。 

1. [オファーを作成する](./cpp-create-offer.md) - オファーに関する詳細情報を指定します。 この情報には、オファーの説明、マーケティング資料、サポート情報、資産の仕様などが含まれます。

1. [ビジネス資産と技術資産を作成するか、既存のものを収集する](./cpp-create-technical-assets.md) - 関連付けられたソリューションに対するビジネス資産 (法務文書とマーケティング資料) と技術資産を作成します。

1. [SKU を作成する](./cpp-skus-tab.md) - オファーに関連付けられた SKU を作成します。 発行を計画しているイメージごとに固有の SKU が必要です。

1. [オファーを認定して発行する](./cpp-publish-offer.md) - オファーと技術資産が完成したら、オファーを送信できます。 この送信によって発行プロセスが開始します。 このプロセスの間に、ソリューションがテストされ、検証され、認定されて、Azure Marketplace での "運用が開始" されます。

## <a name="next-steps"></a>次の手順

これらの手順を検討する前に、Microsoft Azure Marketplace へのマネージド アプリケーションの発行に関する[技術要件とビジネス要件](./cpp-prerequisites.md)を満たしている必要があります。
