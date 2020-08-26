---
title: Microsoft のコマーシャル マーケットプレースのオファーを Azure Active Directory に統合する
description: Azure Active Directory を使用して、Microsoft AppSource と Azure Marketplace オファーを認証します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: a6e304e5ffeab8f0a44cbdfe1566465f2b9bf34a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607418"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>コマーシャル マーケットプレースの一覧を Azure Active Directory に統合する

 この記事では、Azure Active Directory (Azure AD) を使用してコマーシャル マーケットプレースの一覧またはオファーを統合する場合の要件について説明します。 Azure AD はクラウド ID サービスであり、業界標準のフレームワークを使用して、Microsoft アカウントでの認証を有効にします。 [Azure Active Directory の詳細を確認します](https://azure.microsoft.com/services/active-directory)。

## <a name="azure-ad-benefits"></a>Azure AD の利点

Microsoft AppSource と Azure Marketplace のお客様は、製品内エクスペリエンスを利用して、オンライン ストアの一覧カタログを検索します。 これらのアクションでは、お客様が製品にサインインする必要があります。 Azure AD 統合には次のような利点があります。

- より迅速なエンゲージメントと最適化されたカスタマー エクスペリエンス
- 数百万単位のエンタープライズ ユーザーのシングル サインオン (SSO)
- さまざまなパートナーが発行しているアプリケーションで一貫性のあるサインイン体験
- モバイル アプリとクラウド アプリのための拡張可能なクロスプラットフォーム認証

## <a name="offers-that-require-azure-ad"></a>Azure AD を必要とするオファー

コマーシャル マーケットプレースのさまざまな[オプションとオファーの種類の一覧](determine-your-listing-type.md)には、Azure AD の実装に対してそれぞれ異なる要件があります。 詳細については、次の表を参照してください。

| プランの種類    | お問い合わせに Azure AD SSO は必要か?  | 試用版に Azure AD SSO は必要か? | 体験版に Azure AD SSO は必要か?  | 取引に Azure AD SSO は必要か? |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| 仮想マシン | 該当なし | いいえ | いいえ | いいえ |
| Azure アプリ (ソリューション テンプレート)  | 該当なし | 該当なし | 該当なし | 該当なし |
| マネージド アプリ  | 該当なし | 該当なし | 該当なし | いいえ |
| SaaS  | いいえ | はい | はい | はい |
| Containers  | 該当なし | 該当なし | 該当なし | いいえ |
| コンサルティング サービス  | いいえ | 該当なし | 該当なし | 該当なし |

SaaS 技術要件の詳細については、「[コマーシャル マーケットプレースにおける Azure AD と取引可能な SaaS オファー](./azure-ad-saas.md)」を参照してください。

## <a name="azure-ad-integration"></a>Azure AD の統合

- 取引可能な SaaS (サービスとしてのソフトウェア) オファーのために Azure AD を統合する方法の詳細については、「[コマーシャル マーケットプレースにおける Azure AD と取引可能な SaaS オファー](./azure-ad-saas.md)」を参照してください。
- 一覧への Azure AD の統合によってシングル サインオンを有効にする方法については、[開発者向けの Azure Active Directory](../active-directory/develop/index.yml) に関する記事をご覧ください。
- Azure AD シングル サインオンについて詳しくは、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../active-directory/manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="enable-a-trial-listing"></a>試用版の一覧を有効にする

自動化された顧客のセットアップにより、コンバージョン率を高めることができます。 顧客が試用版の一覧を選択し、試用版の環境にリダイレクトされた場合、追加のサインイン手順を必要とせずに、顧客を直接設定できます。

認証時に、Azure AD からアプリまたはオファーにトークンが送信されます。 トークンによって提供されるユーザー情報により、アプリまたはオファー内にユーザー アカウントを作成できます。 詳しくは、[サンプル トークン](../active-directory/develop/id-tokens.md)に関する記事をご覧ください。

Azure AD を使って、アプリまたは試用版一覧でワンクリック認証を有効にした場合、次のことが行われます。

- コマーシャル マーケットプレースから試用版一覧へのカスタマー エクスペリエンスを簡素化する。
- ユーザーがコマーシャル マーケットプレースから公開元のドメインまたは試用環境にリダイレクトされる場合でも、製品内エクスペリエンスの感覚を維持する。
- 余分なサインイン手順がないため、ユーザーがリダイレクトされるときに中止される可能性を低くする。
- 大規模な Azure AD ユーザーの展開障壁を削減する。

## <a name="verify-azure-ad-integration"></a>Azure AD の統合の確認

### <a name="multitenant-solutions"></a>マルチテナント ソリューション

Azure AD を使用して、次のアクションをサポートします。

- コマーシャル マーケットプレースのオンライン ストアの 1 つにアプリを登録する。 詳しくは、[アプリの登録](../active-directory/develop/quickstart-register-app.md)または [AppSource 認定](../active-directory/azuread-dev/howto-get-appsource-certified.md)に関する記事をご覧ください。
- Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを取得する。

Azure AD フェデレーション シングル サインオンを使用した経験がない場合は、次の手順に従います。

1. コマーシャル マーケットプレースにお使いのアプリを登録します。
1. [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) または [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md) を使って、Azure AD で SSO を開発します。
1. Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを提供します。

### <a name="single-tenant-solutions"></a>シングル テナント ソリューション

Azure AD を使用して、次のアクションの 1 つをサポートします。

- [Azure AD B2B](../active-directory/b2b/what-is-b2b.md) を使って、ディレクトリにゲスト ユーザーを追加する。
- **[お問い合わせ]** の公開オプションを使用して、顧客用に試用版を手動で設定する。
- 顧客ごとの "体験版" を開発する。
- SSO を使用するマルチテナント サンプル デモ アプリを構築する。

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、 

- コマーシャル マーケットプレースについて[学習](https://azuremarketplace.microsoft.com/sell)します。

パートナー センターに登録するには、新しいプランの作成を開始するか、既存のものを操作します。

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)して、プランを作成するか完成させます。
