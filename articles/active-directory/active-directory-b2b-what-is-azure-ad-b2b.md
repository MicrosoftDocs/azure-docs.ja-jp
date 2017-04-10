---
title: "Azure Active Directory B2B コラボレーション プレビューについて | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします。"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4bfd76a00ba694096ef0b045b14beb757ba0809f
ms.lasthandoff: 03/21/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>Azure AD B2B コラボレーション プレビューについて
この記事では、Azure Active Directory (Azure AD) B2B コラボレーションのパブリック プレビュー更新の目標、機能、および 利点について説明します。

IT プロフェッショナルのユーザーもインフォメーション ワーカーのユーザーも、Azure AD B2B コラボレーション機能の多数の機能を利用できます。 その所在地、規模、業界、コンプライアンス要件、ガバナンス要件に関係なく、他の組織のパートナーと密接に連携することができます。 内部データの完全な制御を維持しながら、他の組織にドキュメント、リソース、アプリケーションへのアクセスを提供できます。

開発者の場合は、Azure AD B2B API を使用して、組織をまとめるアプリケーションを安全に作成することができます。 インフォメーション ワーカー ユーザーにとっては、プロセスとナビゲーションは単純です。

## <a name="how-b2b-collaboration-works"></a>B2B コラボレーションのしくみ

現在のプレビュー リリースでは、IT プロフェッショナルとインフォメーション ワーカーは、パートナー組織のユーザーを Azure Portal または Invitation Manager API を使用して一度に 1 人または数人追加することで、その組織との関係を確立しています。

管理者は、Azure Portal の新しいポータル エクスペリエンス (https://portal.azure.com) と PowerShell を使用して、関係を確立できます。

インフォメーション ワーカーは、http://myapps.microsoft.com でアクセス パネル エクスペリエンスを使用できます。

開発者は、Azure AD の B2B Invitation Manager API を使用して、B2B コラボレーション ユーザーの追加と招待および参加ワークフローのカスタマイズを行うアプリケーションを作成できます。

通常、B2B コラボレーション ユーザーは、招待および受諾プロセスを通して追加されます。 しくみは次のとおりです。

1. WoodGrove の John Doe は、Sam Oogle を、彼の Gmail アドレス (gsamoogle@gmail.com) を使用して、ユーザーとして追加したいと考えています。

2. John Doe は WoodGrove ポータル (portal.azure.com) またはアクセス パネル (myapps.microsoft.com) に移動してサインインし、WoodGrove ディレクトリ、グループ、またはアプリケーションにユーザー Sam Oogle を追加します。

3. John Doe は Sam Oogle に送信するカスタムの招待メールを指定します。

4. John doe が招待状を送信すると、次に示すように、ユーザー Sam Oogle が WoodGrove の Azure AD 内に作成されます。

  ![portal.azure.com の管理ユーザー インターフェイス](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. ユーザーが作成された後、Azure AD が Sam Oogle に招待メールを送信します。

  ![Sam Oogle 宛の招待メール](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. Sam Oogle は、招待状の **[開始]** を選択します。  
Azure Portal が開きます。

7. Sam Oogle は、Azure Portal にサインインします。

8. Azure AD は、次に示すように、Azure AD 内のユーザー オブジェクト Sam Oogle を、彼のトークンの情報を使用して更新します。

  ![Azure Portal でのユーザー Sam Oogle のプロファイル](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. これで、ユーザー Sam Oogle は招待を受諾したため、WoodGrove リソースにアクセスすることができます。 Azure AD のその他のユーザーと同じように、ユーザー Sam Oogle は、管理者が管理できます。 ユーザーの一覧を次に示します。

  ![Azure AD ユーザーの一覧](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>パブリック プレビュー機能
ユーザーからのフィードバックに基づき、B2B コラボレーションのパブリック プレビュー更新は、次を含む主要な機能を提供します。

* 管理者向け: [Azure Portal](https://portal.azure.com) のユーザー インターフェイスの機能が強化されました。 たとえば、管理者は、B2B ユーザーをディレクトリ、任意のグループ、またはアプリケーションに招待できます。  

* インフォメーション ワーカー向け: [アクセス パネル](https://myapps.microsoft.com)で、B2B コラボレーション セルフサービス招待機能を使用できます。 インフォメーション ワーカーは、各自が管理するセルフサービス グループまたはアプリケーションに B2B コラボレーション ユーザーを招待できます。

* 招待したユーザーに電子メール アドレスの設定を許可します。 ユーザーは、Office 365 またはオンプレミスの Microsoft Exchange のアドレス、outlook.com アドレス、またはソーシャル アドレス (Gmail、Yahoo! など) を使用して招待された組織にアクセスし、Azure AD または Microsoft アカウントを作成することができます。

* テナントによってブランド化されたプロフェッショナルな外観の招待メールを作成できます。

* 招待 API を使用して、ユーザーのオリエンテーションをカスタマイズできます。

* B2B コラボレーション ユーザーに対する多要素認証を招待元組織内に設定できます。

* 管理者以外のユーザーに招待を委任できます。

* B2B コラボレーション用の PowerShell のサポートを提供します。

* 監査およびレポート機能を提供します。

## <a name="help-us-shape-your-features"></a>機能に関するフィードバック送信のお願い
 
B2B コラボレーションを向上させる方法に関するフィードバックは絶えず受け付けています。 マイクロソフトはお客様に、ディスカッションや、ユーザー シナリオ、ベスト プラクティス、また Azure AD B2B コラボレーションに関するご意見の共有への参加を [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) で招待しています。
 
また、[B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) サイトでも、ご意見の送信や、今後の機能への投票を行うことができます。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [B2B コラボレーション ユーザーの監査およびレポート](active-directory-b2b-auditing-and-reporting.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

