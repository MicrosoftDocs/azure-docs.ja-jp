---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f941f67105227b3973386b015344d5fa787c3485
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317551"
---
アプリケーションを Azure AD B2C テナントに登録するには、Microsoft の新しい統合**アプリの登録**エクスペリエンスか以前の**アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの**名前**を入力します。 たとえば、*ROPC_Auth_app* のようになります。
1. その他の値はそのままにして、 **[登録]** を選択します。
1. 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。
1. **[管理]** で、 **[認証]** を選択します。
1. **[新しいエクスペリエンスを試す]** (表示されている場合) を選択します。
1. **[既定のクライアントの種類]** で **[はい]** を選択して、アプリケーションをパブリック クライアントとして扱います。 ROPC フローには、この設定が必要となります。
1. **[保存]** を選択します。
1. 左側のメニューで、 **[マニフェスト]** を選択してマニフェスト エディターを開きます。 
1. **oauth2AllowImplicitFlow** 属性を *true* に設定します。
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. **[保存]** を選択します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション (レガシ)]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*ROPC_Auth_app* のようになります。
1. **[ネイティブ クライアント]** の場合、 **[はい]** を選択します。
1. その他の値はそのままにして、 **[作成]** を選択します。
1. 後の手順で使用するために、**アプリケーション ID** を記録しておきます。