---
title: "ポータルでの Azure アプリ ID の作成 | Microsoft Docs"
description: "Azure Resource Manager でロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しい Azure Active Directory のアプリケーションとサービス プリンシパルを作成する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 5d24fb99e1095d53e5ea547e53b80178d9cb77c0
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する

アプリケーションでリソースにアクセスしたり変更を加えたりするには、Azure Active Directory (AD) アプリケーションをセットアップして、そこに必要な権限を割り当てる必要があります。 この方法は、お客様自身の資格情報でアプリを実行するよりも推奨されます。

* お客様自身のアクセス許可とは異なるアクセス許可を、アプリ ID に割り当てることができます。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。
* お客様の責任が変わっても、アプリの資格情報を変更する必要はありません。 
* 無人インストール用スクリプトを実行するときに、証明書を使用して認証を自動化できます。

このトピックでは、それらの手順をポータルで行う方法について説明します。 ここでは、シングル テナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。 一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。
 
## <a name="required-permissions"></a>必要なアクセス許可
このトピックの手順を実行するには、アプリケーションを Azure AD テナントに登録し、Azure サブスクリプションでアプリケーションをロールに割り当てるための十分なアクセス許可が必要です。 これらの手順を実行するための適切なアクセス許可があることを確認しましょう。

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory のアクセス許可を確認する
1. [Azure Portal](https://portal.azure.com) で Azure アカウントにログインします。
2. **[Azure Active Directory]**を選択します。

     ![[Azure Active Directory] を選択する](./media/resource-group-create-service-principal-portal/select-active-directory.png)
3. Azure Active Directory で **[ユーザー設定]** を選択します。

     ![[ユーザー設定] を選択する](./media/resource-group-create-service-principal-portal/select-user-settings.png)
4. **[アプリの登録]** 設定を確認します。 **[はい]** に設定されている場合は、管理者以外のユーザーが AD アプリを登録できます。 この設定は、Azure AD テナント内のすべてのユーザーがアプリを登録できることを意味します。 この場合、「[Azure サブスクリプションのアクセス許可を確認する](#check-azure-subscription-permissions)」に進んで構いません。

     ![[アプリの登録] を表示する](./media/resource-group-create-service-principal-portal/view-app-registrations.png)
5. [アプリの登録] 設定が **[いいえ]** に設定されている場合は、管理者ユーザーだけがアプリを登録できます。 アカウントが Azure AD テナントの管理者かどうかを確認する必要があります。 **[概要]** を選択し、[クイック タスク] の **[ユーザーを検索する]** を選択します。

     ![ユーザーを検索する](./media/resource-group-create-service-principal-portal/find-user.png)
6. アカウントを検索し、アカウントが見つかったら選択します。

     ![ユーザーを検索する](./media/resource-group-create-service-principal-portal/show-user.png)
7. アカウントの **[ディレクトリ ロール]** を選択します。 

     ![ディレクトリ ロール](./media/resource-group-create-service-principal-portal/select-directory-role.png)
8. Azure AD で割り当て済みのディレクトリ ロールを表示します。 アカウントがユーザー ロールに割り当てられていても、(前の手順の) アプリの登録設定が管理者ユーザーに制限されている場合は、管理者に連絡して、管理者ロールに割り当ててもらうか、ユーザーがアプリを登録できるようにしてもらいます。

     ![ロールを表示する](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Azure サブスクリプションのアクセス許可を確認する
Azure サブスクリプションで、AD アプリをロールに割り当てるには、アカウントに `Microsoft.Authorization/*/Write` アクセス権が必要です。 このアクションは、[所有者](../active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](../active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて許可されます。 アカウントが**共同作成者**ロールに割り当てられている場合は、適切なアクセス許可がありません。 この場合、サービス プリンシパルをロールに割り当てようとすると、エラーが発生します。 

サブスクリプションのアクセス許可を確認するには、次の手順に従います。

1. 前の手順で Azure AD アカウントをまだ確認していない場合は、左側のウィンドウで **[Azure Active Directory]** を選択します。

2. Azure AD アカウントを検索します。 **[概要]** を選択し、[クイック タスク] の **[ユーザーを検索する]** を選択します。

     ![ユーザーを検索する](./media/resource-group-create-service-principal-portal/find-user.png)
2. アカウントを検索し、アカウントが見つかったら選択します。

     ![ユーザーを検索する](./media/resource-group-create-service-principal-portal/show-user.png) 
     
3. **[Azure リソース]** を選択します。

     ![リソースを選択する](./media/resource-group-create-service-principal-portal/select-azure-resources.png) 
3. 割り当て済みのロールを表示し、AD アプリをロールに割り当てるための適切なアクセス許可があるかどうかを確認します。 ない場合は、サブスクリプション管理者に連絡して、ユーザー アクセス管理者ロールに追加してもらいます。 次の図では、ユーザーは 2 つのサブスクリプションの所有者ロールに割り当てられているので、このユーザーには適切なアクセス許可があります。 

     ![アクセス許可を表示する](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する
1. [Azure Portal](https://portal.azure.com) で Azure アカウントにログインします。
2. **[Azure Active Directory]**を選択します。

     ![[Azure Active Directory] を選択する](./media/resource-group-create-service-principal-portal/select-active-directory.png)

4. **[アプリの登録]** を選択します。   

     ![[アプリの登録] を選択する](./media/resource-group-create-service-principal-portal/select-app-registrations.png)
5. **[追加]**を選択します。

     ![アプリを追加する](./media/resource-group-create-service-principal-portal/select-add-app.png)

6. アプリケーションの名前と URL を指定します。 作成するアプリケーションの種類として、**[Web アプリ/API]** または **[ネイティブ]** を選択します。 値を設定したら、**[作成]** をクリックします。

     ![アプリケーションの名前指定](./media/resource-group-create-service-principal-portal/create-app.png)

これでアプリケーションが作成されます。

## <a name="get-application-id-and-authentication-key"></a>アプリケーション ID と認証キーを取得する
プログラムによってログインするときは、アプリケーションの ID と認証キーが必要です。 これらの値を取得するには、次の手順に従います。

1. Azure Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

     ![アプリケーションを選択する](./media/resource-group-create-service-principal-portal/select-app.png)
2. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。 「[サンプル アプリケーション](#sample-applications)」の各アプリケーションでは、この値をクライアント ID と呼んでいます。

     ![[クライアント ID]](./media/resource-group-create-service-principal-portal/copy-app-id.png)
3. 認証キーを生成するには、**[キー]** を選択します。

     ![[キー] を選択する](./media/resource-group-create-service-principal-portal/select-keys.png)
4. キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。

     ![キーを保存する](./media/resource-group-create-service-principal-portal/save-key.png)

     キーを保存すると、キーの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。 キー値は、アプリケーションとしてログインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

     ![保存されたキー](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>テナント ID を取得する
プログラムによってログインするときは、認証要求と共にテナント ID を渡す必要があります。 

1. テナント ID を取得するには、Azure AD テナントの **[プロパティ]** を選択します。 

     ![Azure AD のプロパティを選択する](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

2. **ディレクトリ ID** をコピーします。 この値がテナント ID です。

     ![テナント ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>アプリケーションをロールに割り当てる
サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1. アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。 リソース グループまたはリソースを選択することもできます。

     ![サブスクリプションを選択する](./media/resource-group-create-service-principal-portal/select-subscription.png)

2. アプリケーションを割り当てる特定のサブスクリプション (リソース グループまたはリソース) を選択します。

     ![割り当てのためのサブスクリプションの選択](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

3. **[アクセス制御 (IAM)]** を選択します。

     ![アクセスの選択](./media/resource-group-create-service-principal-portal/select-access-control.png)

4. **[追加]**を選択します。

     ![select add](./media/resource-group-create-service-principal-portal/select-add.png)
6. アプリケーションに割り当てるロールを選択します。 次の図は、**閲覧者**ロールを示しています。

     ![select role](./media/resource-group-create-service-principal-portal/select-role.png)

8. アプリケーションを検索して選択します。

     ![アプリを検索する](./media/resource-group-create-service-principal-portal/search-app.png)
9. **[OK]** をクリックして、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

## <a name="log-in-as-the-application"></a>アプリケーションとしてログイン

Azure Active Directory でアプリケーションがセットアップされました。 アプリケーションとしてサインインする際に使用する ID とキーも用意しました。 アプリケーションは、実行できる特定のアクションを提供するロールに割り当てられています。 さまざまなプラットフォームからアプリケーションとしてログインする方法については、次のリンクをクリックしてください。

* [PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell)
* [Azure CLI](resource-group-authenticate-service-principal-cli.md#provide-credentials-through-azure-cli)
* [REST ()](/rest/api/#create-the-request)
* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.JS](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>次のステップ
* マルチテナント アプリケーションのセットアップについては、「 [Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* セキュリティ ポリシーを指定する方法については、「[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」を参照してください。  
* ユーザーに対して許可または拒否される場合がある使用可能なアクションの一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../active-directory/role-based-access-control-resource-provider-operations.md)」を参照してください。

