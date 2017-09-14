---
title: "Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションを追加する方法 | Microsoft Docs"
description: "独自に開発したマルチテナント アプリケーションを Azure AD アプリケーション ギャラリーで表示する方法について説明します"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9f3caff0e998d670ce82b847525a63f2a85572d3
ms.contentlocale: ja-jp
ms.lasthandoff: 04/18/2017

---

# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションを追加する方法

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーとは

Azure AD アプリケーション ギャラリーは、何百万もの Azure Active Directory ユーザー全員にアプリケーションを公開し、そのアプリケーションの影響とリーチを市場で拡大する優れた手段です。 次の手順では、Azure AD アプリケーション ギャラリーでアプリケーションを表示する方法について説明します。

## <a name="if-your-application-supports-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされている場合
Azure AD アプリケーション ギャラリーで表示したいマルチテナント アプリケーションがある場合、次のいずれかのシングル サインオン テクノロジがそのアプリケーションでサポートされていることを最初に確認する必要があります。

1. **OpenID Connect** - 認証には OpenID Connect を使用し、構成には Azure AD Consent API を使用して Azure AD と直接統合します。 統合を始めたばかりで、アプリケーションが SAML をサポートしていない場合、このモードが推奨されます。
2. **SAML** - SAML プロトコルを使用してサードパーティの ID プロバイダーを構成する機能が既にアプリケーションにある場合に選択します。

Azure AD アプリケーション ギャラリーで表示したいマルチテナント アプリケーションでこれらのシングル サインオン モードのいずれかがサポートされている場合、下記のドキュメントの手順を実行することができます。 すぐに開始する場合、**waadpartners@microsoft.com** に電子メールをお送りください。

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされていない場合
これらのモードがどちらもアプリケーションでサポートされていない場合も、Microsoft のパスワード シングル サインオン テクノロジを使用してギャラリーに統合する方法があります。 この方法を検討したい場合は、**waadpartners@microsoft.com** に電子メールをお送りください。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory アプリケーション ギャラリーでアプリケーションを表示する方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

