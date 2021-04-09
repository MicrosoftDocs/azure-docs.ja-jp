---
title: セルフサービス サインアップ フローにカスタム属性を追加する - Azure AD
description: セルフサービス サインアップのユーザー フローの属性をカスタマイズする方法について説明します。
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b498f8b8512d0202f47dd31ba25cc851ca71e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644104"
---
# <a name="define-custom-attributes-for-user-flows"></a>ユーザー フローのカスタム属性を定義する

アプリケーションごとに、サインアップ中に収集する情報の要件が異なる場合があります。 Azure AD には、名、姓、市区町村、郵便番号など、属性に格納された一連の情報が組み込みで用意されています。 Azure AD では、外部ユーザーがユーザー フローを通じてサインアップするときにゲスト アカウントに格納される一連の属性を拡張できます。

Azure portal でカスタム属性を作成し、セルフサービス サインアップのユーザー フローでそれらを使用できます。 また、[Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md) を使用してこれらの属性を読み書きすることもできます。 Microsoft Graph API では、拡張属性を使用したユーザーの作成と更新がサポートされています。 Graph API の拡張属性には、`extension_<extensions-app-id>_attributename` という規則を使って名前が付けられます。 次に例を示します。

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<extensions-app-id>` は、ご自身のテナントに固有です。 この識別子を調べるには、[Azure Active Directory] > [アプリの登録] > [すべてのアプリケーション] の順に移動します。 "aad-extensions-app" で開始されるアプリを検索して、それを選択します。 アプリの [概要] ページの [アプリケーション (クライアント) ID] を書き留めます。

## <a name="create-a-custom-attribute"></a>カスタム属性を作成する

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[カスタムのユーザー属性]** を選択します。 使用可能なユーザー属性が一覧表示されます。

   ![サインアップ用のユーザー属性の選択](media/user-flow-add-custom-attributes/user-attributes.png)

5. 属性を追加するには、 **[追加]** を選択します。
6. **[属性の追加]** ペインで、次の値を入力します。

   - **[名前]** - カスタム属性の名前を指定します ("ShoeSize" など)。
   - **[データ型]** - データ型を選択します ( **[String]** 、 **[Boolean]** 、または **[Int]** )。
   - **[説明]** - 必要に応じて、内部使用のためにカスタム属性の説明を入力します。 この説明は、ユーザーには表示されません。

   ![属性の追加](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. **［作成］** を選択します

これで、このカスタム属性がユーザー属性の一覧やユーザー フローで使用できるようになります。 カスタム属性が作成されるのは、いずれかのユーザー フローで初めて使用されるときだけであり、ユーザー属性の一覧に追加するときではありません。

新しく作成したカスタム属性を使用するユーザー フローを使用して新しいユーザーを作成したら、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) でそのオブジェクトを照会できます。 これで、ユーザー オブジェクト上でサインアップ中に収集される属性の一覧に、**ShoeSize** が表示されるようになります。 ユーザー オブジェクトにこの属性が追加された後は、アプリケーションから Graph API を呼び出して、そこからデータを取得することができます。

## <a name="next-steps"></a>次のステップ

[セルフサービス サインアップのユーザー フローをアプリに追加する](self-service-sign-up-user-flow.md)