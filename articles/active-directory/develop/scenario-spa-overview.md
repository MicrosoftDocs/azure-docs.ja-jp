---
title: JavaScript シングルページ アプリのシナリオ - Microsoft ID プラットフォーム | Azure
description: Microsoft ID プラットフォームを使用してシングルページ アプリケーションを構築する方法 (シナリオの概要) について説明します。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885176"
---
# <a name="scenario-single-page-application"></a>シナリオ:シングルページ アプリ

シングルページ アプリケーション (SPA) の構築に必要なすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>作業の開始

JavaScript SPA のクイック スタートに従って、最初のアプリケーションを作成できます。

> [!div class="nextstepaction"]
> [クイック スタート: シングルページ アプリケーション](./quickstart-v2-javascript.md)

## <a name="overview"></a>概要

多くの最新の Web アプリケーションは、クライアント側のシングル ページ アプリケーションとして構築されています。 開発者は、JavaScript または SPA フレームワーク (Angular、Vue.js、React.js など) を使用してそれらを作成します。 これらのアプリケーションは Web ブラウザーで実行され、その認証には、従来のサーバー側 Web アプリケーションとは異なる特性があります。 

Microsoft ID プラットフォームにより、シングルページ アプリケーションでユーザーをサインインさせ、[OAuth 2.0 暗黙的フロー](./v2-oauth2-implicit-grant-flow.md)を使用してバックエンド サービスまたは Web API にアクセスするトークンを取得できます。 暗黙的フローにより、アプリケーションから、認証されたユーザーを表す ID トークンを取得し、保護された API の呼び出しに必要なトークンにもアクセスできます。

![シングルページ アプリケーション](./media/scenarios/spa-app.svg)

この認証フローには、Electron や React-Native などのクロスプラットフォーム JavaScript フレームワークを使用するアプリケーション シナリオは含まれません。 それらでは、ネイティブ プラットフォームと対話するための追加の機能が必要になります。

## <a name="specifics"></a>詳細

アプリケーションでこのシナリオを有効にするには、次のことが必要です。

* Azure Active Directory (Azure AD) へのアプリケーションの登録。 この登録では、暗黙的フローを有効にし、トークンが返されるリダイレクト URI を設定する必要があります。
* アプリケーション ID などの登録済みアプリケーション プロパティを使用したアプリケーション構成。
* Microsoft Authentication Library (MSAL) を使用した、サインインしてトークンを取得するための認証フローの実行。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリの登録](scenario-spa-app-registration.md)
