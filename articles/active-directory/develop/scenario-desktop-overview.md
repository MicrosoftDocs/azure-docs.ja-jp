---
title: Web API を呼び出すデスクトップ アプリの構築 | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデスクトップ アプリを構築する方法について説明します (概要)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 92f0909660427e414264442523dba3ed2abe0142
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771861"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>シナリオ:Web API を呼び出すデスクトップ アプリ

Web API を呼び出すデスクトップ アプリを構築するために必要なすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>はじめに

最初のアプリケーションをまだ作成していない場合は、.NET デスクトップのクイックスタート、ユニバーサル Windows プラットフォーム (UWP) のクイックスタートまたは macOS ネイティブ アプリのクイックスタートに従って作成してください。

> [!div class="nextstepaction"]
> [クイック スタート: Windows デスクトップ アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [クイック スタート: UWP アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [クイック スタート: macOS ネイティブ アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-ios.md)

## <a name="overview"></a>概要

デスクトップ アプリケーションを作成して、ユーザーをアプリケーションにサインインさせ、Microsoft Graph、他の Microsoft API、または独自の Web API などの Web API を呼び出します。 次のようにいくつかの可能性があります。

- 対話型トークン取得を使用できます。

  - ご利用のデスクトップ アプリケーションでグラフィカル コントロールがサポートされている場合 (たとえば、Windows.Form アプリケーション、WPF アプリケーション、macOS ネイティブ アプリケーションなどの場合)。
  - または、.NET Core アプリケーションであり、システム ブラウザーで Azure Active Directory (Azure AD) との認証のやり取りが発生することに同意している場合。

- Windows でホストされているアプリケーションの場合、Windows ドメインに参加しているコンピューター、または Azure AD に参加しているコンピューターで実行されているアプリケーションが、統合 Windows 認証を使用して自動的にトークンを取得することも可能です。
- 最後に、推奨されていませんが、パブリック クライアント アプリケーションではユーザー名とパスワードを使用することができます。 DevOps などの一部のシナリオではまだこれが必要になります。 これを使用すると、アプリケーションに制約が発生します。 たとえば、[多要素認証](../authentication/concept-mfa-howitworks.md) (条件付きアクセス) を実行する必要があるユーザーはサインインさせることができません。 また、アプリケーションはシングル サインオン (SSO) のメリットを受けられません。

  さらに、先進認証の原則に反しており、これはレガシの理由のためにのみ提供されています。

  ![デスクトップ アプリケーション](media/scenarios/desktop-app.svg)

- 移植可能なコマンド ライン ツール (おそらく、Linux または Mac で実行される .NET Core アプリケーション) を作成している場合、認証がシステム ブラウザーに委任されることに同意すると、対話式認証を使用できます。 .NET Core は [Web ブラウザー](https://aka.ms/msal-net-uses-web-browser)を提供しないため、システム ブラウザーで認証が行われます。 それ以外の場合、そのケースの最善の選択肢は、デバイス コード フローを使用することです。 このフローは、IoT アプリケーションなどのブラウザーがないアプリケーションにも使用されます。

  ![ブラウザーレス アプリケーション](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>詳細

デスクトップ アプリケーションには、いくつかの特異な点があります。 これらは主に、アプリケーションが対話型認証を使用するかどうかによって異なります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [デスクトップ アプリ:アプリの登録](scenario-desktop-app-registration.md)
