---
title: チュートリアル - Azure App Service の Web アプリに認証を追加する | Azure
description: このチュートリアルでは、Azure App Service で実行されている Web アプリの認証と承認を有効にする方法について説明します。 Web アプリへのアクセスを組織内のユーザーに制限します。
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: a8bd2ef1348692bf57f7e5cb7b6606cfcfd324fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905572"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>チュートリアル:Azure App Service で実行されている Web アプリに認証を追加する

Azure App Service で実行されている Web アプリの認証を有効にし、アクセスを組織内のユーザーに制限する方法について説明します。

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="ユーザー サインインを示す図。" border="false":::

App Service では組み込みの認証と承認がサポートされているので、Web アプリで最小限のコードを記述するだけで、またはコードをまったく記述せずに、ユーザーをサインインし、データにアクセスできます。 App Service の認証および承認モジュールの使用は必須ではありませんが、アプリの認証と承認の簡素化に役立ちます。 この記事では、Azure Active Directory (Azure AD) を ID プロバイダーとして使用して、App Service の認証および承認モジュールによって Web アプリをセキュリティで保護する方法について説明します。

認証および承認モジュールは、Azure portal とアプリの設定を通じて有効化および構成されます。 SDK、特定の言語、またはアプリケーションのコードの変更は必要ありません。 Azure AD、Microsoft Account、Facebook、Google、Twitter など、さまざまな ID プロバイダーがサポートされています。 認証および承認モジュールが有効になっている場合、すべての受信 HTTP 要求は、アプリ コードによって処理される前にこのモジュールを通過します。 詳細については、「 [Azure App Service での認証および認可](overview-authentication-authorization.md)」を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Web アプリの認証を構成する。
> * Web アプリへのアクセスを組織内のユーザーに制限する。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>App Service で Web アプリを作成して公開する

このチュートリアルでは、App Service にデプロイされた Web アプリが必要です。 既存の Web アプリを使用することも、[ASP.NET Core のクイックスタート](quickstart-dotnetcore.md)に従って新しい Web アプリを作成して App Service に公開することもできます。

既存の Web アプリを使用するか新しい Web アプリを作成するかにかかわらず、Web アプリ名と Web アプリのデプロイ先のリソース グループの名前をメモしてください。 これらの名前は、このチュートリアル全体を通して必要になります。 このチュートリアル全体を通して、プロシージャとスクリーンショットのサンプル名に *SecureWebApp* が含まれています。

## <a name="configure-authentication-and-authorization"></a>認証と承認を構成する

これで、App Service で実行されている Web アプリを用意できました。 次に、Web アプリの認証と承認を有効にします。 ID プロバイダーとして Azure AD を使用します。 詳細については、[App Service アプリケーションの Azure AD 認証の構成](configure-authentication-provider-aad.md)に関する記事をご覧ください。

[Azure portal](https://portal.azure.com) メニューで **[リソース グループ]** を選択するか、任意のページから **[リソース グループ]** を検索して選択します。

**[リソース グループ]** でリソース グループを検索して選択します。 **[概要]** で、アプリの管理ページを選択します。

:::image type="content" alt-text="アプリの管理ページの選択を示すスクリーンショット。" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

アプリの左側のメニューで **[認証/承認]** を選択し、 **[オン]** を選択して App Service 認証を有効にします。

**[要求が認証されない場合に実行するアクション]** で、 **[Azure Active Directory でのログイン]** を選択します。

**[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。 **[簡易]** を選択し、既定の設定をそのまま使用して新しい Active Directory アプリを作成します。 **[OK]** を選択します。

:::image type="content" alt-text="簡易認証を示すスクリーンショット" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::。

**[認証/承認]** ページで、 **[保存]** を選択します。

`Successfully saved the Auth Settings for <app-name> App` というメッセージを含む通知が表示されたら、ポータル ページを最新の情報に更新します。

これで、App Service の認証と承認によってアプリが保護されるようになりました。

## <a name="verify-limited-access-to-the-web-app"></a>Web アプリへの制限付きアクセスを確認する

App Service の認証および承認モジュールを有効にしたときに、Azure AD テナントにアプリの登録が作成されました。 アプリの登録には、Web アプリと同じ表示名が付けられています。 設定を確認するには、ポータル メニューから **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。 作成されたアプリの登録を選択します。 概要で、 **[Supported account types]\(サポートされているアカウントの種類\)** が **[所属する組織のみ]** に設定されていることを確認します。

:::image type="content" alt-text="アクセスの確認を示すスクリーンショット。" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

対象のアプリへのアクセスが組織内のユーザーに制限されていることを確認するには、シークレット モードまたはプライベート モードでブラウザーを起動し、`https://<app-name>.azurewebsites.net` に移動します。 セキュリティで保護されたサインイン ページが表示されるので、認証されていないユーザーにはサイトへのアクセスが許可されないことを確認できます。 サイトにアクセスするために、組織内のユーザーとしてサインインします。 新しいブラウザーを起動し、個人用アカウントを使用してサインインしてみることで、組織外のユーザーにはアクセス権がないことを確認することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了し、Web アプリや関連するリソースが不要になった場合は、[作成したリソースをクリーンアップ](scenario-secure-app-clean-up-resources.md)します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
>
> * Web アプリの認証を構成する。
> * Web アプリへのアクセスを組織内のユーザーに制限する。

> [!div class="nextstepaction"]
> [アプリ サービスからストレージにアクセスする](scenario-secure-app-access-storage.md)
