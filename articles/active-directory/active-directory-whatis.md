---
title: "Azure Active Directory とは"
description: "Azure Active Directory を使用すると、既存のオンプレミス ID をクラウドに拡張したり、Azure AD 統合アプリケーションを開発したりすることができます。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.openlocfilehash: b6746afd508832afbd54153851b6f2ae404af147
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory とは
Azure Active Directory (Azure AD) は、マイクロソフトが提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。 Azure AD には主要なディレクトリ サービス、高度な ID 管理機能、アプリケーション アクセスの管理機能が統合されています。 また、Azure AD は、リッチな標準ベースのプラットフォームとなっており、開発者は、一元化されたポリシーとルールを基に、開発したアプリケーションへのアクセス制御を行うことができます。 

IT 管理者は、Azure AD により、Office365、Salesforce.com、DropBox、Concur など、 [さまざまなクラウド型 SaaS アプリケーション](active-directory-saas-tutorial-list.md) へのシングル サインオン (SSO) アクセスを従業員やビジネス パートナーに提供する使いやすいソリューションを手軽な価格で手に入れることができます。

また Azure AD は、世界中の数多くの組織が使用する卓越した ID 管理ソリューションとすばやく簡単に統合できるため、アプリケーション開発者がアプリケーションの構築に集中することを可能にします。

また Azure AD には、Multi-Factor Authentication、デバイスの登録、セルフサービスのパスワード管理、セルフサービスのグループ管理、特権を持つアカウントの管理、ロール ベースのアクセス制御、アプリケーション使用状況の監視、機能豊富な監査とセキュリティの監視、アラートなど、一連の ID 管理機能も用意されています。 これらの機能により、クラウド ベース アプリケーションのセキュリティ保護、IT プロセスの効率化、コストの削減を実現し、企業のコンプライアンス目標を確実に満たします。

さらに、わずか [4 回のクリック](./connect/active-directory-aadconnect-get-started-express.md)で Azure AD を既存の Windows Server Active Directory と統合できるため、組織は既存のオンプレミス ID への投資を活用して、クラウド ベースの SaaS アプリケーションへのアクセスを管理できます。

Office 365、Azure、Dynamics CRM Online をご利用の方は、既に Azure AD を使用していることを認識していない可能性もあります。 実際に、Office 365、Azure、Dynamics CRM のテナントは、いずれも既に Azure AD テナントとなっています。 必要なときはいつでも、そのテナントを使用して、Azure AD と統合するその他さまざまなクラウド アプリケーションへのアクセスを管理できます。

![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Azure AD の信頼性
Azure AD はマルチテナント型で地理的に分散した高可用性のデザインです。つまり Azure AD は、最も重要なビジネス ニーズでも信頼できるツールです。 Azure AD は、フェールオーバーが自動化された世界 28 か所のデータ センターから実行されているため、Azure AD の信頼性が高く、データ センターがダウンした場合でも、地理的に分散した 2 か所以上のデータ センターでディレクトリ データのデータが有効で、すぐにアクセスできるという安心感が得られます。

詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

## <a name="choose-an-edition"></a>エディションの選択
すべての Microsoft Online ビジネス サービスは、サインオンや他の ID のニーズに対応するために、Azure Active Directory (Azure AD) に依存しています。 Microsoft Online ビジネス サービス (Office 365、Microsoft Azure など) にサブスクライブすると、Azure AD を入手することができ、Free エディションのすべての機能を利用できます。 Azure Active Directory Free エディションでは、ユーザーとグループの管理、オンプレミスのディレクトリとの同期、Azure、Office 365、および Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox などの多くの人気のある SaaS アプリケーション間でのシングル サインオンを実現することができます。 

Azure Active Directory を強化するには、Azure Active Directory Basic、Premium P1、Premium P2 の各エディションを使用して有料の機能を追加します。 Azure Active Directory の有料エディションは、既存の無料のディレクトリ上に構築されます。セルフサービス、拡張された監視機能、セキュリティ レポート、Multi-Factor Authentication (MFA)、セキュリティで保護されたモバイル ユーザーのアクセスなどエンタープライズ クラスの機能を提供します。

> [!NOTE]
> これらのエディションの価格オプションについては、「 [Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。 Azure Active Directory Premium P1、Premium P2、Azure Active Directory Basic は、現在、中国ではサポートされていません。 詳細については、Azure Active Directory フォーラムからお問い合わせください。
>

* **Azure Active Directory Basic** : このエディションは、クラウド優先のニーズを持つタスク ワーカー向けに設計されており、クラウド中心のアプリケーション アクセスおよびセルフサービス ID 管理のソリューションを提供します。 Azure Active Directory Basic エディションでは、グループベースのアクセス管理、クラウド アプリケーション向けのセルフサービスのパスワード リセット、Azure Active Directory アプリケーション プロキシ (Azure Active Directory を使用してオンプレミス Web アプリケーションを発行するため) などの生産性の強化とコスト削減の機能が提供され、すべてがアップタイム 99.9% のエンタープライズレベルの SLA によって保証されます。
* **Azure Active Directory Premium P1** : Azure Active Directory Premium エディションは、より要求の厳しい ID とアクセスの管理を必要とする組織を支援することを目的として、機能豊富なエンタープライズレベルの ID 管理機能を追加し、ハイブリッド ユーザーがオンプレミスの機能とクラウドの機能にシームレスにアクセスできるようにします。 このエディションには、クラウド内のアプリケーション アクセス、セルフサービスの ID とアクセスの管理 (IAM)、ID 保護とセキュリティに関して、ハイブリッド環境のインフォメーション ワーカーと ID 管理者が必要とするすべてが含まれています。 また、動的なグループやセルフサービス グループ管理のような高度な管理と委任のリソースをサポートします。 さらに、Microsoft Identity Manager (オンプレミスの ID およびアクセス管理スイート) が含まれており、オンプレミス ユーザー向けのセルフ サービスのパスワード リセットなどのソリューションを実現するクラウドの書き戻し機能を提供します。
* **Azure Active Directory Premium P2** - すべてのユーザーと管理者を対象とした高度な保護機能を備えたこの新しいエディションには、Azure AD Premium P1 の全機能に加え、新しい Identity Protection と Privileged Identity Management が含まれています。 Azure Active Directory Identity Protection では、何十億ものシグナルを活用して、アプリケーションや会社の重要なデータへのリスク ベースの条件付きアクセスを提供します。 また、Azure Active Directory Privileged Identity Management では、特権アカウントを管理および保護できるので、管理者と管理者によるリソースへのアクセスを検出、制限、監視し、必要に応じて Just-In-Time アクセスを提供できます。  

> [!NOTE]
> さまざまな Azure Active Directory の機能を "従量課金制" のエディションで使用できます。
>
> * Active Directory B2C は、コンシューマー向けアプリケーションの ID およびアクセスの管理ソリューションです。 詳しくは、「 [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Azure Multi-Factor Authentication は、ユーザーごとまたは認証プロバイダーごとに使用できます。 詳しくは、「 [Azure Multi-Factor Authentication とは](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>利用を始めるには?

**IT 管理者の方:**

* [実際に使ってみる](https://azure.microsoft.com/trial/get-started-active-directory/) - 30 日間無料試用版に今すぐサインアップすると、このリンクを使って、初めてのクラウド ソリューションを 5 分程度でデプロイできます

* [Azure AD の概要](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium)に関するページで、Azure AD テナントをすばやく稼働させるためのヒントとテクニックをお読みください。

**開発者の方:**
 
* 「 [Azure Active Directory 開発者ガイド](active-directory-developers-guide.md) 」を参照してください。

* [試用版の開始](https://azure.microsoft.com/trial/get-started-active-directory/) – 30 日の無料試用版に今すぐサインアップして、Azure AD とアプリの統合を開始してください。

## <a name="next-steps"></a>次のステップ
[Azure の ID およびアクセス管理の基礎](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
