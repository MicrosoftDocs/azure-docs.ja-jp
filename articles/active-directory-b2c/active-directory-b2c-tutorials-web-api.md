---
title: チュートリアル - ASP.NET Web API へのアクセスを許可する - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C を使用して ASP.NET Web API を保護し、ASP.NET Web アプリケーションからそれを呼び出す方法に関するチュートリアルです。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 339b118e48a01469312a40e6b0652a4ffb90291a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347137"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用して ASP.NET Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory (Azure AD) B2C で保護された Web API リソースを ASP.NET Web アプリケーションから呼び出す方法について説明します。

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

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、 **[追加]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、*webapi1* とします。
6. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、 **[はい]** を選択します。
7. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 このチュートリアルでは、サンプルはローカル環境で実行され、`https://localhost:44332` でリッスンします。
8. **[アプリケーション ID URI]** には、ご使用の Web API で使用される ID を入力します。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 たとえば、「 `https://contosotenant.onmicrosoft.com/api` 」のように入力します。
9. **Create** をクリックしてください。
10. プロパティ ページで、アプリケーション ID を記録しておきます。これは、Web アプリケーションを構成するときに使用します。

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば Web API のユーザーが、読み取りと書き込みの両方のアクセス権限を持つ場合もあれば、読み取りアクセス権限しか持たない場合もあります。 このチュートリアルでは、スコープを使用して、Web API の読み取りアクセス許可と書き込みアクセス許可を定義します。

1. **[アプリケーション]** を選択し、*webapi1* を選択します。
2. **[公開済みスコープ]** を選択します。
3. **スコープ**に「`Hello.Read`」と入力し、説明に「`Read access to hello`」と入力します。
4. **スコープ**に「`Hello.Write`」と入力し、説明に「`Write access to hello`」と入力します。
5. **[Save]** をクリックします。

公開済みスコープを使用すると、クライアント アプリケーションに Web API へのアクセス許可を付与することができます。

## <a name="grant-permissions"></a>アクセス許可を付与する

保護された Web API をアプリケーションから呼び出すには、その API へのアクセス許可をアプリケーションに付与する必要があります。 前提条件のチュートリアルでは、Azure AD B2C に *webapp1* という名前の Web アプリケーションを作成しました。 このアプリケーションを使用して、Web API を呼び出します。

1. **[アプリケーション]** を選択し、*webapp1* を選択します。
2. **[API アクセス]** を選択し、 **[追加]** を選択します。
3. **[API の選択]** ドロップダウンで、*webapi1* を選択します。
4. **[スコープの選択]** ドロップダウンで、前に定義した **Hello.Read** スコープと **Hello.Write** スコープを選択します。
5. Click **OK**.

アプリケーションが登録されて、保護された Web API を呼び出すことができるようになります。 アプリケーションを使用するには、ユーザーは Azure AD B2C での認証を行います。 アプリケーションで、保護された Web API にアクセスするための認可の付与が、Azure AD B2C から取得されます。

## <a name="configure-the-sample"></a>サンプルの構成

Web API を登録し、スコープを定義したので、Azure AD B2C テナントを使用するように Web API を構成します。 このチュートリアルでは、サンプル Web API を構成します。 サンプル Web API は、前提条件のチュートリアルでダウンロードしたプロジェクトに含まれます。

サンプル ソリューションには 2 つのプロジェクトがあります。

サンプル ソリューションには次の 2 つのプロジェクトが含まれます。

- **TaskWebApp** - タスク一覧を作成および編集します。 このサンプルでは、**サインアップまたはサインイン**のユーザー フローを使用してユーザーのサインアップまたはサインインを実行します。
- **TaskService** - タスク リストの作成、読み取り、更新、削除の機能をサポートします。 API は Azure AD B2C によって保護されており、TaskWebApp によって呼び出されます。

### <a name="configure-the-web-application"></a>Web アプリケーションを構成する

1. Visual Studio で **B2C-WebAPI-DotNet** ソリューションを開きます。
2. **TaskWebApp** プロジェクトの **Web.config** を開きます。
3. API をローカルで実行するには、**api:TaskServiceUrl** に localhost の設定を使用します。 次のように Web.config を変更します。 

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. API の URI を構成します。 これは、Web アプリケーションが API 要求を行うために使用する URI です。 また、要求されたアクセス許可を構成します。

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Web API を構成する

1. **TaskService** プロジェクトの **Web.config** を開きます。
2. テナントを使用するように API を構成します。

    ```csharp
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. クライアント ID を、API 用に登録されたアプリケーション ID に設定します。

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. サインアップおよびサインインのユーザー フローの名前で、ユーザー フローの設定を更新します。

    ```csharp
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. ポータルで作成したものと一致するようにスコープの設定を構成します。

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>サンプルを実行する

**TaskWebApp** プロジェクトと **TaskService** プロジェクトの両方を実行する必要があります。 

1. ソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。 
2. **[マルチ スタートアップ プロジェクト]** を選択します。
3. 両方のプロジェクトの **[アクション]** を **[開始]** に変更します。
4. **[OK]** をクリックして構成を保存します。
5. **F5** キーを押して両方のアプリを実行します。 各アプリケーションは、それぞれ別のブラウザー タブで開かれます。`https://localhost:44316/` は Web アプリケーションです。
    `https://localhost:44332/` は Web API です。

6. Web アプリケーションで **[sign-up / sign-in]\(サインアップ/サインイン\)** をクリックして、Web アプリケーションにサインインします。 前に作成したアカウントを使用します。 
7. サインインした後、 **[To-do list]\(To Do リスト\)** をクリックして To Do リスト アイテムを作成します。

To Do リスト アイテムを作成すると、Web アプリケーションは To Do リスト アイテムを生成するよう Web API に要求します。 保護されている Web アプリケーションでは、Azure AD B2C テナント内の保護された Web API が呼び出されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを構成する

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する](tutorial-add-identity-providers.md)
