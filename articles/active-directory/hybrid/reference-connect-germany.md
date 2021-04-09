---
title: Microsoft Cloud Germany の Azure AD Connect
description: Azure AD Connect は、オンプレミスのディレクトリと Azure Active Directory を統合する機能です。 これにより、Azure AD と統合される Microsoft 365、Azure、SaaS アプリケーションに対して共通の ID が与えられます。
keywords: Azure AD Connect の紹介, Azure AD Connect の概要, Azure AD Connect とは, Active Directory のインストール, Germany, Black Forest
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246f8326f7112825de98ee824cf4bab7952a7878
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90014589"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Microsoft Cloud Germany の Azure AD Connect - パブリック プレビュー
## <a name="introduction"></a>はじめに
Azure AD Connect は、オンプレミス Active Directory と Azure Active Directory の間の同期を実現します。
現在、 [Microsoft Cloud Germany](https://azure.microsoft.com/global-infrastructure/germany/
) の多くのシナリオは、オペレーターが行う必要があります。 Microsoft Cloud Germany を使用する場合は、以下の情報を知っておく必要があります。

* 同期が正常に行われるようにするには、プロキシ サーバーで以下の URL を開く必要があります。
  
  * *.microsoftonline.de
  * *.windows.net
  * * 証明書の失効リスト
* Azure AD ディレクトリにサインインするときは、onmicrosoft.de ドメインのアカウントを使用する必要があります。

 
## <a name="download"></a>ダウンロード
Azure AD Connect は、ポータル内の Azure AD Connect ブレードからダウンロードできます。  Azure AD Connect ブレードを表示するには、次の手順に従います。

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect ブレード
Azure portal にサインインした後で、次の作業を行います。

1. [参照] に移動します。
2. [Azure Active Directory] を選択します。
3. 次に、[Azure AD Connect] を選択します。

次の情報が表示されます。

![Azure AD Connect Blade](./media/reference-connect-germany/germany1.png)

次の表は、ブレードに表示される機能の説明です。

| タイトル | 説明 |
| --- | --- |
| 同期状態 |同期が有効か無効かを示します。 |
| 最後の同期 |正常に完了した前回の同期です。 |
| フェデレーション ドメイン |現在構成されているフェデレーション ドメインの数を示します。 |

## <a name="installation"></a>インストール
Azure AD Connect をインストールする場合は、 [こちら](how-to-connect-install-roadmap.md)のドキュメントを参照してください。

## <a name="advanced-features-and-additional-information"></a>高度な機能と追加情報
カスタム設定や高度な構成に関する追加情報については、[オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)に関するページを参照してください。 このページには、情報と追加のガイダンスへのリンクが用意されています。

