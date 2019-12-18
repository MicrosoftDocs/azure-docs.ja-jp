---
title: チュートリアル:ASP.NET Web API へのアクセスを許可する
titleSuffix: Azure AD B2C
description: Active Directory B2C を使用して ASP.NET Web API を保護し、ASP.NET Web アプリケーションからそれを呼び出す方法に関するチュートリアルです。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f9f773beb679b4f31e2b735fd0d74fc1b5dae2dd
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950164"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用して ASP.NET Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) で保護された Web API リソースを ASP.NET Web アプリケーションから呼び出す方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを構成する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

「[Tutorial:Enable authenticate in a web application using Azure Active Directory B2C (チュートリアル: Azure Active Directory B2C を使用して Web アプリケーションで認証を有効にする)](active-directory-b2c-tutorials-web-app.md)」の手順と前提条件を完了します。

## <a name="add-a-web-api-application"></a>Web API アプリケーションを追加する

アクセス トークンを提示するクライアント アプリケーションによる保護されたリソース要求を Web API リソースで受け取って処理できるためには、Web API リソースをテナントに登録しておく必要があります。

アプリケーションを Azure AD B2C テナントに登録するには、現在の**アプリケーション** エクスペリエンス、または新しく統合された**アプリの登録 (プレビュー)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[アプリケーション](#tab/applications/)

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、 **[追加]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、*webapi1* とします。
6. **[Web アプリ/Web API を含める]** には、 **[はい]** を選択します。
7. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 このチュートリアルでは、サンプルはローカル環境で実行され、`https://localhost:44332` でリッスンします。
8. **[アプリケーション ID URI]** には、ご使用の Web API で使用される ID を入力します。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 たとえば、「 `https://contosotenant.onmicrosoft.com/api` 」のように入力します。
9. **Create** をクリックしてください。
10. プロパティ ページで、アプリケーション ID を記録しておきます。これは、Web アプリケーションを構成するときに使用します。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[アプリの登録 (プレビュー)](#tab/app-reg-preview/)

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録 (プレビュー)]** 、 **[新規登録]** の順に選択します。
1. アプリケーションの**名前**を入力します。 たとえば、*webapi1* とします。
1. **[リダイレクト URI]** で、 **[Web]** を選択し、お使いのアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 このチュートリアルでは、サンプルはローカル環境で実行され、`https://localhost:44332` でリッスンします。
1. **[登録]** を選択します。
1. 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。

* * *

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば Web API のユーザーが、読み取りと書き込みの両方のアクセス権限を持つ場合もあれば、読み取りアクセス権限しか持たない場合もあります。 このチュートリアルでは、スコープを使用して、Web API の読み取りアクセス許可と書き込みアクセス許可を定義します。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>アクセス許可を付与する

保護された Web API をアプリケーションから呼び出すには、その API へのアクセス許可をアプリケーションに付与する必要があります。 前提条件のチュートリアルでは、Azure AD B2C に *webapp1* という名前の Web アプリケーションを作成しました。 このアプリケーションを使用して、Web API を呼び出します。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

アプリケーションが登録されて、保護された Web API を呼び出すことができるようになります。 アプリケーションを使用するには、ユーザーは Azure AD B2C での認証を行います。 アプリケーションで、保護された Web API にアクセスするための認可の付与が、Azure AD B2C から取得されます。

## <a name="configure-the-sample"></a>サンプルの構成

Web API を登録し、スコープを定義したので、Azure AD B2C テナントを使用するように Web API を構成します。 このチュートリアルでは、サンプル Web API を構成します。 サンプル Web API は、前提条件のチュートリアルでダウンロードしたプロジェクトに含まれます。

サンプル ソリューションには 2 つのプロジェクトがあります。

* **TaskWebApp** - タスク一覧を作成および編集します。 このサンプルでは、**サインアップまたはサインイン**のユーザー フローを使用してユーザーのサインアップまたはサインインを実行します。
* **TaskService** - タスク リストの作成、読み取り、更新、削除の機能をサポートします。 API は Azure AD B2C によって保護されており、TaskWebApp によって呼び出されます。

### <a name="configure-the-web-application"></a>Web アプリケーションを構成する

1. Visual Studio で **B2C-WebAPI-DotNet** ソリューションを開きます。
1. **TaskWebApp** プロジェクトで **Web.config** を開きます。
1. API をローカルで実行するには、**api:TaskServiceUrl** に localhost の設定を使用します。 次のように Web.config を変更します。

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. API の URI を構成します。 これは、Web アプリケーションが API 要求を行うために使用する URI です。 また、要求されたアクセス許可を構成します。

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Web API を構成する

1. **TaskService** プロジェクトで **Web.config** を開きます。
1. テナントを使用するように API を構成します。

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. クライアント ID に、登録した Web API アプリケーション (*webapi1*) のアプリケーション ID を設定します。

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. サインアップおよびサインインのユーザー フローの名前 (*B2C_1_signupsignin1*) で、ユーザー フローの設定を更新します。

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. ポータルで作成したものと一致するようにスコープの設定を構成します。

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>サンプルを実行する

**TaskWebApp** プロジェクトと **TaskService** プロジェクトの両方を実行する必要があります。

1. ソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。
1. **[マルチ スタートアップ プロジェクト]** を選択します。
1. 両方のプロジェクトの **[アクション]** を **[開始]** に変更します。
1. **[OK]** をクリックして構成を保存します。
1. **F5** キーを押して両方のアプリを実行します。 各アプリケーションは、それぞれ別のブラウザー ウィンドウで開かれます。
    * `https://localhost:44316/` は Web アプリケーションです。
    * `https://localhost:44332/` は Web API です。

1. Web アプリケーションで **[sign-up / sign-in]\(サインアップ/サインイン\)** を選択して、Web アプリケーションにサインインします。 前に作成したアカウントを使用します。
1. サインインした後、 **[To-do list]\(To Do リスト\)** を選択して To Do リスト アイテムを作成します。

To Do リスト アイテムを作成すると、Web アプリケーションは To Do リスト アイテムを生成するよう Web API に要求します。 保護されている Web アプリケーションでは、Azure AD B2C によって保護された Web API が呼び出されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを構成する

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する](tutorial-add-identity-providers.md)
