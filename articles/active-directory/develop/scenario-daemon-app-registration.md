---
title: Web API を呼び出すデーモン アプリを登録する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すデーモン アプリを構築する方法について説明します - アプリの登録
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 938a19276839d5e0d2bd3e0244510fc068cb029c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582909"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Web API を呼び出すデーモン アプリ - アプリの登録

デーモン アプリケーションでアプリを登録するときに知っておくべきことについて説明します。

## <a name="supported-account-types"></a>サポートされているアカウントの種類

デーモン アプリケーションは、Azure AD テナントでのみ意味があります。 そのため、このアプリケーションを作成するときは、次のいずれかのオプションを選択します。

- **この組織のディレクトリ内のアカウントのみ**。 デーモン アプリケーションは通常、基幹業務 (LOB) の開発者によって作成されるため、この選択が最も一般的なものです。
- **任意の組織のディレクトリ内のアカウント**。 お客様が顧客にユーティリティ ツールを提供する ISV である場合は、この選択を行います。 顧客のテナント管理者に承認してもらう必要があります。

## <a name="authentication---no-reply-uri-needed"></a>認証 - 応答 URI は不要

機密クライアント アプリケーションがクライアントの資格情報フロー "*のみ*" を使用する場合、応答 URI を登録する必要はありません。 アプリケーションの構成または構築でも必要ありません。 クライアント資格情報フローでは使用しません。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API のアクセス許可 - アプリのアクセス許可および管理者の同意

デーモン アプリケーションでは、(委任されたアクセス許可ではなく) API に対するアプリケーションのアクセス許可のみを要求できます。 アプリケーション登録の **[API のアクセス許可]** ページで **[アクセス許可の追加]** を選択して API ファミリを選択した後に、 **[アプリケーションのアクセス許可]** を選択し、該当のアクセス許可を選択します。

![アプリのアクセス許可と管理者の同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 呼び出す Web API では、委任されたアクセス許可ではなく、*アプリケーションのアクセス許可 (アプリ ロール)* を定義する必要があります。 そのような API を公開する方法の詳細については、[「保護された Web API: アプリの登録」のデーモン アプリで Web API が呼び出される場合](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)に関するセクションを参照してください。

デーモン アプリケーションには、Web API を呼び出すアプリケーションに対するテナント管理者の事前同意が必要です。 テナント管理者は、 **[Grant admin consent to *our organization*]\(組織に対して管理者の同意を付与\)** を選択することにより、同じ **[API のアクセス許可]** ページでこの同意を提供します。

マルチテナント アプリケーションを構築している ISV の場合は、[マルチテナント デーモン アプリの場合のデプロイ](scenario-daemon-production.md#deployment---multitenant-daemon-apps)に関するセクションをお読みください。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[アプリ コードの構成](./scenario-daemon-app-configuration.md)に関する記事に進みます。
