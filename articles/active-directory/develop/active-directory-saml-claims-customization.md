---
title: アプリの SAML トークン要求をカスタマイズする
titleSuffix: Microsoft identity platform
description: エンタープライズ アプリケーションの SAML トークンで Microsoft ID プラットフォームによって発行された要求をカスタマイズする方法について説明します。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 0cccf45037320b476b1a44cafa8074bacadacbc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600951"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ

現在、Microsoft ID プラットフォームによるシングル サインオン (SSO) は、Azure AD アプリ ギャラリー内の事前統合済みアプリケーションと、カスタム アプリケーションを含め、ほとんどのエンタープライズ アプリケーションでサポートされています。 ユーザーが Microsoft ID プラットフォームで SAML 2.0 プロトコルを使ってアプリケーションへのユーザー認証を行うと、Microsoft ID プラットフォームは、(HTTP POST を使用して) アプリケーションにトークンを送信します。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してユーザーをログオンします。 これらの SAML トークンには、"*要求*" と呼ばれる、ユーザーに関する情報が含まれています。

*要求* とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。 [SAML トークン](https://en.wikipedia.org/wiki/SAML_2.0)では、通常、このデータは SAML 属性ステートメントに含まれています。 ユーザーの一意の ID は通常、名前識別子とも呼ばれる SAML サブジェクトで表されます。

既定では、Microsoft ID プラットフォームにより、ユーザーを一意に識別できる Azure AD のユーザー名 (別名: ユーザー プリンシパル名) を値として持つ `NameIdentifier` 要求を含む SAML トークンがアプリケーションに対して発行されます。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含むその他の要求も含まれています。

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

特定の形式の NameIDPolicy 要素が SAML 要求に含まれている場合、その要求の形式が Microsoft ID プラットフォームで使用されます。

SAML 要求に NameIDPolicy 要素が含まれていない場合、指定した形式の NameID がMicrosoft ID プラットフォームによって発行されます。 形式を指定しないと、Microsoft ID プラットフォームでは、選択した要求ソースに関連付けられている既定のソース形式が使用されます。

**[名前識別子の形式の選択]** ドロップダウンで、次のオプションのいずれかを選択できます。

| NameID の形式 | 説明 |
|---------------|-------------|
| **[Default]** | 既定のソース形式が使用されます。 |
| **永続的** | NameID 形式として Persistent が使用されます。 |
| **EmailAddress** | NameID 形式として EmailAddress が使用されます。 |
| **未指定** | NameID 形式として Unspecified が使用されます。 |

一時的な NameID もサポートされていますが、ドロップダウンでは選択できず、また、Azure 側で構成できません。 NameIDPolicy 属性について詳しくは、「[シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)」をご覧ください。

### <a name="attributes"></a>属性

`NameIdentifier` (または NameID) 要求の必要なソースを選択します。 次のオプションから選択できます。

| 名前 | 説明 |
|------|-------------|
| Email | ユーザーの電子メール アドレス |
| userprincipalName | ユーザーのユーザー プリンシパル名 (UPN) |
| onpremisessamaccountname | オンプレミスの Azure AD から同期された SAM アカウント名 |
| objectid | Azure AD でのユーザーの objectid |
| employeeid | ユーザーの従業員 ID |
| ディレクトリ拡張機能 | [Azure AD Connect 同期を使用してオンプレミスの Active Directory から同期された](../hybrid/how-to-connect-sync-feature-directory-extensions.md)ディレクトリ拡張機能 |
| 拡張属性 1 ～ 15 | Azure AD のスキーマを拡張するために使用されるオンプレミスの 拡張機能属性 |

詳しくは、「[表 3: ソースごとに有効な ID 値](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)」をご覧ください。

また、Azure AD で定義したあらゆるクレームに定数 (静的) 値を割り当てることができます。 定数値は次の手順で割り当ててください。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> の **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで、**編集** アイコンをクリックしてクレームを編集ます。
1. 変更する必要があるクレームをクリックします。
1. 組織に応じて **[ソース属性]** に引用符を付けずに定数値を入力し、 **[保存]** をクリックします。

    ![Azure portal の [Org Attributes & Claims]\(組織の属性と要求\) セクション](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 定数値は次のように表示されます。

    ![Azure portal の [Edit Attributes & Claims]\(属性と要求の編集\) セクション](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>特別な要求 - 変換

要求変換関数を使用することもできます。

| 機能 | 説明 |
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

### <a name="claim-transformations"></a>要求の変換

ユーザー属性に変換を適用するには、次の手順を行います。

1. **[要求の管理]** で、要求ソースとして *[変換]* を選択して、 **[変換の管理]** ページを開きます。
2. 変換ドロップダウンから関数を選択します。 選択した関数に応じて、変換内で評価するパラメーターと定数値を指定する必要があります。 使用可能な関数の詳細については、次の表を参照してください。
3. 複数の変換を適用するには、 **[変換の追加]** をクリックします。1 つの要求に対して最大 2 つの変換を適用できます。 たとえば、最初に `user.mail` のメール プレフィックスを抽出できます。 次に、文字列を大文字にします。

   ![複数の要求の変換](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

次の関数を使用して、要求を変換できます。

| 機能 | 説明 |
|----------|-------------|
| **ExtractMailPrefix()** | メール アドレスまたはユーザー プリンシパル名からドメイン サフィックスを除去します。 これにより、渡されたユーザー名の最初の部分のみが抽出されます (例: joe_smith@contoso.com ではなく "joe_smith" のみ)。 |
| **Join()** | 2 つの属性を結合することで、新しい値を作成します。 必要に応じて、2 つの属性の間に区切り記号を使用できます。 NameID 要求の変換では、結合は検証済みドメインに制限されます。 選択したユーザー ID 値にドメインが含まれる場合、ユーザー名が抽出されて、選択された検証済みドメインが追加されます。 たとえば、ユーザー ID 値としてメール アドレス (joe_smith@contoso.com) を選択し、検証済みドメインとして contoso.onmicrosoft.com を選択した場合、結果は joe_smith@contoso.onmicrosoft.com になります。 |
| **ToLowercase()** | 選択した属性の文字を小文字に変換します。 |
| **ToUppercase()** | 選択した属性の文字を大文字に変換します。 |
| **Contains()** | 入力が指定した値と一致する場合、属性または定数を出力します。 一致しない場合は、別の出力を指定できます。<br/>たとえば、ユーザーのメール アドレスに "@contoso.com" が含まれる場合はメール アドレスを値とする要求を出力し、それ以外の場合はユーザー プリンシパル名を出力するものとします。 これを行うには、次の値を構成します。<br/>*Parameter 1 (入力)* : user.email<br/>*Value*: "@contoso.com"<br/>Parameter 2 (出力): user.email<br/>Parameter 3 (一致しない場合の出力): user.userprincipalname |
| **EndWith()** | 入力が指定した値で終わっている場合、属性または定数を出力します。 一致しない場合は、別の出力を指定できます。<br/>たとえば、ユーザーの従業員 ID が "000" で終わっている場合は従業員 ID を値とする要求を出力し、それ以外の場合は拡張属性を出力するものとします。 これを行うには、次の値を構成します。<br/>*Parameter 1 (入力)* : user.employeeid<br/>*値*: "000"<br/>Parameter 2 (出力): user.employeeid<br/>Parameter 3 (一致しない場合の出力): user.extensionattribute1 |
| **StartWith()** | 入力が指定した値で始まっている場合、属性または定数を出力します。 一致しない場合は、別の出力を指定できます。<br/>たとえば、country/region が "US" で始まっている場合はユーザーの従業員 ID を値とする要求を出力し、それ以外の場合は拡張属性を出力するものとします。 これを行うには、次の値を構成します。<br/>*Parameter 1 (入力)* : user.country<br/>*値*: "US"<br/>Parameter 2 (出力): user.employeeid<br/>Parameter 3 (一致しない場合の出力): user.extensionattribute1 |
| **Extract() - 一致の後** | 指定した値との一致より後の部分文字列を返します。<br/>たとえば、入力の値が "Finance_BSimon" で、一致する値が "Finance_" の場合、要求の出力は "BSimon" です。 |
| **Extract() - 一致の前** | 指定した値との一致より前の部分文字列を返します。<br/>たとえば、入力の値が "BSimon_US" で、一致する値が "_US" の場合、要求の出力は "BSimon" です。 |
| **Extract() - 一致の間** | 指定した値との一致より前の部分文字列を返します。<br/>たとえば、入力の値が "Finance_BSimon_US" で、1 番目の一致する値が "Finance\_"、2 番目の一致する値が "\_US" である場合、要求の出力は "BSimon" です。 |
| **ExtractAlpha() - プレフィックス** | 文字列のプレフィックスのアルファベット部分を返します。<br/>たとえば、入力の値が "BSimon_123" の場合は、"BSimon" を返します。 |
| **ExtractAlpha() - サフィックス** | 文字列のサフィックスのアルファベット部分を返します。<br/>たとえば、入力の値が "123_Simon" の場合は、"Simon" を返します。 |
| **ExtractNumeric() - プレフィックス** | 文字列のプレフィックスの数字部分を返します。<br/>たとえば、入力の値が "123_BSimon" の場合は、"123" を返します。 |
| **ExtractNumeric() - サフィックス** | 文字列のサフィックスの数字部分を返します。<br/>たとえば、入力の値が "BSimon_123" の場合は、"123" を返します。 |
| **IfEmpty()** | 入力が null または空の場合、属性または定数を出力します。<br/>たとえば、特定のユーザーの従業員 ID が空の場合は、extensionattribute に格納されている属性を出力するものとします。 これを行うには、次の値を構成します。<br/>Parameter 1 (入力): user.employeeid<br/>Parameter 2 (出力): user.extensionattribute1<br/>Parameter 3 (一致しない場合の出力): user.employeeid |
| **IfNotEmpty()** | 入力が null または空ではない場合、属性または定数を出力します。<br/>たとえば、特定のユーザーの従業員 ID が空ではない場合は、extensionattribute に格納されている属性を出力するものとします。 これを行うには、次の値を構成します。<br/>Parameter 1 (入力): user.employeeid<br/>Parameter 2 (出力): user.extensionattribute1 |

他の変換が必要な場合は、[Azure AD のフィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)の *SaaS アプリケーション* カテゴリで、アイデアをお送りください。

## <a name="emitting-claims-based-on-conditions"></a>条件に基づいた要求の出力

ユーザーの種類とユーザーが属するグループに基づいて、要求のソースを指定することができます。 

ユーザーの種類は次のとおりです。
- **任意**: すべてのユーザーはアプリケーションへのアクセスが許可されています。
- **メンバー**: テナントのネイティブ メンバー
- **すべてのゲスト**: ユーザーは、Azure AD の有無にかかわらず、外部の組織から移行します。
- **AAD ゲスト**: ゲスト ユーザーは、Azure AD を使用する別の組織に属します。
- **外部のゲスト**: ゲスト ユーザーは、Azure AD のない外部組織に属します。


これが役立つのは、クレームのソースが、ゲストとアプリケーションにアクセスする従業員とで異なる場合です。 ユーザーが従業員である場合、NameID が user.email からソース化されるように指定できます。ただし、ユーザーがゲストの場合は、NameID は user.extensionattribute1 からソース化されます。

要求条件を追加するには、次の手順を行います。

1. **[要求の管理]** で、要求条件を展開します。
2. ユーザーの種類を選択します。
3. ユーザーが属するグループを選択します。 特定のアプリケーションに対するすべての要求で、最大 50 個の一意のグループを選択できます。 
4. クレームの値が取得される **[ソース]** を選択します。 ソース属性のドロップダウンからユーザー属性を選択するか、または要求として生成する前にユーザー属性に変換を適用することができます。

条件を追加する順序は重要です。 Azure AD では、条件を上から下に評価し、要求に出力する値を決定しています。 式と一致する最後の値が要求に出力されます。

たとえば、Britta Simon は Contoso テナントのゲスト ユーザーです。 彼女は、同様に Azure AD を使用する別の組織に属しています。 Fabrikam アプリケーションが下のように構成されている場合、Britta が Fabrikam にサインインしようとすると、Microsoft ID プラットフォームで次のように条件が評価されます。

最初に、Microsoft ID プラットフォームにより、Britta のユーザーの種類が `All guests` かどうかが確認されます。 これに当てはまるため、要求のソースが `user.extensionattribute1` に割り当てられます。 次に、Britta のユーザーの種類が `AAD guests` かどうかが確認されます。これも当てはまるため、要求のソースが `user.mail` に割り当てられます。 最後に、Britta の値 `user.mail` を使用して要求が出力されます。

![要求の条件付き構成](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>次のステップ

* [Azure AD のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [Azure AD アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンを構成する](../manage-apps/configure-saml-single-sign-on.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](../manage-apps/debug-saml-sso-issues.md)
