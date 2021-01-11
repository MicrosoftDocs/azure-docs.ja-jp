---
title: クイック スタート:Web API を公開するようにアプリを構成する | Azure
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、アプリケーションをクライアント アプリケーションが使用できるようにするために、新しいアクセス許可およびスコープとロールを公開するようにアプリケーションを構成する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830293"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>クイック スタート:Web API を公開するようにアプリケーションを構成する

自ら Web API を開発し、クライアント アプリケーションで利用できるようにすることもできます。そのためには、[アクセス許可/スコープ](developer-glossary.md#scopes)と[ロール](developer-glossary.md#roles)を公開する必要があります。 Web API を適切に構成すれば、Graph API や Office 365 API など、他の Microsoft Web API と同じように利用できるようになります。

このクイックスタートでは、クライアント アプリケーションが使用できるようにするために、新しいスコープを公開するようにアプリケーションを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 次の項目の完了: 「[クイックスタート: Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)」を参照してください。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure portal にサインインしてアプリを選択する

アプリを構成する前に、次の手順を実行します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスを選択し、 **[アプリの登録]** を選択します。
1. 構成するアプリケーションを探して選択します。 アプリを選択すると、アプリケーションの **[概要]** またはメイン登録ページが表示されます。
1. 新しいスコープを公開するために、UI とアプリケーション マニフェストのどちらの方法を使用するかを選択します。
    * [UI を通じて新しいスコープを公開する](#expose-a-new-scope-through-the-ui)
    * [アプリケーション マニフェストを通じて新しいスコープまたはロールを公開する](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>UI を通じて新しいスコープを公開する

[![UI を使用して API を公開する方法の画像](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

UI を通じて新しいスコープを公開するには:

1. アプリの **[概要]** ページで、 **[API の公開]** セクションを選択します。

1. **[Scope の追加]** を選択します。

1. **[アプリケーション ID URI]** を設定していない場合は、入力を求めるメッセージが表示されます。 アプリケーション ID URI を入力するか、提供されるものの 1 つを使用し、 **[保存してから続ける]** を選択します。

1. **[Scope の追加]** ページが表示されたら、以下のスコープの情報を入力します。

    | フィールド | 説明 |
    |-------|-------------|
    | **スコープ名** | スコープのわかりやすい名前を入力します。<br><br>たとえば、「 `Employees.Read.All` 」のように入力します。 |
    | **同意できるユーザー** | このスコープにユーザーが同意できるかどうかと、管理者の同意が必要かどうかを選択します。 より高い特権のアクセス許可にするには、 **[管理者のみ]** を選択します。 |
    | **管理者の同意の表示名** | スコープのわかりやすい説明を入力します。説明は管理者に表示されます。<br><br>たとえば、`Read-only access to Employee records` のように指定します。 |
    | **管理者の同意の説明** | スコープのわかりやすい説明を入力します。説明は管理者に表示されます。<br><br>たとえば、`Allow the application to have read-only access to all Employee data.` のように指定します。 |

    ユーザーがスコープに同意できる場合は、以下のフィールドにも値を追加します。

    | フィールド | 説明 |
    |-------|-------------|
    | **ユーザーの同意の表示名** | スコープのわかりやすい名前を入力します。名前はユーザーに表示されます。<br><br>たとえば、`Read-only access to your Employee records` のように指定します。 |
    | **ユーザーの同意の説明** | スコープのわかりやすい説明を入力します。説明はユーザーに表示されます。<br><br>たとえば、`Allow the application to have read-only access to your Employee data.` のように指定します。 |

1. 完了したら、 **[状態]** を設定し、 **[スコープの追加]** を選択します。

1. (省略可能) 定義されているスコープに対してアプリのユーザーによる同意を求めるメッセージを表示しないようにするには、クライアント アプリケーションによる Web API へのアクセスを "事前承認" することができます。 ユーザーには同意を拒否する機会がないため、信頼できるクライアント アプリケーション "*だけ*" を事前承認する必要があります。
    1. **[承認済みのクライアント アプリケーション]** で、 **[クライアント アプリケーションの追加]** を選択します
    1. 事前承認するクライアント アプリケーションの **[アプリケーション (クライアント) ID]** を入力します。 たとえば、以前に登録した Web アプリケーションのそれです。
    1. **[承認済みのスコープ]** で、同意を求めるメッセージを表示しないスコープを選択し、 **[アプリケーションの追加]** を選択します。

    クライアント アプリが事前承認されたクライアント アプリ (PCA) になり、ユーザーにはサインインするときの同意を求めるメッセージが表示されなくなります。

1. [他のアプリケーションに Web API が公開されていることを確認する](#verify-the-web-api-is-exposed-to-other-applications)ための手順を実行します。

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>アプリケーション マニフェストを通じて新しいスコープまたはロールを公開する

アプリケーション マニフェストは、Azure AD アプリ登録の属性を定義するアプリケーション エンティティを更新するためのメカニズムとしての役割を果たします。

[![マニフェストで oauth2Permissions コレクションを使用して新しいスコープを公開する](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

アプリケーション マニフェストを編集して新しいスコープを公開するには、次のようにします。

1. アプリの **[概要]** ページで、 **[マニフェスト]** セクションを選択します。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを**編集**できます。 必要があれば、 **[ダウンロード]** を選択してローカルでマニフェストを編集します。その後、 **[アップロード]** を使用して、アプリケーションにマニフェストを再適用します。

    次の例では、`oauth2Permissions` コレクションに以下の JSON 要素を追加して、リソース/API の `Employees.Read.All` という新しいスコープを公開する方法を示しています。

    プログラムによって、または [guidgen](https://www.microsoft.com/download/details.aspx?id=55984) のような GUID 生成ツールを使用して、`id` 値を生成します。

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. 完了したら、 **[保存]** をクリックします。 これで Web API の構成が終わり、ディレクトリ内の他のアプリケーションが利用できるようになりました。
1. [他のアプリケーションに Web API が公開されていることを確認する](#verify-the-web-api-is-exposed-to-other-applications)ための手順を実行します。

アプリケーション エンティティおよびそのスキーマの詳細については、Microsoft Graph の [Application][ms-graph-application] リソース タイプのリファレンス ドキュメントを参照してください。

アプリケーション マニフェストの詳細とそのスキーマ リファレンスについては、[Azure AD アプリ マニフェストの概要](reference-app-manifest.md)に関するページを参照してください。

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>他のアプリケーションに Web API が公開されているかどうかを確認する

1. Azure AD テナントに戻り、 **[アプリの登録]** を選択してから、構成するクライアント アプリケーションを見つけて選択します。
1. [Web API にアクセスするためのクライアント アプリケーションの構成](quickstart-configure-app-access-web-apis.md)に関するページで説明されている手順をもう一度実行します。
1. [API を選択する](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)ステップになったら、リソース (Web API アプリの登録) を選択します。
    * Azure portal を使用して Web API アプリの登録を作成した場合、その API リソースは **[自分の API]** タブの一覧に表示されます。
    * プロジェクトの作成時に Visual Studio による Web API アプリの登録の作成を許可した場合は、 **[所属する組織で使用している API]** タブの一覧に API リソースが表示されます。

Web API リソースを選択すると、クライアントのアクセス許可の要求で利用できる新しいスコープが表示されます。

## <a name="using-the-exposed-scopes"></a>公開されたスコープを使用する

クライアントに対して Web API にアクセスするためのアクセス許可が適切に構成されていると、そのクライアントには Azure AD によって OAuth 2.0 アクセス トークンが発行できます。 クライアントが Web API を呼び出すときには、このアクセス トークンを提示することになります。そして、このアクセス トークンでは、スコープ (`scp`) 要求がアプリケーションの登録時に要求されたアクセス許可のとおりに設定されています。

公開するスコープは、必要に応じて後から追加することもできます。 たとえば、Web API で、さまざまな機能が関連付けられたスコープをいくつも公開しているとします。 リソースは、受け取った OAuth 2.0 アクセス トークンのスコープ (`scp`) 要求を評価することによって、実行時に Web API へのアクセスを制御します。

アプリケーションでは、完全なスコープ値は Web API の**アプリケーション ID URI** (リソース) と**スコープ名**を連結したものです。

たとえば、Web API のアプリケーション ID URI が `https://contoso.com/api` で、スコープ名が `Employees.Read.All` である場合、完全なスコープは次のようになります。

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>次のステップ

スコープを構成して Web API を公開したので、これらのスコープにアクセスするためのアクセス許可でクライアント アプリの登録を構成します。

> [!div class="nextstepaction"]
> [Web API アクセスのためにアプリ登録を構成する](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
