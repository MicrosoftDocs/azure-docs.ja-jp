---
title: Dynamics 365 for Customer Engagement オファーの前提条件 | Azure Marketplace
description: Azure Marketplace で Azure アプリケーション オファーを発行するための前提条件。
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 9cfec65843012e781c8a8dd06cbc4d1225b0dd36
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338907"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 for Customer Engagement の前提条件

この記事では、AppSource Marketplace で Dynamics 365 for Customer Engagement アプリケーション オファーを公開するための技術面およびビジネス面の前提条件について説明します。  まだお読みになっていない場合は、「[Office 365、Dynamics 365、PowerApps、Power BI 用のオファー発行ガイド](../../appsource-offer-publishing-guide.md)」をご確認ください。


## <a name="technical-requirements"></a>技術的な要件

Dynamics 365 for Customer Engagement アプリケーションでは「[Microsoft AppSource app review guidelines (Microsoft AppSource アプリ レビュー ガイドライン)](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf)」に準拠する必要があり、次の要件が含まれます。


|              要件             |        説明           |
|            ---------------           |      ---------------         |
| Azure Active Directory の統合   | アプリでは、同意が有効な Azure Active Directory フェデレーション シングル サインオン (AAD フェデレーション SSO) を許可する必要があります。 詳しくは、「[Azure Active Directory の AppSource 認定を取得する方法](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified)」をご覧ください。 |
| Microsoft Cloud サービスとの統合 (オプション) | この機能が必要な場合は、アプリと、Microsoft Power BI、Microsoft Flow、Machine Learning や Cognitive Services といった Microsoft Azure サービスなどの他の Microsoft Cloud サービスを、統合する必要があります。 |
| 基幹業務アプリ対応            |  アプリでは、明確に定義されたビジネス プロセスまたは問題に対応し (主にビジネスの顧客がターゲット)、ユーザーが自分の職場の資格情報 (ユーザー名とパスワード) でサインインできるようにする必要があります。  |
| 無料試用期間および試用エクスペリエンス |  顧客がアプリを限られた期間無料で使用できる必要があります。"今すぐ入手" は無料アプリ向け、"無料試用版" は期間指定の場合、"体験版" はデモンストレーション用、"連絡する" は要求オプションです。  |
| 構成がない/少ない                 | アプリは、簡単に短時間で構成およびセットアップできる必要があります (開発またはカスタマイズが不要)。  |
| カスタマー サポート                     | 顧客がヘルプを発見できるサポート リンクに、アプリのサポートが含まれる必要があります。  |
| 可用性/アップタイム                  | アプリには、少なくとも 99.9% のアップタイムが必要です。 |
|  |  |


## <a name="business-requirements"></a>ビジネス要件

ビジネス要件には、次の手続き、契約、法律に関する義務が含まれます。

* パートナーは、[Microsoft Partner Network (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) に登録するか、登録済みのクラウド マーケットプレース パブリッシャーである必要があります。 登録していない場合は、「[クラウド マーケットプレース パブリッシャーになる](https://docs.microsoft.com/azure/marketplace/become-publisher)」の手順に従ってください。  ([Dynamics 365 for Customer Engagement のオンボード チーム](https://experience.dynamics.com/isvengage/)に連絡を取ることもできます。)

    >[!NOTE]
    >Microsoft デベロッパー センター登録アカウントと同じアカウントを使用して、Cloud パートナー ポータルにサインインする必要があります。 複数の Azure Marketplace サービスに対して 1 つの Microsoft アカウントを使用してください。 このアカウントは、個々のサービスまたはオファーに固有のものにしないでください。

* AppSource では商取引が有効な公開オプションは提供されていないので、追加の投資や変更なしで、現在の注文および課金インフラストラクチャを使用する必要があります。
* 商取引上合理的な方法で、顧客に技術サポートを提供していただきます。 このサポートは、無償、有償、コミュニティ アプローチのいずれでもかまいません。
* 会社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。
* 公式のアプリ名、説明 (HTML 形式)、PNG 形式のロゴ画像 (40 x 40、90 x 90、115 x 115、255 x 115 ピクセル)、使用条件とプライバシー ポリシーなど、関連するマーケティング資料を作成しておく必要があります。  


## <a name="next-steps"></a>次の手順

これらの要件を満たした後は、[Dynamics 365 for Customer Engagement オファーを作成する](./cpp-create-offer.md)ことができます 
