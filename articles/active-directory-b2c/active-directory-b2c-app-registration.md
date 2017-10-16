---
title: "Azure Active Directory B2C: アプリケーションの登録 | Microsoft Docs"
description: "Azure Active Directory B2C にアプリケーションを登録する方法"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: PatAltimore
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 0e91d249116d39675d6e4bdf429442b6bc8af391
ms.contentlocale: ja-jp
ms.lasthandoff: 09/01/2017

---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: アプリケーションを登録する

このクイック スタートを読むと、Microsoft Azure Active Directory (Azure AD) B2C テナントに数分でアプリケーションを登録できるようになります。 完了すると、Azure B2C テナントで使用するアプリケーションが登録されます。

## <a name="prerequisites"></a>前提条件

コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C テナントに登録する必要があります。 「 [Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)」に書かれている手順に従って独自のテナントを取得してください。

Azure Portal の Azure AD B2C ブレードから作成したアプリケーションは、同じ場所から管理する必要があります。 PowerShell または別のポータルを使用して B2C アプリケーションを編集すると、そのアプリケーションはサポート対象外となり、Azure AD B2C で動作しなくなります。 詳細については、「[アプリの障害](#faulted-apps)」セクションを参照してください。 

この記事では、サンプルを使い始めるために役立つ例を使用しています。 これらのサンプルの詳細については、後続の記事で説明しています。

## <a name="navigate-to-b2c-settings"></a>B2C の設定に移動する

B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にログインします。 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>アプリケーションの種類に基づいて次の手順を選択する

* [Web アプリケーションの登録](#register-a-web-app)
* [Web API の登録](#register-a-web-api)
* [モバイル/ネイティブ アプリケーションの登録](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Web アプリの登録

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

Web アプリケーションから、Azure AD B2C によって保護された Web API を呼び出す場合、次の手順を実行します。
   1. **[キー]** ブレードに移動して **[キーの生成]** ボタンをクリックし、アプリケーション シークレットを作成します。 **[アプリ キー]** の値をメモしておきます。 アプリケーションのコード内では、この値をアプリケーション シークレットとして使用します。
   2. **[API アクセス]**、**[追加]** の順にクリックし、Web API とスコープ (アクセス許可) を選択します。

> [!NOTE]
> **アプリケーション シークレット** は重要なセキュリティ資格情報であり、適切にセキュリティで保護する必要があります。
> 

[**次の手順**に進む](#next-steps)

### <a name="register-a-web-api"></a>Web API の登録

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

必要に応じて **[公開済みスコープ ]** をクリックしてスコープを追加します。 既定では、"user_impersonation" スコープが定義されます。 user_impersonation スコープによって、他のアプリケーションが、サインイン ユーザーの代わりにこの API にアクセスできるようになります。 不要であれば user_impersonation スコープは削除してかまいません。

[**次の手順**に進む](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>モバイル/ネイティブ アプリの登録

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[**次の手順**に進む](#next-steps)

## <a name="limitations"></a>制限事項

### <a name="choosing-a-web-app-or-api-reply-url"></a>Web アプリ/API の応答 URL の選択

現時点では、Azure AD B2C に登録されるアプリは、限られた応答 URL 値に制限されています。 Web アプリと Web サービスの応答 URL は `https` スキームで始まる必要があり、すべての応答 URL 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、次の応答 URL のいずれかを使用する Web アプリを登録することはできません。

`https://login-east.contoso.com`

`https://login-west.contoso.com`

登録システムによって、既存の応答 URL の DNS 名全体と、追加しようとしている応答 URL の DNS 名が比較されます。 次のいずれかの条件に当てはまる場合、DNS 名を追加する要求は失敗します。

* 新しい応答 URL の DNS 名全体が、既存の応答 URL の DNS 名と一致しない場合。
* 新しい応答 URL の DNS 名全体が、既存の応答 URL のサブドメインではない場合。

たとえば、アプリで次の応答 URL を使用しているとします。

`https://login.contoso.com`

次のように、これに追加できます。

`https://login.contoso.com/new`

この場合、DNS は完全に一致します。 または、次を行うことができます。

`https://new.login.contoso.com`

この場合、login.contoso.com の DNS サブドメインを参照しています。応答 URL として login-east.contoso.com と login-west.contoso.com を使用するアプリが必要な場合は、これらの応答 URL を次の順番で追加する必要があります。

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

後の 2 つの応答 URL を追加できるのは、これらが 1 つ目の contoso.com という応答 URL のサブドメインであるためです。

### <a name="choosing-a-native-app-redirect-uri"></a>ネイティブ アプリケーションのリダイレクト URI の選択

モバイル/ネイティブ アプリケーションのリダイレクト URI を選択する際には、2 つの重要な考慮事項があります。

* **一意**: リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 この例 (com.onmicrosoft.contoso.appname://redirect/path) では、スキームとして com.onmicrosoft.contoso.appname を使用しています。 このパターンに従うことをお勧めします。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーには [アプリの選択] ダイアログが表示されます。 ユーザーが不適切な選択を行った場合、ログインは失敗します。
* **完全**: リダイレクト URI には、スキームとパスが必要です。 パスには、ドメインの後に 1 つ以上のスラッシュを含める必要があります (たとえば、//contoso/ は機能しますが、//contoso は失敗します)。

リダイレクト URI にアンダースコアなどの特殊文字がないことを確認します。

### <a name="faulted-apps"></a>アプリの障害

B2C アプリケーションは次の手段で編集しないでください。

* 他のアプリケーション管理ポータル ([Azure クラシック ポータル](https://manage.windowsazure.com/)や[アプリケーション登録ポータル](https://apps.dev.microsoft.com/)など)
* Graph API または PowerShell

前に説明したように、B2C アプリケーションを編集し、それを Azure Portal の Azure AD B2C 機能ブレードでもう一度編集しようとすると、アプリに障害が発生し、Azure AD B2C で使用できなくなります。 この場合、アプリケーションを削除し、もう一度作成する必要があります。

アプリケーションを削除するには、[アプリケーション登録ポータル](https://apps.dev.microsoft.com/)に移動し、そこでアプリケーションを削除します。 アプリケーションを表示するためには、そのアプリケーションの所有者である必要があります (テナントの管理者であるだけでは不十分です)。

## <a name="next-steps"></a>次のステップ

アプリケーションを Azure AD B2C に登録したので、いずれかの[クイック スタート チュートリアル](active-directory-b2c-overview.md#get-started)を実行して使い始めることができます。

> [!div class="nextstepaction"]
> [サインアップ、サインイン、パスワード リセット機能を備えた ASP.NET Web アプリを作成する](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
