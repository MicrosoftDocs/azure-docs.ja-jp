---
title: Web API を呼び出すデスクトップ アプリ (アプリの登録) - Microsoft ID プラットフォーム
description: Web API を呼び出すデスクトップ アプリ (アプリの登録) をビルドする方法について学習します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ab2701a82da0b8f7bc4e23a3d947be905593e85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057221"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Web API を呼び出すデスクトップ アプリ - アプリの登録

この記事には、デスクトップ アプリケーションのアプリ登録の特異性が含まれています。

## <a name="supported-accounts-types"></a>サポートされているアカウントの種類

デスクトップ アプリケーションでサポートされているアカウントの種類は、ライトアップしたいエクスペリエンスに依存しており、したがって使用するフローにも依存しています。

### <a name="audience-for-interactive-token-acquisition"></a>対話型トークン取得の対象ユーザー

デスクトップ アプリケーションで対話型認証を使用する場合は、任意の[アカウントの種類](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)からユーザーにサインインすることができます

### <a name="audience-for-desktop-app-silent-flows"></a>デスクトップ アプリのサイレント フローの対象ユーザー

- 統合 Windows 認証またはユーザー名/パスワードを使用する場合は、アプリケーションが独自のテナント (LOB 開発者) 内のユーザー、または Azure Active Directory 組織 (ISV シナリオ) にサインインする必要があります。 これらの認証フローは、Microsoft の個人用アカウントではサポートされていません。
- デバイス コード フローを使用する場合は、ユーザーの Microsoft の個人アカウントを使用してユーザーにサインインすることはまだできません。
- B2C 機関とポリシーを渡すソーシャル ID を使用してユーザーにサインインする場合は、対話型のユーザー名とパスワード認証のみを使用できます。

## <a name="redirect-uris"></a>リダイレクト URI

ここでも、デスクトップ アプリケーションで使用するリダイレクト URI は、使用するフローに依存します。

- **対話型認証**または**デバイス コード フロー**を使っている場合は、`https://login.microsoftonline.com/common/oauth2/nativeclient` を使う必要があります。 アプリケーションの **[認証]** セクションで対応する URL をクリックすることで、この構成が実現します。
  
  > [!IMPORTANT]
  > 現在、MSAL.NET は、Windows で実行されているデスクトップ アプリケーションでは、既定で別のリダイレクト URI を使用しています (`urn:ietf:wg:oauth:2.0:oob`)。 今後、この既定を変更することを予定しているため、`https://login.microsoftonline.com/common/oauth2/nativeclient` を使用することをお勧めします。

- アプリで統合 Windows 認証、ユーザー名/パスワードのみを使用している場合は、アプリケーションのリダイレクト URI を登録する必要はありません。 実際には、これらのフローが Microsoft ID プラットフォームの v2.0 エンドポイントへのラウンド トリップを行い、アプリケーションは特定の URI にコールバックされません。 
- デバイス コード フロー、統合 Windows 認証、ユーザー名/パスワードを、いずれのリダイレクト URI も持たない機密性の高いクライアント アプリケーション フロー (デーモン アプリケーションで使用されるクライアント資格証明フロー) と区別するには、アプリケーションがパブリック クライアント アプリケーションであることを示す必要があります。 この構成を実現するには、アプリケーションの **[認証]** セクションに移動して、 **[詳細設定]** のサブセクションで、( **[既定のクライアントの種類]** の段落で) **[アプリケーションは、パブリック クライアントとして扱います]** の質問に対して **[はい]** を選択します。

  ![パブリック クライアントを許可する](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API のアクセス許可

デスクトップ アプリケーションが、サインインしたユーザーの代わりに API を呼び出します。 委任されたアクセス許可を要求する必要があります。 アプリケーションのアクセス許可を要求することはできません (これは[デーモン アプリケーション](scenario-daemon-overview.md)でのみ処理されます)。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デスクトップ アプリ - アプリの構成](scenario-desktop-app-configuration.md)
