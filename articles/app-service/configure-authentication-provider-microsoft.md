---
title: Microsoft 認証を構成する
description: App Services アプリまたは Azure Functions アプリ用の ID プロバイダーとして、Microsoft アカウント認証を構成する方法について説明します。
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043247"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Microsoft アカウント ログインを使用するように App Service アプリまたは Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、AAD を使用して個人用の Microsoft アカウント ログインをサポートするように Azure App Service または Azure Functions を構成する方法を示します。

> [!NOTE]
> 個人用の Microsoft アカウントと組織アカウントの両方で AAD ID プロバイダーが使用されます。 現時点では、両方の種類のログインをサポートするように、この ID プロバイダーを構成することはできません。

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"></a>Microsoft アカウントにアプリを登録する

1. Azure portal で [ **[アプリの登録]**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) に移動します。 必要であれば、Microsoft アカウントを使ってサインインします。
1. **[New registration]\(新規登録\)** を選択し、アプリケーション名を入力します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)]\(任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント) と、個人用の Microsoft アカウント (Skype、Xbox など)\)** を選択します。
1. **[リダイレクト URI]** で **[Web]** を選択し、「`https://<app-domain-name>/.auth/login/aad/callback`」と入力します。 *\<app-domain-name>* をご自分のアプリのドメイン名に置き換えます。  たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。 URL には HTTPS スキームを必ず使用します。

1. **[登録]** を選択します。
1. **アプリケーション (クライアント) ID** をコピーします。 この情報は後で必要になります。
1. 左側のウィンドウで、 **[Certificates & secrets]\(証明書とシークレット\)** を選択し、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。 説明を入力し、有効期間を選択し、 **[追加]** を選択します。
1. **[Certificates & secrets]\(証明書とシークレット\)** ページに表示される値をコピーします。 この値はページから移動すると再び表示されません。

    > [!IMPORTANT]
    > クライアント シークレット値 (パスワード) は、重要なセキュリティ資格情報です。 このパスワードを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"></a>Microsoft アカウントの情報を App Service アプリケーションに追加する

1. [Azure portal] で自分のアプリケーションに移動します。
1. **[設定]** 、 **[認証/承認]** の順に選択し、 **[App Service 認証]** が **[オン]** になっていることを確認します。
1. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。 **[管理モード]** の下の **[Advanced]\(詳細\)** を選択します。 前に取得したアプリケーション (クライアント) ID とクライアント シークレットを貼り付けます。 **[発行者の URL]** フィールドには **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** を使用します。
1. **[OK]** を選択します。

   App Service は認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。

1. (省略可能) Microsoft アカウント ユーザーへのアクセスを制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。 この機能を設定すると、お使いのアプリでは、すべての要求を認証する必要があります。 また、認証されていない要求はすべて、AAD を使用した認証にリダイレクトされます。 Microsoft アカウント テナントを使用するように **発行者の URL** を構成したため、個人用のアカウントのみが正常に認証されることにご注意ください。

   > [!CAUTION]
   > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページが与えられているアプリには適切でない場合があります。 このようなアプリケーションの場合は、アプリ自体が手動で認証を開始する、 **[匿名要求を許可する (操作不要)]** が推奨されることがあります。 詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」をご覧ください。

1. **[保存]** を選択します。

これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
