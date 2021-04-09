---
title: Azure Active Directory でのハイブリッド ID とは
description: ハイブリッド ID とは、オンプレミスとクラウドの両方で、認証と承認のための共通のユーザー ID を持つことです。
keywords: Azure AD Connect の紹介, Azure AD Connect の概要, Azure AD Connect とは, Active Directory のインストール
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895355b5acebffe6ad24b15b0c709d21e8f20be4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90016650"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Azure Active Directory でのハイブリッド ID とは

今日、ビジネスや企業ではますます、オンプレミスのアプリケーションとクラウド アプリケーションが混在して使用されるようになっています。  ユーザーは、オンプレミスおよびクラウドの両方のアプリケーションへのアクセス権を必要とします。 オンプレミスとクラウドの両方でユーザーを管理するのは困難なシナリオです。 

Microsoft の ID ソリューションは、オンプレミスおよびクラウドベースの機能を範囲とします。  これらのソリューションでは、場所に関係なく、すべてのリソースに対する認証と承認のための共通ユーザー ID を作成します。 これを **ハイブリッド ID** と呼んでいます。

Azure AD に対するハイブリッド ID、およびハイブリッド ID 管理によって、これらのシナリオが可能になります。

Azure AD でハイブリッド ID を実現するために、お使いのシナリオに応じて、3 つの認証方法のうち 1 つを使用できます。   次に 3 つの方法を示します。 

- **[パスワード ハッシュの同期 (PHS)](whatis-phs.md)**  
- **[パススルー認証 (PTA)](how-to-connect-pta.md)**  
- **[フェデレーション (AD FS)](whatis-fed.md)** 

これらの認証方法でも、[シングル サインオン](how-to-connect-sso.md)機能が提供されます。  シングル サインオンでは、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行います。

詳細については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](./choose-ad-authn.md)」を参照してください。 

## <a name="common-scenarios-and-recommendations"></a>一般的なシナリオと推奨事項 

以降では、ハイブリッド ID とアクセス管理に関する一般的なシナリオをいくつか取り上げると共に、それぞれのシナリオで最適と思われるハイブリッド ID オプションを紹介します。 

|必要事項|PHS および SSO<sup>1</sup>| PTA と SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|新しいユーザー、連絡先、およびオンプレミスの Active Directory で作成したグループ アカウントを自動的にクラウドに同期|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
|テナントを Microsoft 365 ハイブリッド シナリオ用に設定する。|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)| 
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
> <sup>4</sup> AD FS は、エンタープライズ PKI と統合すると、証明書を使用したサインインが可能になります。 これらの証明書は、MDM や GPO などの信頼できるプロビジョニング チャネル、スマートカードの証明書 (PIV/CAC カードなど) または Hello for Business (証明書信頼) を経由してデプロイされるソフト証明書を使用できます。 スマートカード認証のサポートの詳細については、[このブログ](/archive/blogs/samueld/adfs-certauth-aad-o365)を参照してください。 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Azure AD Connect を使用するためのライセンス要件

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>次のステップ 

- [Azure AD Connect と Connect Health とは](whatis-azure-ad-connect.md) 
- [パスワード ハッシュ同期 (PHS) とは](whatis-phs.md) 
- [パススルー認証 (PTA) とは](how-to-connect-pta.md) 
- [フェデレーションとは](whatis-fed.md) 
- [シングル サインオンとは](how-to-connect-sso.md)