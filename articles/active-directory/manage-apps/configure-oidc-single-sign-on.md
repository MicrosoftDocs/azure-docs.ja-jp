---
title: Azure Active Directory におけるアプリに対する OIDC ベースのシングル サインオン (SSO) について理解する
description: Azure Active Directory におけるアプリに対する OIDC ベースのシングル サインオン (SSO) について理解する。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: ffaa55d4aa482e8f0eda93b1b32db6310d17e2a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255251"
---
# <a name="understand-oidc-based-single-sign-on"></a>OIDC ベースのシングル サインオンについて理解する
アプリケーション管理の[クイックスタート シリーズ](view-applications-portal.md)では、アプリケーションの ID プロバイダー (IdP) として Azure AD を使用する方法について学習しました。 この記事では、OpenID Connect 標準を使用してシングル サインオンを実装するアプリについて詳しく説明します。 

## <a name="before-you-begin"></a>開始する前に
Azure Active Directory テナントにアプリを追加するプロセスは、アプリケーションで実装されているシングル サインオンの種類によって異なります。 ID 管理に Azure AD を使用できるアプリで使用できるシングル サインオン オプションの詳細については、[シングル サインオン オプション](sso-options.md)に関するページを参照してください。 この記事では、OIDC ベースのアプリについて説明します。


## <a name="basic-oidc-configuration"></a>基本的な OIDC 構成
[クイックスタート シリーズ](add-application-portal-setup-oidc-sso.md)には、シングル サインオンの構成に関する記事があります。 そこでは、OIDC ベースのアプリを Azure テナントに追加する方法について説明しています。

OIDC 標準をシングル サインオンに使用するアプリを追加する利点は、構成が最小限になることです。 次の短いビデオは、OIDC ベースのアプリをテナントに追加する方法を示しています。

Azure Active Directory で OIDC ベースのアプリを追加する

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

ユーザーおよび管理者の同意の詳細については、「[ユーザーおよび管理者の同意について](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [アプリケーション管理のクイックスタート シリーズ](add-application-portal-setup-oidc-sso.md)
- [シングル サインオンのオプション](sso-options.md)
- [方法: すべての Azure Active Directory ユーザーがマルチテナント アプリケーション パターンを使用してサインインする](../develop/howto-convert-app-to-be-multi-tenant.md)
