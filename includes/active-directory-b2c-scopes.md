---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 3ebe1ec4c0292a530e5ef2c754e9b002e931300e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680302"
---
#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/) 

1. **[アプリの登録]** を選択します。
1. *webapi1* アプリケーションを選択して、その **[概要]** ページを開きます。
1. **[管理]** の **[API の公開]** を選択します。
1. **[アプリケーション ID URI]** の横にある **[設定]** リンクを選択します。
1. 既定値 (GUID) を `api` に置き換え、 **[保存]** を選択します。 完全な URI が表示されます。`https://your-tenant-name.onmicrosoft.com/api` の形式になっているはずです。 Web アプリケーションで API のアクセス トークンを要求するときに、API に対して定義する各スコープのプレフィックスとしてこの URI を追加する必要があります。
1. **[この API で定義されるスコープ]** で、 **[スコープの追加]** を選択します。
1. 次の値を入力して、API への読み取りアクセスを定義するスコープを作成し、 **[スコープの追加]** を選択します。
    1. **スコープ名**: `demo.read`
    1. **管理者の同意の表示名**: `Read access to demo API`
    1. **管理者の同意の説明**: `Allows read access to the demo API`
1. **[スコープの追加]** を選択し、次の値を入力して、API への書き込みアクセスを定義するスコープを作成し、 **[スコープの追加]** を選択します。
    1. **スコープ名**: `demo.write`
    1. **管理者の同意の表示名**: `Write access to demo API`
    1. **管理者の同意の説明**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. **[アプリケーション (レガシ)]** を選択します。
1. *webapi1* アプリケーションを選択して、その **[プロパティ]** ページを開きます。
1. **[公開済みスコープ]** を選択します。 公開済みスコープを使用すると、クライアント アプリケーションに Web API への特定のアクセス許可を付与することができます。
1. **[スコープ]** に「`demo.read`」と入力し、 **[説明]** に「`Read access to the web API`」と入力します。
1. **[スコープ]** に「`demo.write`」と入力し、 **[説明]** に「`Write access to the web API`」と入力します。
1. **[保存]** を選択します。