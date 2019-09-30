---
title: Microsoft ID プラットフォーム Java Web アプリのクイックスタート | Azure
description: OpenID Connect を使用して、Java Web アプリ上で Microsoft サインインを実装する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: 22d65d20220bfda821fe255e08dd056b761a828d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086990"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>クイック スタート:Java Web アプリに "Microsoft でサインイン" を追加する

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

このクイックスタートでは、Java Web アプリを Microsoft ID プラットフォームと統合する方法を説明します。 お使いのアプリによって、ユーザーがサインインされ、Microsoft Graph API を呼び出すためのアクセス トークンが取得されて、Microsoft Graph API への要求が行われます。 

このガイドを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>前提条件

このサンプルを実行するには、次のものが必要になります。 
- Java Development Kit (JDK) 8 以降および Maven。

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする
> クイックスタート アプリケーションを開始する方法としては、[簡易] (オプション 1) と [手動] (オプション 2) の 2 つの選択肢があります。
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1:アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. [Azure portal の [アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) に移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 指示に従って新しいアプリケーションをダウンロードし、自動構成します。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2:アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
> 
>
> #### <a name="step-1-download-the-code-sample"></a>手順 1:コード サンプルのダウンロード
> 
> - [コード サンプルのダウンロード](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>手順 2:application.properties のオープン
>
> 1. ZIP ファイルをローカル フォルダーに展開します。
> 1. (オプション) 統合開発環境を使用する場合は、その IDE でサンプルを開きます。
> 1. *application.properties* ファイルを開きます。 次の手順でアプリケーションを登録するときに、`aad.clientId`、`aad.authority`、および `aad.secretKey` の値を挿入します。


> #### <a name="step-3-register-your-application"></a>手順 3:アプリケーションの登録
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
> 1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
> 1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
> 1. **[新規登録]** を選択します。
> 1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
>    - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `java-webapp`)。
>    - ここでは **[リダイレクト URI]** は空白のままにして、 **[登録]** を選択します。
> 1. アプリケーションの**アプリケーション (クライアント) ID** の値を見つけます。 *application.properties* ファイル内の `Enter_the_Application_Id_here` の値を更新します。
> 1. アプリケーションの**ディレクトリ (テナント) ID** の値を見つけます。 *application.properties* ファイル内の `Enter_the_Tenant_Info_Here` の値を更新します。 
> 1. **[認証]** メニューを選択し、次の情報を追加します。
>    - **[リダイレクト URI]** で `http://localhost:8080/msal4jsamples/secure/aad` と `https://localhost:8080/msal4jsamples/graph/users` を追加します。
>    - **[保存]** を選択します。
> 1. 左側のメニューで **[証明書とシークレット]** を選択し、 **[クライアント シークレット]** セクションで **[新しいクライアント シークレット]** をクリックします。
>     
>    - キーの説明 (インスタンス アプリ シークレットの) を入力します。
>    - キーの有効期間として **[1 年]** を選択します。
>    - **[追加]** をクリックすると、キーの値が表示されます。 
>    - キーの値をコピーします。 前にダウンロードした *application.properties* ファイルを開き、`Enter_the_Client_Secret_Here` の値をキーの値で更新します。 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタートのコード サンプルを動作させるには、以下の操作が必要です。
> 1. 応答 URL として `http://localhost:8080/msal4jsamples/secure/aad` および `https://localhost:8080/msal4jsamples/graph/users` を追加します。
> 1. クライアント シークレットを作成します。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこれらの属性で構成されています。
> 
> #### <a name="step-2-download-the-code-sample"></a>手順 2:コード サンプルのダウンロード
> 
> - [コード サンプルのダウンロード](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>手順 3:コード サンプルの構成 
> 
> 1. ZIP ファイルをローカル フォルダーに展開します。
> 1. 統合開発環境を使用する場合は、その IDE でサンプルを開きます (オプション)。
> 1. **application.properties** ファイルを開きます。このファイルは、*src/main/resources/* にあります。
> 1. アプリケーションのプロパティを置き換えます。
>   1. `aad.clientId` を探し、`Enter_the_Application_Id_here` の値を、登録済みのアプリケーションの**アプリケーション (クライアント) ID** 値で更新します。 
>   1. `aad.authority` を探し、`Enter_the_Tenant_Name_Here` の値を、登録済みのアプリケーションの**ディレクトリ (テナント) ID** 値で更新します。
>   1. `aad.secretKey` を探し、`Enter_the_Client_Secret_Here` の値を、登録済みアプリケーションの **[証明書とシークレット]** で作成した**クライアント シークレット**で更新します。

#### <a name="step-4-run-the-code-sample"></a>手順 4:コード サンプルの実行
1. コード サンプルを実行し、ブラウザーを開いて、 *http://localhost:8080* に移動します。
1. フロント ページには、**サインイン** ボタンが含まれています。 **サインイン** ボタンをクリックして、Azure Active Directory にリダイレクトします。 ユーザーが、資格情報の入力を求められます。  
1. Azure Active Directory で正常に認証されると、 *http://localhost:8080/msal4jsamples/secure/aad* にリダイレクトされます。 これらのユーザーはアプリケーションに正式にサインインし、ページにはサインインしたアカウントの情報が表示されます。 また、以下の操作のためのボタンも含まれています。 
    - "*サインアウト*": 現在のユーザーをアプリケーションからサインアウトし、ホーム ページにリダイレクトします。
    - "*ユーザーの表示*": Microsoft Graph のトークンを取得してから、トークンを要求にアタッチして Microsoft Graph を呼び出し、テナント内のすべてのユーザーを取得します。


## <a name="more-information"></a>詳細情報

### <a name="getting-msal"></a>MSAL の取得
MSAL4J は、ユーザーをサインインさせるために使用されたり、Microsoft ID プラットフォームによって保護されている API にアクセスするためのトークンを要求するために使用されたりするライブラリです。 Maven または Gradle を使用して、アプリケーションに MSAL4J を追加し、アプリケーションの pom.xml または build.gradle ファイルに対して以下の変更を行うことで、依存関係を管理できます。 

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Msal の初期化
MSAL4J を使用するファイルの先頭に次のコードを追加すると、MSAL4J への参照を追加できます。 

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>次の手順

アクセス許可と同意について学習します。

> [!div class="nextstepaction"]
> [アクセス許可と同意](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

このシナリオ用の認証フローの詳細については、OAuth 2.0 承認コード フローを参照してください。

> [!div class="nextstepaction"]
> [承認コードの Oauth フロー](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Microsoft ID プラットフォームの改善にご協力ください。 簡単な 2 つの質問からなるアンケートに記入し、ご意見をお聞かせください。

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
