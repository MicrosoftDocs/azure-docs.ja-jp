---
title: Web API にアクセスするようにアプリケーションを構成する (プレビュー) | Azure
description: リダイレクト URI、資格情報、または Web API にアクセスするためのアクセス許可を含めるように、Microsoft ID プラットフォームに登録されたアプリケーションを構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ef499e49fc4d1a0dc79dfc4efb818f7330b57b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995208"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>クイック スタート:Web API にアクセスするようにクライアント アプリケーションを構成する (プレビュー)

Web/confidential クライアント アプリケーションが認証を必要とする認可付与フローに参加 (し、アクセス トークンを取得) できるようにするには、セキュリティで保護された資格情報を確立する必要があります。 Azure portal でサポートされている既定の認証方法は、クライアント ID と秘密鍵の組み合わせです。

さらに、同意フレームワークでは、リソース アプリケーションによって公開されている Web API (Microsoft Graph API など) にクライアントがアクセスできるようになる前に、要求されたアクセス許可に基づいてクライアントに必要なアクセス許可が付与されていることを確認します。 既定では、すべてのアプリケーションが Microsoft Graph API からのアクセス許可を選択できます。 [Graph API の [サインインとユーザー プロファイルの読み取り] アクセス許可](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions)が既定で選択されています。 目的の Web API ごとに [2 種類のアクセス許可](developer-glossary.md#permissions)から選択できます。

* **アプリケーションのアクセス許可** - クライアント アプリケーションは、アプリケーション自体として (ユーザー コンテキストなしで) Web API に直接アクセスする必要があります。 この種類のアクセス許可には管理者の同意が必要であるほか、パブリック (デスクトップおよびモバイル) クライアント アプリケーションでは使用できません。
* **委任されたアクセス許可** - クライアント アプリケーションは、サインインしているユーザーとして Web API にアクセスする必要があります。アクセスにあたっては、選択されているアクセス許可に応じて制限が適用されます。 この種類のアクセス許可は、管理者の同意が必要でない限り、ユーザーが付与できます。

  > [!NOTE]
  > 委任されたアクセス許可をアプリケーションに追加しても、テナント内のユーザーに対して自動的に同意が与えられるわけではありません。 管理者がすべてのユーザーに代わって同意を許可しない限り、ユーザーはやはり追加された委任されたアクセス許可に実行時に手動で同意する必要があります。

このクイック スタートでは、アプリを次のように構成する方法を説明します。

* [リダイレクト URI をアプリケーションに追加する](#add-redirect-uris-to-your-application)
* [資格情報を Web アプリケーションに追加する](#add-credentials-to-your-web-application)
* [Web API にアクセスするためのアクセス許可を追加する](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>前提条件

最初に、以下の前提条件を完了していることを確認します。

* サポートされている[アクセス許可と同意](v2-permissions-and-consent.md)について学習する。他のユーザーまたはアプリケーションによって使用されるアプリケーションを構築する場合は、これを理解しておくことが重要です。
* アプリケーションが登録されているテナントを持つ。
  * アプリを登録していない場合は、[Microsoft ID プラットフォームを使用してアプリケーションを登録する方法について学習](quickstart-register-app.md)します。
* Azure portal でアプリを登録するためのプレビュー エクスペリエンスをオプトインする。 このクイック スタートの手順は新しい UI に対応しており、プレビュー エクスペリエンスをオプトインした場合にのみ機能します。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure portal にサインインしてアプリを選択する

アプリを構成する前に、次の手順を実行します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選択し、**[アプリの登録 (プレビュー)]** を選択します。
1. 構成するアプリケーションを探して選択します。 アプリを選択すると、アプリケーションの **[概要]** またはメイン登録ページが表示されます。
1. Web API にアクセスするようにアプリケーションを構成するには、以下の手順を実行します。 
    * [リダイレクト URI をアプリケーションに追加する](#add-redirect-uris-to-your-application)
    * [資格情報を Web アプリケーションに追加する](#add-credentials-to-your-web-application)
    * [Web API にアクセスするためのアクセス許可を追加する](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>リダイレクト URI をアプリケーションに追加する

[![Web アプリとパブリック クライアント アプリのカスタム リダイレクト URI を追加する](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

リダイレクト URI をアプリケーションに追加するには:

1. アプリの **[概要]** ページで、**[認証]** セクションを選択します。

1. Web アプリケーションとパブリック クライアント アプリケーションのカスタム リダイレクト URI を追加するには、次の手順を実行します。

   1. **[リダイレクト URI]** セクションを見つけます。
   1. 構築するアプリケーションの種類として **[Web]** または **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。
   1. アプリケーションのリダイレクト URI を入力します。
      * Web アプリケーションの場合は、アプリケーションのベース URL を指定します。 ローカル コンピューターで実行されている Web アプリケーションの URL であれば、たとえば `http://localhost:31544` のようになります。 ユーザーはこの URL を使用して、Web クライアント アプリケーションにサインインすることになります。
      * パブリック アプリケーションの場合は、トークン応答を返すために Azure AD に使用される URI を指定します。 https://MyFirstApp など、アプリケーションに固有の値を入力してください。

1. パブリック クライアント (モバイル、デスクトップ) に推奨されるリダイレクト URI の中から選択するには、以下の手順に従います。

    1. **[パブリック クライアント (モバイル、デスクトップ) に推奨されるリダイレクト URI]** セクションを探します。
    1. チェック ボックスを使用して、アプリケーションの適切なリダイレクト URI を選択します。

## <a name="add-credentials-to-your-web-application"></a>資格情報を Web アプリケーションに追加する

[![資格情報とクライアント シークレットを追加する](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

資格情報を Web アプリケーションに追加するには:

1. アプリの **[概要]** ページで、**[証明書とシークレット]** セクションを選択します。

1. 証明書を追加するには、次の手順を実行します。

    1. **[証明書のアップロード]** を選択します。
    1. アップロードするファイルを選択します。 ファイルの種類は .cer、.pem、.crt のいずれかである必要があります。
    1. **[追加]** を選択します。

1. クライアント シークレットを追加するには、次の手順を実行します。

    1. **[新しいクライアント シークレット]** を選択します。
    1. クライアント シークレットの説明を追加します。
    1. 期間を選択します。
    1. **[追加]** を選択します。

> [!NOTE]
> 構成の変更を保存すると、右端の列にクライアント シークレットの値が格納されます。 クライアント アプリケーションのコードで使用するので、**必ず値をコピーしておいてください**。このページを一度閉じると、値にアクセスできなくなります。

## <a name="add-permissions-to-access-web-apis"></a>Web API にアクセスするためのアクセス許可を追加する

[![API のアクセス許可を追加する](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

クライアントからリソース API にアクセスするためのアクセス許可を追加するには:

1. アプリの **[概要]** ページで、**[API のアクセス許可]** を選択します。
1. **[アクセス許可の追加]** ボタンを選択します。
1. 既定では、ビューで **[Microsoft API]** から選択することができます。 次の中から、目的の API のセクションを選択します。
    * **[Microsoft API]** - Microsoft Graph などの Microsoft API のアクセス許可を選択することができます。
    * **[所属する組織で使用している API]** - 組織によって公開されている API、または組織が統合した API のアクセス許可を選択することができます。
    * **[自分の API]** - 自分で公開した API のアクセス許可を選択することができます。
1. API を選択すると、**[API アクセス許可の要求]** ページが表示されます。 委任されたアクセス許可とアプリケーションのアクセス許可の両方を API が公開している場合は、アプリケーションに必要なアクセス許可の種類を選択します。
1. 完了したら、**[アクセス許可の追加]** を選択します。 **[API のアクセス許可]** ページに戻ります。アクセス許可は保存され、テーブルに追加されています。

## <a name="next-steps"></a>次の手順

以下のその他のアプリ管理関連のクイック スタートを学習します。

* [Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)
* [Web API を公開するようにアプリケーションを構成する](quickstart-configure-app-expose-web-apis.md)
* [アプリケーションによってサポートされるアカウントを変更する](quickstart-modify-supported-accounts.md)
* [Microsoft ID プラットフォームに登録されたアプリケーションを削除する](quickstart-remove-app.md)

登録されたアプリケーションを表す 2 つの Azure AD オブジェクトと、両者間の関係については、[Application objects and service principal objects](app-objects-and-service-principals.md)\(アプリケーション オブジェクトとサービス プリンシパル オブジェクト\) を参照してください。

Azure Active Directory でアプリケーションを開発するときに使用するブランド化ガイドラインについては、[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)を参照してください。
