---
title: Azure Application オファーの前提条件 | Microsoft Docs
description: Azure Marketplace で Azure アプリケーション オファーを発行するための前提条件。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: df127eec662f8598246f276ef9b1608ae3021512
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007164"
---
# <a name="azure-application-prerequisites"></a>Azure アプリケーションの前提条件

この記事では、Azure Marketplace でマネージド アプリケーション オファーを発行するための技術面/ビジネス面の前提条件について説明します。  まだ完了していない場合は、次の情報源を確認します。
- SKU の種類に応じて、次のいずれかを確認します: 「[Azure アプリケーション: ソリューション テンプレートのオファー発行ガイド](../../marketplace-solution-templates.md)」または「[Azure アプリケーション: マネージド アプリケーション オファーの発行ガイド](../../marketplace-managed-apps.md)」
- [Azure Marketplace 向けのソリューション テンプレートとマネージド アプリケーションの作成](https://channel9.msdn.com/Events/Build/2018/BRK3603)に関するビデオ


## <a name="technical-requirements"></a>技術的な要件

技術的な要件には次の項目があります。

*   Azure Resource Manager テンプレート。詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)」をご覧ください。 この記事では、Azure Resource Manager テンプレートの構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。 
* Azure クイックスタート テンプレート。<br> 詳細については、次を参照してください。

  * [Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)。 Azure Resource Manager を通じてコミュニティ提供のテンプレートで Azure リソースをデプロイし、生産性を高めます。 Azure リソース マネージャーでは、宣言型のテンプレートを使用してアプリケーションをプロビジョニングすることができます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。
  * [GitHub:Azure Resource Manager のクイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates)。 このリポジトリには、コミュニティから提供され、現在利用できる Azure Resource Manager テンプレートがすべて含まれています。 検索可能なテンプレート索引が https://azure.microsoft.com/en-us/documentation/templates/ にあります。
* UI 定義を作成する<br>
詳細については、「[マネージド アプリケーション用の Azure portal のユーザー インターフェイスを作成する](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)」を参照してください。 この記事では、createUiDefinition.json ファイルの主要な概念について説明します。 Azure ポータルは、このファイルを使用して、マネージド アプリケーションを作成するためのユーザー インターフェイスを生成します。


## <a name="business-requirements"></a>ビジネス要件

ビジネス要件には、次の手続き、契約、法律に関する義務が含まれます。

* 登録済みのクラウド マーケットプレース パブリッシャーである必要があります。 登録していない場合は、「[クラウド マーケットプレース パブリッシャーになる](../../become-publisher.md)」の手順に従ってください。

>[!NOTE]
>Microsoft デベロッパー センター登録アカウントと同じアカウントを使用して、Cloud パートナー ポータルにサインインする必要があります。 複数の Azure Marketplace サービスに対して 1 つの Microsoft アカウントを使用してください。 このアカウントは、個々のサービスまたはオファーに固有のものにしないでください。

* 会社 (またはその子会社) は、Azure Marketplace によってサポートされる販売元の国に所在している必要があります。 これらの国の最新の一覧については、「[Microsoft Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)」をご覧ください。
* Azure Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。 詳細については、「[Azure Marketplace の課金オプション](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations)」を参照してください。
* 商取引上合理的な方法で、顧客に技術サポートを提供していただきます。 このサポートは、無償、有償、コミュニティ アプローチのいずれでもかまいません。
* 会社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。
* Azure Marketplace および Azure portal にオファーが掲載されるための条件を満たすコンテンツを提供する必要があります。
* Microsoft Azure Marketplace 参加ポリシーと発行者契約の条項に同意する必要があります。
* Microsoft Azure Web サイト使用条件、Microsoft のプライバシーに関する声明、Microsoft Azure 認定プログラム契約に従う必要があります。


## <a name="publishing-requirements"></a>発行要件

新しい Azure アプリケーション オファーを発行するには、次の前提条件を満たす必要があります。

* メタデータの使用準備ができていること。 次の一覧 (一部) は、このメタデータの例を示すものです。
  * タイトル
  * (HTML 形式) での説明
  * PNG 形式で次の固定画像サイズのロゴ画像:40 x 40 ピクセル、90 x 90 ピクセル、115 x 115 ピクセル、255 x 115 ピクセル。
* "*利用規約*" と "*プライバシー ポリシー*" に関するドキュメント
* アプリケーションのドキュメント
* サポートの連絡先


## <a name="next-steps"></a>次の手順

すべての要件を満たしたら、[Azure アプリケーション オファーを作成](./cpp-create-offer.md)する準備ができます。 
 
