---
title: Azure AD で SAML プロトコルを使用する方法 | Microsoft Docs
description: この記事では、Azure Active Directory のシングル サインオンおよびシングル サインアウト SAML プロファイルの概要を示します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57c5f21e04b2d3adad975be0368d9435583b4f72
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844671"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Azure AD で SAML プロトコルを使用する方法

Azure Active Directory (Azure AD) は SAML 2.0 プロトコルを使用して、アプリケーションでユーザーにシングル サインオン エクスペリエンスを提供できるようにします。 Azure AD の[シングル サインオン](single-sign-on-saml-protocol.md)と[シングル サインアウト](single-sign-out-saml-protocol.md) SAML プロファイルには、ID プロバイダー サービスでの SAML アサーション、プロトコルおよびバインドの使用方法について説明されています。

SAML プロトコルで、ID プロバイダー (Azure AD) とサービス プロバイダー (アプリケーション) に関する情報を交換する必要があります。

アプリケーションが Azure AD に登録されると、アプリ開発者は Azure AD にフェデレーションに関する情報を登録します。 この情報には、アプリケーションの**リダイレクト URI** と**メタデータ URI** が含まれます。

Azure AD では、クラウド サービスの**メタデータ URI** を使用し、署名キーとログアウト URI を取得します。 お客様は、 **[Azure AD]、[アプリの登録]** の順に選択してアプリを開き、 **[設定]、[プロパティ]** の順に選択してログアウト URL を更新できます。 このようにして、Azure AD から適切な URL に応答を送信できます。 

Azure Active Directory は、テナント固有および共通 (テナント独立) のシングル サインオンとシングル サインアウト エンドポイントを公開します。 これらの URL は、単なる識別子ではなくアドレス指定可能な場所を表します。したがって、ユーザーはエンドポイントに移動してメタデータを読み取ることができます。

* テナント固有のエンドポイントは、`https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` にあります。 *\<TenantDomainName>* プレースホルダーは、Azure AD テナントの登録済みドメイン名または TenantID GUID を表します。 たとえば、contoso.com テナントのフェデレーション メタデータは https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml にあります。

* テナント独立のエンドポイントは、`https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` にあります。 このエンドポイント アドレスの **common** は、テナントのドメイン名または ID の代わりに表示されます。

Azure AD で発行されるフェデレーション メタデータ ドキュメントについては、「[フェデレーション メタデータ](azure-ad-federation-metadata.md)」を参照してください。
