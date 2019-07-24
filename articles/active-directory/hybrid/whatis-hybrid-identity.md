---
title: Active Directory を Azure Active Directory と接続する | Microsoft Docs
description: Azure AD Connect は、オンプレミスのディレクトリと Azure Active Directory を統合する機能です。 Office 365、Azure、SaaS など Azure AD と連動するアプリケーションの ID を共通化することができます。
keywords: Azure AD Connect の紹介, Azure AD Connect の概要, Azure AD Connect とは, Active Directory のインストール
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 536edcf74bff6f89dade4a713c40c9bef12e18af
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881806"
---
# <a name="what-is-hybrid-identity"></a>ハイブリッド ID とは

今日、ビジネスや企業ではますます、オンプレミスのアプリケーションとクラウド アプリケーションが混在して使用されるようになっています。  ユーザーは、オンプレミスおよびクラウドの両方のアプリケーションへのアクセス権を必要とします。 この要件は、難易度の高いシナリオになっています。 

Microsoft の ID ソリューションは、オンプレミスおよびクラウドベースの機能を範囲とします。  これらのソリューションでは、場所に関係なく、すべてのリソースに対する認証と承認のための共通ユーザー ID を作成します。 これを**ハイブリッド ID** と呼んでいます。

ハイブリッド ID を実現するために、お使いのシナリオに応じて、3 つの認証方法のうち 1 つを使用できます。   次に 3 つの方法を示します。 

- **[パスワード ハッシュの同期 (PHS)](whatis-phs.md)**  
- **[パススルー認証 (PTA)](how-to-connect-pta.md)**  
- **[フェデレーション (AD FS)](whatis-fed.md)** 

これらの認証方法でも、[シングル サインオン](how-to-connect-sso.md)機能が提供されます。  シングル サインオンでは、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行います。

詳細については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)」を参照してください。 

## <a name="common-scenarios-and-recommendations"></a>一般的なシナリオと推奨事項 

以降では、ハイブリッド ID とアクセス管理に関する一般的なシナリオをいくつか取り上げると共に、それぞれのシナリオで最適と思われるハイブリッド ID オプションを紹介します。 

|必要事項|PHS および SSO<sup>1</sup>| PTA と SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|新しいユーザー、連絡先、およびオンプレミスの Active Directory で作成したグループ アカウントを自動的にクラウドに同期|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|テナントを Office 365 ハイブリッド シナリオ用にセット アップする。|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|ユーザーがオンプレミスのパスワードを使用してサインインしたりクラウド サービスにアクセスしたりすることを有効化する。|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|企業の資格情報を使用したシングル サインオンを実装する。|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)|  
|クラウドにパスワード ハッシュが保存されないようにする。| |![推奨](./media/whatis-hybrid-identity/ic195031.png)|![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|クラウドベースの多要素認証ソリューションを有効化する。|![推奨](./media/whatis-hybrid-identity/ic195031.png)|![推奨](./media/whatis-hybrid-identity/ic195031.png)|![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|オンプレミスの Multi-factor Authentication ソリューションを有効化する。| | |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|ユーザーのスマートカード認証をサポートする。<sup>4</sup>| | |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|Office ポータル および Windows 10 デスクトップにパスワードの有効期限通知を表示する。| | |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> シングル サインオンによるパスワード ハッシュ同期。 
> 
> <sup>2</sup> パススルー認証およびシングル サインオン。  
> 
> <sup>3</sup> AD FS を使用したフェデレーション シングル サインオン。  
>  
> <sup>4</sup> AD FS は、エンタープライズ PKI と統合すると、証明書を使用したサインインが可能になります。 これらの証明書は、MDM や GPO などの信頼できるプロビジョニング チャネル、スマートカードの証明書 (PIV/CAC カードなど) または Hello for Business (証明書信頼) を経由してデプロイされるソフト証明書を使用できます。 スマートカード認証のサポートの詳細については、[このブログ](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)を参照してください。 
> 

## <a name="next-steps"></a>次の手順 

- [Azure AD Connect と Connect Health とは](whatis-azure-ad-connect.md) 
- [パスワード ハッシュ同期 (PHS) とは](whatis-phs.md) 
- [パススルー認証 (PTA) とは](how-to-connect-pta.md) 
- [フェデレーションとは](whatis-fed.md) 
- [シングル サインオンとは](how-to-connect-sso.md) 

