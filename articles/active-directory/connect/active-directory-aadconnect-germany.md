---
title: "Microsoft Cloud Germany の Azure AD Connect"
description: "Azure AD Connect は、オンプレミスのディレクトリと Azure Active Directory を統合する機能です。 Office 365、Azure、SaaS など Azure AD と連動するアプリケーションの ID を共通化することができます。"
keywords: "Azure AD Connect の紹介, Azure AD Connect の概要, Azure AD Connect とは, Active Directory のインストール, Germany, Black Forest"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7db56a4c0efb208591bb15aa03a4c0dbf833d22e
ms.openlocfilehash: a6bb1c4b3a4972cdab9b99c548ef918a4d1070a0


---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Microsoft Cloud Germany の Azure AD Connect - パブリック プレビュー
## <a name="introduction"></a>はじめに
Azure AD Connect は、オンプレミス Active Directory と Azure Active Directory の間の同期を実現します。
現在、 [Microsoft Cloud Germany](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) の多くのシナリオは、オペレーターが行う必要があります。 Microsoft Cloud Germany を使用する場合は、以下の点に注意する必要があります。

* 同期が正常に行われるようにするには、プロキシ サーバーで以下の URL を開く必要があります。
  
  * *.microsoftonline.de
  * *.windows.net
  * * 証明書の失効リスト
* Azure AD ディレクトリにサインインするときは、onmicrosoft.de ドメインのアカウントを使用する必要があります。
* 以下の機能は使用できません。
  * Azure AD Connect Health
  * 自動更新
  * パスワードの書き戻し

## <a name="download"></a>ダウンロード
Azure AD Connect は、ポータル内の Azure AD Connect ブレードからダウンロードできます。  Azure AD Connect ブレードを表示するには、次の手順に従います。

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect ブレード
Azure Portal にサインインしたら、次の操作を行います。

1. [参照] に移動します。
2. [Azure Active Directory] を選択します。
3. 次に、[Azure AD Connect] を選択します。

次のように表示されます。

![Azure AD Connect Blade](media/active-directory-aadconnect-germany/germany1.png)

次の表は、ブレードに表示される機能の説明です。

| タイトル | Description |
| --- | --- |
| 同期状態 |同期が有効か無効かを示します。 |
| 最後の同期 |正常に完了した前回の同期です。 |
| フェデレーション ドメイン |現在構成されているフェデレーション ドメインの数を示します。 |

## <a name="installation"></a>インストール
Azure AD Connect をインストールする場合は、 [こちら](active-directory-aadconnect.md#install-azure-ad-connect)のドキュメントを参照してください。

## <a name="advanced-features-and-additional-information"></a>高度な機能と追加情報
追加情報と、カスタム設定や高度な構成のガイダンスについては、最初に「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。  このページには、情報と追加のガイダンスへのリンクが用意されています。




<!--HONumber=Jan17_HO1-->


