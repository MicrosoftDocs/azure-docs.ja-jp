---
title: Web API を呼び出すモバイル アプリ - 概要 | Microsoft ID プラットフォーム
description: Web API を呼び出すモバイル アプリを構築する方法 (概要) について説明します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc5e12bf1633c32a61e03c7df6d6c0320263d6e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325326"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>シナリオ: Web API を呼び出すモバイル アプリケーション

Web API を呼び出すモバイル アプリを構築するために知っておくべきすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用の開始

最初のモバイル アプリケーションを作成する場合は、クイック スタートをお試しください。

> [!div class="nextstepaction"]
> [クイック スタート:Android アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-android.md)
>
> [クイック スタート:iOS アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-ios.md)
>
> [クイック スタート:Xamarin iOS および Android アプリからトークンを取得し、Microsoft Graph API を呼び出す](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概要

パーソナライズされたシームレスなユーザー エクスペリエンスはモバイル アプリにとって必要不可欠です。  Microsoft ID プラットフォームを使用すれば、モバイル開発者は iOS および Android のユーザーに対してこのエクスペリエンスを作り出すことができます。 お使いのアプリケーションで Azure Active Directory (Azure AD) ユーザー、個人の Microsoft アカウント ユーザー、Azure AD B2C ユーザーをサインインさせて、そのユーザーの代わりに Web API を呼び出すためのトークンを取得できます。 これらのフローを実装するため、業界標準の [OAuth2.0 承認コード フロー](v2-oauth2-auth-code-flow.md)を実装する Microsoft Authentication Library (MSAL) を使用します。

![デーモン アプリ](./media/scenarios/mobile-app.svg)

モバイル アプリに関する考慮事項:

- **ユーザー エクスペリエンスが重要**:ユーザーが、サインイン要求の前に該当のアプリの値を確認して、必要なアクセス許可のみを要求できるようにします。
- **すべてのユーザー構成をサポート**:多くのモバイル ビジネス ユーザーに、条件付きアクセスおよびデバイス コンプライアンスのポリシーが適用されています。 必ずこれらの重要なシナリオをサポートしてください。
- **シングル サインオン (SSO) の実装**:MSAL と Microsoft ID プラットフォームにより、デバイスのブラウザーまたは Microsoft Authenticator (および Android の Intune ポータル サイト) を介したシングル サインオンを容易に実現できます。

## <a name="specifics"></a>詳細

Microsoft ID プラットフォームでモバイル アプリを構築するときは、以下の考慮事項を念頭に置いてください。

- プラットフォームによっては、ユーザーが初めてサインインするときに何らかのユーザー対話が必要になる場合があります。 たとえば iOS では、Microsoft Authenticator (および Android の Intune ポータル サイト) を介して初めて SSO を使用するときに、アプリでユーザーの対話を表示する必要があります。
- iOS および Android の MSAL では、ユーザーをサインインさせるために、(お使いのアプリの上に表示される場合がある) 外部ブラウザーを使用することがあります。 アプリ内の WebView を代わりに使用するように構成をカスタマイズすることができます。
- モバイル アプリケーションでは、シークレットは決して使用しないでください。 すべてのユーザーがアクセスできるようになります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリの登録](scenario-mobile-app-registration.md)
