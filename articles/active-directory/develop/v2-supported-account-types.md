---
title: サポートされているアカウントの種類 | Azure
titleSuffix: Microsoft identity platform
description: アプリケーションの対象ユーザーとサポートされているアカウントの種類に関する概念的なドキュメント
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d6c184e2983a072dec4b3021a1b58a61cd206dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755984"
---
# <a name="supported-account-types"></a>サポートされているアカウントの種類

この記事では、Microsoft ID プラットフォーム アプリケーションでサポートされているアカウントの種類 ("*対象ユーザー*" とも呼ばれます) について説明します。

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>パブリック クラウドのアカウントの種類

Microsoft Azure パブリック クラウドでは、ほとんどの種類のアプリで、どのようなオーディエンスのユーザーでもサインインさせることができます。

- 基幹業務 (LOB) アプリケーションを記述している場合は、お客様の組織内のユーザーをサインインさせられます。 このようなアプリケーションは、*シングル テナント* と呼ばれることもあります。
- お客様が ISV である場合は、次の場合にユーザーをサインインさせるアプリケーションを記述できます。

  - 任意の組織に。 このようなアプリケーションは、*マルチ テナント* Web アプリケーションと呼ばれます。 職場または学校のアカウンでユーザーをサインインさせると解釈できる場合もあります。
  - 職場、学校または個人用の Microsoft アカウントを使用して。
  - 個人用の Microsoft アカウントのみを使用して。
    
- ビジネスから消費者へのアプリケーションを作成している場合は、Azure Active Directory B2C (Azure AD B2C) を使用して、ソーシャル ID でユーザーをサインインさせることもできます。

## <a name="account-type-support-in-authentication-flows"></a>認証フローでのアカウントの種類のサポート

一部のアカウントの種類は、特定の認証フローでは使用できません。 たとえば、デスクトップでは、UWP やデーモン アプリケーションが該当します。

- デーモン アプリケーションは Azure AD 組織でのみ使用できます。 Microsoft の個人アカウントを操作するためにデーモン アプリケーションの使用を試みる意味はありません。 管理者の同意は付与されません。
- 統合 Windows 認証フローは、(お客様の組織または任意の組織の) 職場または学校のアカウントでのみ使用できます。 統合 Windows 認証はドメイン アカウントで動作し、マシンをドメインに参加させるか Azure AD に参加させる必要があります。 このフローは、個人の Microsoft アカウントには意味がありません。
- [リソース所有者のパスワード資格情報付与](./v2-oauth-ropc.md) (ユーザー名/パスワード) は、個人の Microsoft アカウントでは使用できません。 個人の Microsoft アカウントでは、ユーザーがサインイン セッションごとに個人のリソースへのアクセスに同意する必要があります。 このため、この動作は非対話型フローとは互換性がありません。

## <a name="account-types-in-national-clouds"></a>各国のクラウドのアカウントの種類

アプリは、[各国のクラウド](authentication-national-cloud.md)にユーザーをサインさせることもできます。 ただし、Microsoft の個人アカウントは、これらのクラウドでサポートされていません。 そのため、これらのクラウドでサポートされるアカウントの種類は、お客様の組織 (シングル テナント) または任意の組織 (マルチ テナント アプリケーション) に限定されます。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory のテナント](./single-and-multi-tenant-apps.md)の詳細を確認する
- [各国のクラウド](./authentication-national-cloud.md)の詳細を確認する
