---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "82925445"
---
## <a name="register-an-azure-ad-app"></a>Azure AD アプリの登録

特定の Azure AD 組織のユーザーのサインインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 組織の Azure AD テナント (contoso.com など) が含まれているディレクトリを使用していることを確認します。 上部のメニューで **[ディレクトリ + サブスクリプション] フィルター**を選択し、Azure AD テナントが含まれているディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
1. このアプリケーションには、 **[この組織のディレクトリ内のアカウントのみ]** という既定の選択をそのまま使用します。
1. **[リダイレクト URL]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp` 」のように入力します。

1. **[登録]** を選択します。 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。
1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。
1. シークレットの**説明**を入力し、有効期限を選択して、 **[追加]** を選択します。 後の手順で使用するために、シークレットの**値**を記録しておきます。

### <a name="configuring-optional-claims"></a>省略可能な要求の構成

Azure AD から `family_name` および `given_name` 要求を取得する場合は、ご利用のアプリケーションに対して省略可能な要求を Azure portal UI またはアプリケーション マニフェストで構成できます。 詳細については、[Azure AD アプリに省略可能な要求を提供する方法](/azure/active-directory/develop/active-directory-optional-claims)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 **Azure Active Directory** を検索して選択します。
1. **[管理]** セクションで、 **[アプリの登録]** を選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。
1. **[管理]** セクションで、 **[トークン構成]** を選択します。
1. **[省略可能な要求を追加]** を選択します。
1. **[トークンの種類]** で、 **[ID]** を選択します。
1. 追加する省略可能な要求 (`family_name` と `given_name`) を選択します。
1. **[追加]** をクリックします。