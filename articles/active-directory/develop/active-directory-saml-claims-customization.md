---
title: Azure AD のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ | Microsoft Docs
description: Azure AD のエンタープライズ アプリケーションの SAML トークンで発行された要求をカスタマイズする方法について説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 636086ce0d055ab8de1d1b95dbbf7e5d96c7d7ef
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483061"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>方法:エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ

現在、Azure Active Directory (Azure AD) では、Azure AD アプリ ギャラリーの事前統合済みアプリケーション、カスタム アプリケーションを含め、ほとんどのエンタープライズ アプリケーションで、シングル サインオン (SSO) がサポートされています。 ユーザーが Azure AD によって SAML 2.0 プロトコルを使ってアプリケーションに対して認証されると、Azure AD は、アプリケーションにトークンを送信します (HTTP POST 経由)。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してユーザーをログオンします。 これらの SAML トークンには、"*要求*" と呼ばれる、ユーザーに関する情報が含まれています。

*要求*とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。 [SAML トークン](https://en.wikipedia.org/wiki/SAML_2.0)では、通常、このデータは SAML 属性ステートメントに含まれています。 ユーザーの一意の ID は通常、名前識別子とも呼ばれる SAML サブジェクトで表されます。

既定では、Azure AD により、アプリケーションに対して、Azure AD でのユーザーのユーザー名 (別名: ユーザー プリンシパル名) を値に持つ `NameIdentifier` 要求を含む SAML トークンが発行され、ユーザーを一意に識別できます。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含むその他の要求も含まれています。

アプリケーションに対して SAML トークンで発行された要求を表示または編集するには、Azure Portal でアプリケーションを開きます。 次に、 **[ユーザー属性とクレーム]** セクションを開きます。

![Azure portal で [ユーザー属性とクレーム] セクションを開く](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

SAML トークンで発行された要求を編集する必要がある理由は、2 つ考えられます。

* アプリケーションで、`NameIdentifier` 要求または NameID 要求が、Azure AD に格納されているユーザー名 (またはユーザー プリンシパル名) 以外のものである必要がある。
* アプリケーションが、別の要求 URI または要求値のセットを必要とするように記述されている。

## <a name="editing-nameid"></a>NameID の編集

NameID (名前識別子の値) を編集するには:

1. **[名前識別子の値]** ページを開きます。
1. 属性または属性に適用する変換を選択します。 必要に応じて、NameID 要求の形式を指定できます。

   ![NameID (名前識別子の値) を編集する](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID の形式

SAML 要求に、特定の形式を持つ NameIDPolicy 要素が含まれている場合、Azure AD では要求の形式が使用されます。

SAML 要求に NameIDPolicy の要素が含まれていない場合、Azure AD では指定した形式の NameID が発行されます。 形式を指定しないと、Azure AD では、選択されている要求ソースに関連付けられている既定のソース形式が使用されます。

**[名前識別子の形式の選択]** ドロップダウンで、次のオプションのいずれかを選択できます。

| NameID の形式 | 説明 |
|---------------|-------------|
| **既定値** | Azure AD では、既定のソース形式が使用されます。 |
| **永続的** | Azure AD では、NameID の形式として "永続的" が使用されます。 |
| **EmailAddress** | Azure AD では、NameID の形式として "EmailAddress" が使用されます。 |
| **未指定** | Azure AD では、NameID の形式として "未指定" が使用されます。 |
| **一時的** | Azure AD では、NameID の形式として "一時的" が使用されます。 |

NameIDPolicy 属性について詳しくは、「[シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)」をご覧ください。

### <a name="attributes"></a>属性

`NameIdentifier` (または NameID) 要求の必要なソースを選択します。 次のオプションから選択できます。

| Name | 説明 |
|------|-------------|
| Email | ユーザーの電子メール アドレス |
| userprincipalName | ユーザーのユーザー プリンシパル名 (UPN) |
| onpremisessamaccount | オンプレミスの Azure AD から同期された SAM アカウント名 |
| objectid | Azure AD でのユーザーの objectid |
| employeeid | ユーザーの employeeid |
| ディレクトリ拡張機能 | [Azure AD Connect 同期を使用してオンプレミスの Active Directory から同期された](../hybrid/how-to-connect-sync-feature-directory-extensions.md)ディレクトリ拡張機能 |
| 拡張属性 1 ～ 15 | Azure AD のスキーマを拡張するために使用されるオンプレミスの 拡張機能属性 |

詳しくは、「[表 3: ソースごとに有効な ID 値](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)」をご覧ください。

### <a name="special-claims---transformations"></a>特別な要求 - 変換

要求変換関数を使用することもできます。

| Function | 説明 |
|----------|-------------|
| **ExtractMailPrefix()** | メール アドレスまたはユーザー プリンシパル名からドメイン サフィックスを除去します。 これにより、渡されたユーザー名の最初の部分のみが抽出されます (例: joe_smith@contoso.com ではなく "joe_smith" のみ)。 |
| **Join()** | 属性を検証済みドメインと結合します。 選択したユーザー ID 値にドメインが含まれる場合、ユーザー名が抽出されて、選択された検証済みドメインが追加されます。 たとえば、ユーザー ID 値としてメール アドレス (joe_smith@contoso.com) を選択し、検証済みドメインとして contoso.onmicrosoft.com を選択した場合、結果は joe_smith@contoso.onmicrosoft.com になります。 |
| **ToLower()** | 選択した属性の文字を小文字に変換します。 |
| **ToUpper()** | 選択した属性の文字を大文字に変換します。 |

## <a name="adding-application-specific-claims"></a>アプリケーション固有の要求の追加

アプリケーション固有の要求を追加するには:

1. **[ユーザー属性とクレーム]** で、 **[新しいクレームの追加]** を選択して **[ユーザー クレームの管理]** ページを開きます。
1. クレームの **[名前]** を入力します。 値は、SAML 仕様の URI パターンに厳密に従う必要はありません。URI パターンが必要な場合は、 **[名前空間]** フィールドに追加することができます。
1. クレームの値が取得される **[ソース]** を選択します。 ソース属性のドロップダウンからユーザー属性を選択するか、または要求として生成する前にユーザー属性に変換を適用することができます。

### <a name="application-specific-claims---transformations"></a>アプリケーション固有の要求 - 変換

要求変換関数を使用することもできます。

| Function | 説明 |
|----------|-------------|
| **ExtractMailPrefix()** | メール アドレスまたはユーザー プリンシパル名からドメイン サフィックスを除去します。 これにより、渡されたユーザー名の最初の部分のみが抽出されます (例: joe_smith@contoso.com ではなく "joe_smith" のみ)。 |
| **Join()** | 2 つの属性を結合することで、新しい値を作成します。 必要に応じて、2 つの属性の間に区切り記号を使用できます。 |
| **ToLower()** | 選択した属性の文字を小文字に変換します。 |
| **ToUpper()** | 選択した属性の文字を大文字に変換します。 |
| **Contains()** | 入力が指定した値と一致する場合、属性または定数を出力します。 一致しない場合は、別の出力を指定できます。<br/>たとえば、ユーザーのメール アドレスに "@contoso.com" が含まれる場合はメール アドレスを値とする要求を出力し、それ以外の場合はユーザー プリンシパル名を出力するものとします。 これを行うには、次の値を構成します。<br/>*Parameter 1 (入力)* : user.email<br/>*Value*: "@contoso.com"<br/>Parameter 2 (出力): user.email<br/>Parameter 3 (一致しない場合の出力): user.userprincipalname |
| **EndWith()** | 入力が指定した値で終わっている場合、属性または定数を出力します。 一致しない場合は、別の出力を指定できます。<br/>たとえば、ユーザーの employeeid が "000" で終わっている場合は employeeid を値とする要求を出力し、それ以外の場合は拡張属性を出力するものとします。 これを行うには、次の値を構成します。<br/>*Parameter 1 (入力)* : user.employeeid<br/>*値*: "000"<br/>Parameter 2 (出力): user.employeeid<br/>Parameter 3 (一致しない場合の出力): user.extensionattribute1 |
| **StartWith()** | 入力が指定した値で始まっている場合、属性または定数を出力します。 一致しない場合は、別の出力を指定できます。<br/>たとえば、country/region が "US" で始まっている場合はユーザーの employeeid を値とする要求を出力し、それ以外の場合は拡張属性を出力するものとします。 これを行うには、次の値を構成します。<br/>*Parameter 1 (入力)* : user.country<br/>*値*: "US"<br/>Parameter 2 (出力): user.employeeid<br/>Parameter 3 (一致しない場合の出力): user.extensionattribute1 |
| **Extract() - 一致の後** | 指定した値との一致より後の部分文字列を返します。<br/>たとえば、入力の値が "Finance_BSimon" で、一致する値が "Finance_" の場合、要求の出力は "BSimon" です。 |
| **Extract() - 一致の前** | 指定した値との一致より前の部分文字列を返します。<br/>たとえば、入力の値が "BSimon_US" で、一致する値が "_US" の場合、要求の出力は "BSimon" です。 |
| **Extract() - 一致の間** | 指定した値との一致より前の部分文字列を返します。<br/>たとえば、入力の値が "Finance_BSimon_US" で、1 番目の一致する値が "Finance_"、2 番目の一致する値が "_US" である場合、要求の出力は "BSimon" です。 |
| **ExtractAlpha() - プレフィックス** | 文字列のプレフィックスのアルファベット部分を返します。<br/>たとえば、入力の値が "BSimon_123" の場合は、"BSimon" を返します。 |
| **ExtractAlpha() - サフィックス** | 文字列のサフィックスのアルファベット部分を返します。<br/>たとえば、入力の値が "123_Simon" の場合は、"Simon" を返します。 |
| **ExtractNumeric() - プレフィックス** | 文字列のプレフィックスの数字部分を返します。<br/>たとえば、入力の値が "123_BSimon" の場合は、"123" を返します。 |
| **ExtractNumeric() - サフィックス** | 文字列のサフィックスの数字部分を返します。<br/>たとえば、入力の値が "BSimon_123" の場合は、"123" を返します。 |
| **IfEmpty()** | 入力が null または空の場合、属性または定数を出力します。<br/>たとえば、特定のユーザーの employeeid が空の場合は、extensionattribute に格納されている属性を出力するものとします。 これを行うには、次の値を構成します。<br/>Parameter 1 (入力): user.employeeid<br/>Parameter 2 (出力): user.extensionattribute1<br/>Parameter 3 (一致しない場合の出力): user.employeeid |
| **IfNotEmpty()** | 入力が null または空ではない場合、属性または定数を出力します。<br/>たとえば、特定のユーザーの employeeid が空ではない場合は、extensionattribute に格納されている属性を出力するものとします。 これを行うには、次の値を構成します。<br/>Parameter 1 (入力): user.employeeid<br/>Parameter 2 (出力): user.extensionattribute1 |

他の変換が必要な場合は、[Azure AD のフィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)の *SaaS アプリケーション* カテゴリで、アイデアをお送りください。

## <a name="next-steps"></a>次の手順

* [Azure AD のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [Azure AD アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンを構成する](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](howto-v1-debug-saml-sso-issues.md)
