---
title: SaaS アプリケーション公開ガイド - Microsoft 商業マーケットプレース
description: Microsoft AppSource および Azure Marketplace に SaaS アプリケーション オファーを公開するための要件とリソース。
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9f488675e4f2437982e2426da73b4db1a713905d
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607299"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS アプリケーションのオファー発行ガイド

SaaS アプリケーションは、3 つの行動喚起"お問い合わせ"、"今すぐ試す"、"今すぐ入手する" を使用して、マーケットプレースに公開できます。 この記事では、この 3 つのオプションとそれぞれの要件について説明します。 

## <a name="offer-overview"></a>オファーの概要  

SaaS アプリケーションは Microsoft AppSource と Azure Marketplace でご利用いただけます。  両方のオンライン ストアで、リスト、試用版、トランザクションのオファーがサポートされています。

**リスト:** リストの発行オプションは、お問い合わせのオファー タイプから構成され、試用版レベルまたは取引レベルの参加を実現できない場合に使用されます。 この方法の利点は、市場にソリューションのあるパブリッシャーはすぐに取引に転換し、ビジネスを向上できるリードの受け取りを始められることです。  
**試用版/取引:** 顧客はソリューションを直接購入するか、試用版を要求することができます。 試用版エクスペリエンスを提供することで、顧客に提供されるエンゲージメント レベルが高くなり、顧客は購入前にソリューションを調べることができます。 試用版エクスペリエンスでは、オンライン ストアでのプロモーションの機会が多くなり、顧客エンゲージメントからより多くの有望なリードを期待できます。 試用は、少なくとも試用期間中は無料サポートを含む必要があります。  

| SaaS アプリのオファー | ビジネス要件 | 技術的な要件 |  
| --- | --- | --- |  
| **お問い合わせ** | はい | いいえ |  
| **Power BI/Dynamics** | はい | あり (Azure AD の統合) |  
| **SaaS アプリ**| はい | あり (Azure AD の統合) |     

## <a name="saas-list"></a>SaaS のリスト

試用版や課金機能のない SaaS のリストの行動喚起は、"お問い合わせ" です。 

SaaS アプリケーションをリストするために、Azure Active Directory を構成する必要はありません。 

|必要条件  |詳細  |
|---------|---------|
|アプリが SaaS オファーである  |   ソリューションが SaaS オファリングであり、マルチテナント SaaS 製品を提供する      |


## <a name="saas-trial"></a>SaaS 試用版

無料で試すことができる SaaS (サービスとしてのソフトウェア) ベースの試用版としてソリューションまたはアプリを提供します。 無料試用版のオファーは、制限付き利用または制限された期間の試用版アカウントとして表示できます。 


|必要条件  |詳細  |
|---------|---------|
|アプリが SaaS オファーである  |   ソリューションが SaaS オファリングであり、マルチテナント SaaS 製品を提供する      |
|アプリで AAD が有効になっている     |   顧客があなたのドメインにリダイレクトされ、顧客と直接取引する       |


## <a name="saas-trial-technical-requirements"></a>SaaS 試用版の技術的な要件

SaaS アプリケーションの技術面の要件はシンプルです。 パブリッシャーは、発行する Azure Active Directory (Azure AD) と統合することのみ必要です。 Azure AD とアプリケーションの統合は明確に文書化されており、Microsoft はこれを実現する複数の SDK とリソースを提供しています。  

まず、Azure Marketplace での発行専用のサブスクリプションを用意し、作業を他の計画から分離できるようにすることをお勧めします。 これが完了すると、このサブスクリプションで SaaS アプリケーションを展開し、開発作業を開始することができます。  

最適な Azure Active Directory のドキュメント、サンプル、およびガイダンスについては、以下のサイトを参照してください。 

* [Azure Active Directory 開発者ガイド](../active-directory/develop/index.yml)

* [Azure Active Directory との統合](../active-directory/develop/active-directory-how-to-integrate.md)

* [Azure のロードマップ - セキュリティ + ID](https://azure.microsoft.com/roadmap/?category=security-identity)

ビデオ チュートリアルについては、以下をご覧ください。

* [Vittorio Bertocci による Azure Active Directory の認証](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 技術の簡単な説明 - パート 1/2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 技術の簡単な説明 - パート 2/2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Microsoft Azure Active Directory でのアプリの構築](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Active Directory に重点を置いた Microsoft Azure ビデオ](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

無料の Azure Active Directory トレーニングは以下で利用できます。  
* [IT プロ向けの Microsoft Azure コンテンツ シリーズ:Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

さらに、Azure Active Directory は、サービス更新プログラムを確認するサイトを提供しています   
* [Azure AD サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Azure Active Directory を使用して試用を有効にする  

Microsoft はすべての Marketplace ユーザーを Azure AD で認証するので、認証されたユーザーが Marketplace で試用リストをクリックし、試用環境にリダイレクトされた場合、それ以上のサインイン ステップを必要とせずに、ユーザーを直接試用にプロビジョニングできます。 認証の間にアプリが Azure AD から受け取るトークンには、アプリでユーザー アカウントを作成するために使うことができる重要なユーザー情報が含まれているので、プロビジョニング エクスペリエンスを自動化し、変換の可能性を高くすることができます。 トークンの詳細については、[サンプル トークン](../active-directory/develop/active-directory-token-and-claims.md)に関するページを参照してください。

Azure AD を使ってアプリまたは試用版の 1 クリック認証を有効にすると、次のことが可能になります。  
* Marketplace から試用版への顧客エクスペリエンスを簡素化する。  
* ユーザーが 'Marketplace からパブリッシャーのドメインまたは試用環境にリダイレクトされる場合でも、"製品内エクスペリエンス" の感覚を維持する。  
* 余分なサインイン手順がないため、リダイレクトで中止される可能性を低くする。  
* 大規模な Azure AD ユーザーの展開障壁を削減する。  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Marketplace に対する Azure AD 統合の認定  

アプリケーションがシングル テナントかマルチテナントか、Azure AD フェデレーション シングル サインオン (SSO) を初めて使用するか、既にサポートしているかに応じて、Azure AD の統合を認定します。  

**マルチテナント アプリケーションの場合:**  

既に Azure AD をサポートしている場合は、以下を実行します。
1.    Azure Portal でアプリケーションを登録します
2.    Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを取得する。 詳細については、[こちら](../active-directory/develop/active-directory-integrating-applications.md)を参照してください。  

Azure AD フェデレーション SSO が初めての場合は、以下を実行します。 
1.  Azure Portal でアプリケーションを登録します
2.  [OpenID Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) または [OAuth 2.0](../active-directory/develop/active-directory-protocols-oauth-code.md) を使って、Azure AD で SSO を開発します。
3.  AAD のマルチテナントのサポート機能を有効にして、ワンクリック試用環境を用意します。詳細情報については、[こちら](../active-directory/develop/active-directory-devhowto-appsource-certified.md)を参照してください。  

**シングルテナントアプリケーションの場合は、以下のいずれかのオプションを使用してください。**  
* [Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) を使用して、ユーザーをゲスト ユーザーとしてディレクトリに追加します
* "お問い合わせ" を使用して、顧客用に試用版を手動でプロビジョニングします
* 顧客ごとの "体験版" を開発します
* SSO を使ってマルチテナント サンプル デモ アプリをビルドします

## <a name="saas-subscriptions"></a>SaaS のサブスクリプション

SaaS ベースの技術的ソリューションをサブスクリプションとして顧客が購入できるようにするには、オファーの種類として SaaS アプリを使用します。 SaaS アプリでは、次の要件が満たされている必要があります。
- サービスの価格設置と請求を定額 (月額または年額) またはユーザー単位のレートで行う。
- サービスをいつでもアップグレードまたはキャンセルする手段を用意する。
商用トランザクションは、Microsoft がホストします。 顧客に対する請求は、Microsoft が公開元に代わって行います。 SaaS アプリをサブスクリプションとして提供するには、SaaS Fulfillment API と統合する必要があります。  サービスは、プロビジョニング、アップグレード、およびキャンセルをサポートする必要があります。

| 要件 | 詳細 |  
|:--- |:--- |  
|請求/メータリング | プランの価格は、発行する前に選択する価格モデル (定額またはユーザー単位) に基づいて決まります。  定額モデルを使用する場合、オプションで、定額料金には含まれない使用量に応じて、追加のディメンションを含めることができます。 |  
|キャンセル | オファーは、いつでも顧客が解約できます。 |  
|トランザクションのランディング ページ | Azure の共同ブランドのトランザクションのランディング ページをホストします。ここでは、ユーザーが自身の SaaS サービス アカウントを作成して管理します。 |   
| サブスクリプション API | SaaS サブスクリプションと対話できるサービスを公開して、ユーザー アカウントとサービス プランを作成、更新、および削除します。 重要な API の変更には、24 時間以内に対応する必要があります。 重要でない API の変更は定期的にリリースされます。 |  

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
まだ行っていない場合は、

* マーケットプレースについて[学習](https://azuremarketplace.microsoft.com/sell)します。

パートナー センターに登録するには、新しいプランの作成を開始するか、既存のものを操作します。

* [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)し、オファーを作成するか完成させます。
* 詳細については、「[新しい SaaS オファーを作成する](./partner-center-portal/create-new-saas-offer.md)」を参照してください。
