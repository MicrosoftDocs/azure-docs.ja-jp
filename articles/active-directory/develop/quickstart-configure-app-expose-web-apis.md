---
title: クイック スタート:Web API の登録と公開 | Azure
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Microsoft ID プラットフォームに Web API を登録し、そのスコープを構成して、API のリソースに対するアクセス許可ベースのアクセス権のためにクライアントに公開します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 820bc7dfe9123db495c151cd5cd0ea5ae337619f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103976"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>クイック スタート:Web API を公開するようにアプリケーションを構成する

このクイックスタートでは、Microsoft ID プラットフォームに Web API を登録し、スコープの例を追加してそれをクライアント アプリに公開します。 Web API を登録し、スコープを介して公開することで、リソースに対するアクセス許可ベースのアクセス権を、API にアクセスする承認済みのユーザーおよびクライアント アプリに付与できます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションを持つ Azure アカウント - [アカウントを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 次の項目の完了: 「[クイックスタート: テナントを設定する](quickstart-create-new-tenant.md)」

## <a name="register-the-web-api"></a>Web API を登録する

Web API 内のリソースへのスコープ付きアクセス権を付与するには、まず API を Microsoft ID プラットフォームに登録する必要があります。

1. 「**アプリケーションを登録する**」(「[クイックスタート: Microsoft ID プラットフォームにアプリを登録する](quickstart-register-app.md)」) の手順を行います。
1. **リダイレクト URI の追加** と **プラットフォーム設定の構成** に関するセクションはスキップします。 ユーザーは対話的にログインしないため、Web API のリダイレクト URI を構成する必要はありません。
1. ここでは、**資格情報の追加** のセクションをスキップします。 API からダウンストリームにアクセスする場合にのみ、独自の資格情報が必要になりますが、この記事では取り上げません。

Web API を登録すると、スコープを追加する準備は完了です。これを API のコードに使って、API のコンシューマーにきめ細かなアクセス許可を付与することができます。

## <a name="add-a-scope"></a>スコープを追加する

クライアント アプリケーションのコードによって、保護されたリソース (Web API) への要求と共にアクセス トークンを渡すことにより、Web API で定義された操作を実行するアクセス許可が要求されます。 Web API では、操作に必要なスコープが受け取ったアクセス トークンに含まれている場合にのみ、要求された操作が実行されます。

まず、次の手順で `Employees.Read.All` という名前のスコープの例を作成します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、上部のメニューの **[ディレクトリ + サブスクリプション]** フィルター :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: を使用して、クライアント アプリの登録を含むテナントを選択します。
1. **[Azure Active Directory]**  >  **[アプリの登録]** を選択し、API のアプリの登録を選択します。
1. **[API の公開]**  >  **[スコープの追加]** を選択します。

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Azure portal のアプリ登録の [API の公開] ペイン":::

1. まだ構成していない場合は、 **[アプリケーション ID URI]** を設定するように求められます。

   アプリ ID URI は、API のコードで参照するスコープのプレフィックスとして機能し、グローバルに一意である必要があります。 指定されている既定値 (`api://<application-client-id>` 形式) を使用するか、`https://contoso.com/api` のようにより読みやすい URI を指定できます。

1. 次に、 **[スコープの追加]** ペインでスコープの属性を指定します。 このチュートリアルでは、例の値を使用するか、独自の値を指定できます。

    | フィールド | 説明 | 例 |
    |-------|-------------|---------|
    | **スコープ名** | スコープの名前。 一般的なスコープの名前付け規則は `resource.operation.constraint` です。 | `Employees.Read.All` |
    | **同意できるユーザー** | このスコープにユーザーが同意できるかどうかと、管理者の同意が必要かどうか。 より高い特権のアクセス許可にするには、 **[管理者のみ]** を選択します。 | **管理者とユーザー** |
    | **管理者の同意の表示名** | 管理者のみに表示される、スコープの目的についての簡単な説明。 | `Read-only access to Employee records` |
    | **管理者の同意の説明** | 管理者のみに表示される、スコープによって付与されるアクセス許可の詳細な説明。 | `Allow the application to have read-only access to all Employee data.` |
    | **ユーザーの同意の表示名** | スコープの目的に関する簡単な説明。 **[同意できるユーザー]** を **[管理者とユーザー]** に設定した場合にのみユーザーに表示されます。 | `Read-only access to your Employee records` |
    | **ユーザーの同意の説明** | スコープによって付与されるアクセス許可の詳細な説明。 **[同意できるユーザー]** を **[管理者とユーザー]** に設定した場合にのみユーザーに表示されます。 | `Allow the application to have read-only access to your Employee data.` |

1. **[状態]** を **[有効]** に設定し、 **[スコープの追加]** を選択します。

1. (省略可能) 定義されているスコープに対してアプリのユーザーによる同意を求めるメッセージを表示しないようにするには、クライアント アプリケーションによる Web API へのアクセスを "*事前承認*" することができます。 ユーザーには同意を拒否する機会がないため、信頼できるクライアント アプリケーション "*だけ*" を事前承認します。
    1. **[承認済みのクライアント アプリケーション]** で、 **[クライアント アプリケーションの追加]** を選択します
    1. 事前承認するクライアント アプリケーションの **[アプリケーション (クライアント) ID]** を入力します。 たとえば、以前に登録した Web アプリケーションのそれです。
    1. **[承認済みのスコープ]** で、同意を求めるメッセージを表示しないスコープを選択し、 **[アプリケーションの追加]** を選択します。

    この省略可能な手順を行った場合、クライアント アプリは承認済みのクライアント アプリ (PCA) になり、ユーザーはアプリにサインインするときに同意を求められません。

## <a name="add-a-scope-requiring-admin-consent"></a>管理者の同意が必要なスコープを追加する

次に、管理者だけが同意できる `Employees.Write.All` という名前の別のスコープの例を追加します。 通常、管理者の同意が必要なスコープは、より高い特権の操作に対するアクセス権を付与するために使用され、多くの場合、ユーザーが対話的にサインインしないバックエンド サービスまたはデーモンとして実行されるクライアント アプリケーションに使用されます。

`Employees.Write.All` のスコープの例を追加するには、「[スコープの追加](#add-a-scope)」セクションの手順を行い、 **[スコープの追加]** ペインでこれらの値を指定します。

| フィールド                          | 値の例                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **スコープ名**                 | `Employees.Write.All`                                              |
| **同意できるユーザー**            | **管理者のみ**                                                    |
| **管理者の同意の表示名** | `Write access to Employee records`                                 |
| **管理者の同意の説明**  | `Allow the application to have write access to all Employee data.` |
| **ユーザーの同意の表示名**  | *なし (空のまま)*                                               |
| **ユーザーの同意の説明**   | *なし (空のまま)*                                               |

## <a name="verify-the-exposed-scopes"></a>公開されたスコープを確認する

前のセクションで説明した両方のスコープの例を追加すると、次の画像のように、Web API のアプリ登録の **[API の公開]** ペインに表示されます。

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="2 つの公開されたスコープを示す [API の公開] ペインのスクリーンショット。":::

この画像に示すように、スコープの完全な文字列は、Web API の **[アプリケーション ID URI]** とスコープの **[スコープ名]** を連結したものです。

たとえば、Web API のアプリケーション ID URI が `https://contoso.com/api` で、スコープ名が `Employees.Read.All` である場合、完全なスコープは次のようになります。

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>公開されたスコープを使用する

このシリーズの次の記事では、この記事の手順に従って定義した Web API へのアクセスとスコープを使用して、クライアント アプリの登録を構成します。

クライアント アプリの登録に Web API へのアクセス許可が付与されると、Microsoft ID プラットフォームによってクライアントに OAuth 2.0 アクセス トークンが発行されます。 クライアントから Web API を呼び出すと、アクセス トークンが表示されます。そのスコープ (`scp`) 要求は、クライアントのアプリ登録で指定したアクセス許可に設定されています。

公開するスコープは、必要に応じて後から追加することもできます。 Web API を使用すると、複数の操作に関連付けられた複数のスコープを公開できることを考慮してください。 リソースは、受け取った OAuth 2.0 アクセス トークンのスコープ (`scp`) 要求を評価することによって、実行時に Web API へのアクセスを制御します。

## <a name="next-steps"></a>次のステップ

スコープを構成して Web API を公開したので、これらのスコープにアクセスするためのアクセス許可でクライアント アプリの登録を構成します。

> [!div class="nextstepaction"]
> [Web API アクセスのためにアプリ登録を構成する](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
