---
title: Web API を公開するようにアプリを構成する - Microsoft ID プラットフォーム | Azure
description: アプリケーションをクライアント アプリケーションが使用できるようにするために、新しいアクセス許可/スコープとロールを公開するようにアプリケーションを構成する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/14/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: a45b3ee9bf9cea71255ac9a83f970d2b6c72b67f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966114"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis"></a>クイック スタート:Web API を公開するようにアプリケーションを構成する

自ら Web API を開発し、クライアント アプリケーションで利用できるようにすることもできます。そのためには、[アクセス許可/スコープ](developer-glossary.md#scopes)と[ロール](developer-glossary.md#roles)を公開する必要があります。 Web API を適切に構成すれば、Graph API や Office 365 API など、他の Microsoft Web API と同じように利用できるようになります。

このクイック スタートでは、クライアント アプリケーションが使用できるようにするために、新しいスコープを公開するようにアプリケーションを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

最初に、以下の前提条件を完了していることを確認します。

* サポートされている[アクセス許可と同意](v2-permissions-and-consent.md)について学習する。他のユーザーまたはアプリケーションによって使用されるアプリケーションを構築する場合は、これを理解しておくことが重要です。
* アプリケーションが登録されているテナントを持つ。
  * アプリを登録していない場合は、[Microsoft ID プラットフォームを使用してアプリケーションを登録する方法について学習](quickstart-register-app.md)します。

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

1. [他のアプリケーションに Web API が公開されていることを確認する](#verify-the-web-api-is-exposed-to-other-applications)ための手順を実行します。

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>アプリケーション マニフェストを通じて新しいスコープまたはロールを公開する

[![マニフェストで oauth2Permissions コレクションを使用して新しいスコープを公開する](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

アプリケーション マニフェストを通じて新しいスコープを公開するには:

1. アプリの **[概要]** ページで、 **[マニフェスト]** セクションを選択します。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを**編集**できます。 必要があれば、 **[ダウンロード]** を選択してローカルでマニフェストを編集します。その後、 **[アップロード]** を使用して、アプリケーションにマニフェストを再適用します。
    
    次の例では、`oauth2Permissions` コレクションに以下の JSON 要素を追加して、リソース/API の `Employees.Read.All` という新しいスコープを公開する方法を示しています。

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

   > [!NOTE]
   > `id` の値は、プログラムによって、または [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) などの GUID 生成ツールを使って作成する必要があります。 `id` は、Web API によって公開されるスコープの一意の識別子となります。 クライアントに対して Web API にアクセスするためのアクセス許可が適切に構成されていると、そのクライアントには、Azure AD によって OAuth 2.0 アクセス トークンが発行されます。 クライアントが Web API を呼び出すときには、このアクセス トークンを提示することになります。そして、このアクセス トークンでは、スコープ (scp) 要求がアプリケーションの登録時に要求されたアクセス許可のとおりに設定されています。
   >
   > 公開するスコープは、必要に応じて後から追加することもできます。 たとえば、Web API で、さまざまな機能が関連付けられたスコープをいくつも公開しているとします。 リソースは、受け取った OAuth 2.0 アクセス トークンのスコープ (`scp`) 要求を評価することによって、実行時に Web API へのアクセスを制御します。

1. 完了したら、 **[保存]** をクリックします。 これで Web API の構成が終わり、ディレクトリ内の他のアプリケーションが利用できるようになりました。
1. [他のアプリケーションに Web API が公開されていることを確認する](#verify-the-web-api-is-exposed-to-other-applications)ための手順を実行します。

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>他のアプリケーションに Web API が公開されているかどうかを確認する

1. Azure AD テナントに戻って、 **[アプリの登録]** を選択し、構成するクライアント アプリケーションを見つけて選択します。
1. [Web API にアクセスするためのクライアント アプリケーションの構成](quickstart-configure-app-access-web-apis.md)に関するページで説明されている手順をもう一度実行します。
1. [API の選択](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis
)の手順で、リソースを選択します。 新しいスコープが表示されていれば、クライアントのアクセス許可の要求で利用できる状態です。

## <a name="more-on-the-application-manifest"></a>アプリケーション マニフェストの詳細

アプリケーション マニフェストは、アプリケーション エンティティを更新するためのメカニズムとしての役割を果たすものです。Azure AD アプリケーションの ID 構成の属性はすべて、このアプリケーション エンティティで定義されています。 アプリケーション エンティティとそのスキーマの詳細については、[Graph API のアプリケーション エンティティに関するドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)を参照してください。 このドキュメントでは、API のアクセス許可を指定するために使用するアプリケーション エンティティのメンバーについて、詳細な参照情報を確認できます。たとえば、次のような情報です。  

* appRoles メンバー: Web API の[アプリケーションのアクセス許可](developer-glossary.md#permissions)を定義するときに使用する [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) エンティティのコレクションです。
* oauth2Permissions メンバー: Web API の[委任されたアクセス許可](developer-glossary.md#permissions)を定義するときに使用する [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) エンティティのコレクションです。

アプリケーション マニフェストの概念一般の詳細については、[Azure Active Directory のアプリケーション マニフェスト](reference-app-manifest.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

以下のその他のアプリ管理関連のクイック スタートを学習します。

* [Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)
* [Web API にアクセスするようにクライアント アプリケーションを構成する](quickstart-configure-app-access-web-apis.md)
* [アプリケーションによってサポートされるアカウントを変更する](quickstart-modify-supported-accounts.md)
* [Microsoft ID プラットフォームに登録されたアプリケーションを削除する](quickstart-remove-app.md)

登録されたアプリケーションを表す 2 つの Azure AD オブジェクトと、両者間の関係については、[Application objects and service principal objects](app-objects-and-service-principals.md)\(アプリケーション オブジェクトとサービス プリンシパル オブジェクト\) を参照してください。

Azure Active Directory でアプリケーションを開発するときに使用するブランド化ガイドラインについては、[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)を参照してください。
