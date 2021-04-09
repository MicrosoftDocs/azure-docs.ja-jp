---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーの ContentDefinitions element 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62bae22b6a4bb06b1e97c18e52ad614fd2439902
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489323"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[セルフアサート技術プロファイル](self-asserted-technical-profile.md)の外観をカスタマイズすることができます。 Azure Active Directory B2C (Azure AD B2C) は、ユーザーのブラウザーでコードを実行し、クロス オリジン リソース共有 (CORS) と呼ばれる最新の手法を使用します。

ユーザー インターフェイスをカスタマイズするには、**ContentDefinition** 要素でカスタマイズされた HTMLコンテンツを含む URL を指定します。 セルフアサート技術プロファイルまたは **OrchestrationStep** で、そのコンテンツ定義識別子をポイントします。 コンテンツ定義には、ロードするローカライズされたリソースの一覧を指定する、**LocalizedResourcesReference**  要素を含めることができます。 Azure AD B2C により、ユーザー インターフェイス要素が URL から読み込まれた HTML コンテンツとマージされ、ユーザーにページが表示されます。

**ContentDefinitions** 要素には、ユーザー体験で使用できる HTML5 テンプレートへの URL が含まれています。 HTML5 ページの URI は、指定されたユーザー インターフェイス ステップで使用されます。 たとえば、サインインまたはサインアップ、パスワード リセット、またはエラー ページなどです。 HTML5 ファイルの LoadUri をオーバーライドすることで、外観を変更できます。 必要に応じて新しいコンテンツ定義を作成できます。 この要素には、[Localization](localization.md) 要素で指定されたローカライズ識別子への、ローカライズされたリソース参照を含めることができます。

次の例は、コンテンツ定義識別子とローカライズされたリソースの定義を示しています。

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

**LocalAccountSignUpWithLogonEmail** セルフアサート技術プロファイルには、`api.localaccountsignup` に設定されたコンテンツ定義識別子 **ContentDefinitionReferenceId** が含まれています。

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | コンテンツ定義の識別子。 この値は、このページの後半の **コンテンツ定義 ID** セクションで指定される値です。 |

**ContentDefinition** 要素には、次の属性が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | コンテンツ定義用の HTML5 ページの URL を含む文字列。 |
| RecoveryUri | 1:1 | コンテンツ定義に関連するエラーを表示するための HTML ページの URL を含む文字列。 現在使用されていません。値は `~/common/default_page_error.html` にする必要があります。 |
| DataUri | 1:1 | ステップに対して呼び出すユーザー エクスペリエンスを提供する、HTML ファイルの相対 URL を含む文字列。 |
| Metadata | 0:1 | コンテンツ定義によって利用されるメタデータを含む、キー/値ペアのコレクション。 |
| LocalizedResourcesReferences | 0:1 | ローカライズされたリソース参照のコレクション。 この要素を使用して、ユーザー インターフェイスと要求属性のローカライズをカスタマイズします。 |

### <a name="datauri"></a>DataUri

**DataUri** 要素は、ページ識別子を指定するために使用されます。 Azure AD B2C は、ページ識別子を使用して、UI 要素とクライアント側の JavaScript を読み込み開始します。 この値のフォーマットは `urn:com:microsoft:aad:b2c:elements:page-name:version` です。 次の表に、使用できるページ識別子の一覧を示します。

| ページ識別子 | 説明 |
| ----- | ----------- |
| `globalexception` | 例外またはエラーが発生したときにエラー ページを表示します。 |
| `providerselection`, `idpselection` | ユーザーがサインイン時に選択できる ID プロバイダーを一覧表示します。  |
| `unifiedssp` | 電子メール アドレスまたはユーザー名に基づいたローカル アカウントでサインインするためのフォームを表示します。 またこの値は、「サインインしたままにする機能」および「パスワードを忘れた場合」 のリンクをクリックするように促します。 |
| `unifiedssd` | 電子メール アドレスまたはユーザー名に基づいたローカル アカウントでサインインするためのフォームを表示します。 |
| `multifactor` | サインアップ中またはサインイン中にテキストまたは音声を使用して電話番号を確認します。 |
| `selfasserted` | ユーザーからデータを収集するためのフォームを表示します。 たとえば、ユーザーがプロファイルを作成または更新できるようにします。 |

### <a name="select-a-page-layout"></a>ページ レイアウトを選択する

[JavaScript クライアント側コード](javascript-and-page-layout.md)を有効にするには、`elements` とページの種類の間に `contract` を挿入します。 たとえば、「 `urn:com:microsoft:aad:b2c:elements:contract:page-name:version` 」のように入力します。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

`DataUri` の [version](page-layout.md) 部分には、ポリシーのユーザー インターフェイス要素に対する HTML、CSS、JavaScript を含むコンテンツのパッケージが指定されます。 JavaScript クライアント側コードを有効にする場合は、JavaScript の基になる要素を変更不可にする必要があります。 変更不可になっていないと、何らかの変更によってユーザー フローのページで予期しない動作が発生する可能性があります。 このような問題を防ぐために、ページ レイアウトの使用を強制して、ページ レイアウトのバージョンを指定します。 これにより、JavaScript の基になるすべてのコンテンツ定義が変更不可になります。 JavaScript を有効にする予定がない場合でも、引き続きページに対してページ レイアウトのバージョンを指定する必要があります。

次の例は、`selfasserted` バージョン `1.2.0` の **DataUri** を示しています。

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>ページ レイアウトへの移行

値の形式には、次のように `contract` という語が含まれている必要があります: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 古い **DataUri** 値を使用するカスタム ポリシーでページ レイアウトを指定するには、次の表を使用して新しい形式に移行してください。

| 古い DataUri 値 | 新しい DataUri 値 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |

次の例は、コンテンツ定義識別子と、対応する **DataUri** を最新ページ バージョンと共に示しています。 

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Metadata

**Metadata** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Item | 0:n | コンテンツ定義に関連するメタデータ。 |

**Metadata** 要素の **Item** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Key | はい | メタデータ キー。  |

#### <a name="metadata-keys"></a>メタデータ キー

コンテンツ定義では、次のメタデータ項目がサポートされています。

| Key | 必須 | 説明 |
| --------- | -------- | ----------- |
| DisplayName | いいえ | コンテンツ定義の名前を表す文字列。 |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**LocalizedResourcesReferences** 要素には、次の属性が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | コンテンツ定義のローカライズされたリソース参照の一覧。 |

**LocalizedResourcesReference** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Language | はい | 「RFC 5646 - 言語を識別するタグ」に従ってポリシーでサポートされている言語を含む文字列。 |
| LocalizedResourcesReferenceId | はい | **LocalizedResources** 要素の識別子。 |

次の例は、英語、フランス語、およびスペイン語のローカライズへの参照を含む、サインアップまたはサインインのコンテンツ定義を示しています。

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

コンテンツ定義にローカライズ サポートを追加する方法については、「[ローカライズ](localization.md)」を参照してください。

## <a name="content-definition-ids"></a>コンテンツ定義 ID

**ContentDefinition** 要素の ID 属性は、コンテンツ定義に関連するページの種類を指定します。 この要素は、カスタム HTML5/CSS テンプレートが適用されるコンテキストを定義します。 次の表は、Identity Experience Framework によって認識される一連のコンテンツ定義 ID と、それらに関連するページの種類を示しています。 任意の ID を使用して独自のコンテンツ定義を作成できます。

| ID | 既定のテンプレート | 説明 |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **エラー ページ** - 例外またはエラーが発生したときにエラーページを表示します。 |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **ID プロバイダーの選択ページ** - ユーザーがサインイン時に選択できる ID プロバイダーを一覧表示します。 ID プロバイダーは、通常、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **サインアップのための ID プロバイダーの選択** - ユーザーがサインアップ時に選択できる ID プロバイダーを一覧表示します。 ID プロバイダーは、通常、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **パスワードを忘れた場合のページ** - パスワードのリセットを開始するためにユーザーが入力する必要があるフォームを表示します。 |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ローカル アカウントのサインイン ページ** - 電子メール アドレスまたはユーザー名に基づいたローカル アカウントでサインインするためのフォームを表示します。 このフォームには、テキスト入力ボックスとパスワード入力ボックスを含めることができます。 |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ローカル アカウントのサインアップ ページ** - 電子メール アドレスまたはユーザー名に基づいたローカル アカウントでサインアップするためのフォームを表示します。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなど、さまざまな入力コントロールを含めることができます。 |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多要素認証ページ** - サインアップ中またはサインイン中にテキストまたは音声を使用して電話番号を確認します。 |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ソーシャル アカウントのサインアップ ページ** - ソーシャル ID プロバイダーの既存のアカウントを使用してサインアップするときに、ユーザーが入力する必要があるフォームを表示します。 このページは、パスワード入力フィールドを除いて、上記のソーシャル アカウントのサインアップ ページに似ています。 |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **プロファイルの更新ページ** - ユーザーがプロファイルを更新するためにアクセスできるフォームを表示します。 このページは、パスワード入力フィールドを除いて、ソーシャル アカウントのサインアップ ページに似ています。 |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統合されたサインアップ ページまたはサインイン ページ** - ユーザーのサインアップおよびサインイン プロセスを処理します。 ユーザーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかを使用できます。 |

## <a name="next-steps"></a>次のステップ

コンテンツ定義を使用してユーザー インターフェイスをカスタマイズする例については、次を参照してください。

[カスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)
