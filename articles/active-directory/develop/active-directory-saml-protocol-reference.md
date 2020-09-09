---
title: Microsoft ID プラットフォームでの SAML プロトコルの使用方法
description: この記事では、Azure Active Directory のシングル サインオンおよびシングル サインアウト SAML プロファイルの概要を示します。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552800"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Microsoft ID プラットフォームでの SAML プロトコルの使用方法

Microsoft ID プラットフォームでは、SAML 2.0 プロトコルを使用して、ユーザーにアプリケーションでのシングル サインオン エクスペリエンスを提供しています。 Azure AD の[シングル サインオン](single-sign-on-saml-protocol.md)と[シングル サインアウト](single-sign-out-saml-protocol.md) SAML プロファイルには、ID プロバイダー サービスでの SAML アサーション、プロトコルおよびバインドの使用方法について説明されています。

SAML プロトコルで、ID プロバイダー (Microsoft ID プラットフォーム) とサービス プロバイダー (アプリケーション) に関する情報を交換する必要があります。

アプリケーションが Azure AD に登録されると、アプリ開発者は Azure AD にフェデレーションに関する情報を登録します。 この情報には、アプリケーションの**リダイレクト URI** と**メタデータ URI** が含まれます。

Microsoft ID プラットフォームでは、クラウド サービスの**メタデータ URI** を使用し、署名キーとログアウト URI を取得します。 お客様は、 **[Azure AD]、[アプリの登録]** の順に選択してアプリを開き、 **[設定]、[プロパティ]** の順に選択してログアウト URL を更新できます。 このようにして、Microsoft ID プラットフォームから正しい URL に応答が送信されます。 

Azure Active Directory は、テナント固有および共通 (テナント独立) のシングル サインオンとシングル サインアウト エンドポイントを公開します。 これらの URL は、単なる識別子ではなくアドレス指定可能な場所を表します。したがって、ユーザーはエンドポイントに移動してメタデータを読み取ることができます。

* テナント固有のエンドポイントは、`https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` にあります。 *\<TenantDomainName>* プレースホルダーは、Azure AD テナントの登録済みドメイン名または TenantID GUID を表します。 たとえば、contoso.com テナントのフェデレーション メタデータは https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml にあります。

* テナント独立のエンドポイントは、`https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` にあります。 このエンドポイント アドレスの **common** は、テナントのドメイン名または ID の代わりに表示されます。

Azure AD で発行されるフェデレーション メタデータ ドキュメントについては、「[フェデレーション メタデータ](../azuread-dev/azure-ad-federation-metadata.md)」を参照してください。
