---
title: アプリの発行元ドメインを構成する | Azure
titleSuffix: Microsoft identity platform
description: ユーザーに情報の送信先を知らせるためにアプリケーションの発行元ドメインを構成する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 9b3840ffb39a204cfa24dcb0430f20ac16587582
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104180"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>方法:アプリケーションの発行元ドメインを構成する

アプリケーションの発行元ドメインは、ユーザーに情報の送信先を知らせるために[アプリケーションの同意プロンプト](application-consent-experience.md)でユーザーに表示されます。 2019 年 5 月 21 日の後に登録されたマルチテナント アプリケーションのうち、発行元ドメインのないアプリケーションは **未検証** として表示されます。 マルチテナント アプリケーションは、1 つの組織ディレクトリの外部にあるアカウントをサポートするアプリケーションです。たとえば、すべての Azure AD アカウントをサポートするか、またはすべての Azure AD アカウントと個人の Microsoft アカウントをサポートします。

## <a name="new-applications"></a>新しいアプリケーション

新しいアプリを登録すると、そのアプリの発行元ドメインは既定値に設定される可能性があります。 この値は、アプリが登録された場所、特にアプリがテナントで登録されたかどうか、およびそのテナントにテナントで検証済みのドメインが存在するかどうかによって異なります。

テナントで検証済みのドメインが存在する場合、アプリの発行元ドメインは、既定でそのテナントの検証済みプライマリ ドメインになります。 テナントで検証済みのドメインが存在しない場合 (そのテナントでアプリケーションが登録されていない場合)、アプリの発行元ドメインは null に設定されます。

次の表は、発行元ドメイン値の既定の動作をまとめたものです。  

| テナントで検証済みのドメイン | 発行元ドメインの既定値 |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (プライマリ) | domain2.com |

マルチテナント アプリケーションの発行元ドメインが設定されていないか、または .onmicrosoft.com で終わるドメインに設定されている場合、アプリの同意プロンプトには発行元ドメインの代わりに **未検証** が表示されます。

## <a name="grandfathered-applications"></a>条件が適用されないアプリケーション

アプリが 2019 年 5 月 21 日の前に登録された場合は、発行元ドメインを設定していなくても、アプリケーションの同意プロンプトに **未検証** は表示されません。 ユーザーがアプリの同意プロンプトでこの情報を確認できるように、発行元ドメイン値を設定することをお勧めします。

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Azure Portal を使用して発行元ドメインを構成する

アプリの発行元ドメインを設定するには、次の手順に従います。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、上部のメニューの **[ディレクトリとサブスクリプション]** フィルター :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: を使用して、アプリが登録されているテナントを選択します。
1. [[Azure Active Directory] > [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) に移動し、構成するアプリを見つけて選択します。

   アプリを選択すると、アプリの **[概要]** ページが表示されます。
1. **[管理]** 下にある **[ブランド]** を選択します。
1. **[発行元ドメイン]** フィールドを見つけ、次のいずれかのオプションを選択します。

   - ドメインがまだ構成されていない場合は、 **[ドメインの構成]** を選択します。
   - ドメインが既に構成されている場合は、 **[ドメインを更新]** を選択します。

アプリがテナントで登録されている場合は、選択するタブとして **[確認済みドメインの選択]** と **[新しいドメインの確認]** の 2 つが表示されます。

アプリがテナントで登録されていない場合は、アプリケーションの新しいドメインを検証するためのオプションのみが表示されます。

### <a name="to-verify-a-new-domain-for-your-app"></a>アプリの新しいドメインを検証するには

1. `microsoft-identity-association.json` という名前のファイルを作成し、次の JSON コード スニペットを貼り付けます。

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. プレースホルダー *{YOUR-APP-ID-HERE}* をアプリに対応するアプリケーション (クライアント) ID に置き換えます。

1. このファイルを `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` でホストします。 プレースホルダー *{YOUR-DOMAIN-HERE}* を検証済みのドメインに一致するように置き換えます。

1. **[ドメインを検証して保存]** ボタンをクリックします。

### <a name="to-select-a-verified-domain"></a>検証済みのドメインを選択するには

- テナントに検証済みのドメインが存在する場合は、 **[確認済みドメインの選択]** ドロップダウンからいずれかのドメインを選択します。

>[!Note]
> 返される必要がある 'Content-Type' ヘッダーは `application/json` です。 `application/json; charset=utf-8` のようにそれ以外のものを使用すると、以下に示すエラーが発生する場合があります。 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>アプリの同意プロンプトへの影響

発行元ドメインを構成すると、アプリの同意プロンプトでユーザーに表示される内容に影響を与えます。 同意プロンプトのコンポーネントを完全に理解するには、[アプリケーションの同意エクスペリエンスの理解](application-consent-experience.md)に関するページを参照してください。

次の表では、2019 年 5 月 21 日の前に作成されたアプリケーションの動作について説明します。

![2019 年 5 月 21 日の前に作成されたアプリの同意プロンプト](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019 年 5 月 21 日の後に作成された新しいアプリケーションの動作は、発行元ドメインとアプリケーションの種類によって異なります。 次の表では、さまざまな構成の組み合わせで表示されることが予測される内容の変化について説明します。

![2019 年 5 月 21 日の後に作成されたアプリの同意プロンプト](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>リダイレクト URI への影響

ユーザーが職場または学校アカウントあるいは個人の Microsoft アカウント ([マルチテナント](single-and-multi-tenant-apps.md)) でサインインするアプリケーションは、リダイレクト URI を指定するときにいくつかの制限に従います。

### <a name="single-root-domain-restriction"></a>単一のルート ドメインの制限

マルチテナント アプリの発行元ドメイン値が null に設定されている場合、アプリは、リダイレクト URI に関して単一のルート ドメインを共有するように制限されます。 たとえば、ルート ドメイン contoso.com は fabrikam.com に一致しないため、次の値の組み合わせは許可されません。

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>サブドメインの制限

サブドメインは許可されますが、ルート ドメインを明示的に登録する必要があります。 たとえば、次の URI は単一のルート ドメインを共有していますが、この組み合わせは許可されません。

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

ただし、開発者がルート ドメインを明示的に追加した場合、この組み合わせは許可されます。

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>例外

次のケースは、単一のルート ドメインの制限に従いません。

- シングル テナント アプリ、または 1 つのディレクトリ内のアカウントを対象にするアプリ
- リダイレクト URI としての localhost の使用
- カスタム スキームを含むリダイレクト URI (HTTP 以外または HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>プログラムで発行元ドメインを構成する

現在、プログラムで発行元ドメインを構成するための REST API または PowerShell のサポートはありません。
