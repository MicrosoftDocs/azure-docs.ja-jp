---
title: ClaimsProviders  - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーの ClaimsProvider 要素を指定します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5068692d5f12b5b6c8b0476a789123ef9fd1dc75
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508812"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

1 つのクレーム プロバイダーには、[技術プロファイル](technicalprofiles.md)のセットが含まれます。 どのクレーム プロバイダーも、そのクレーム プロバイダーと通信するために必要なエンドポイントとプロトコルを特定する技術プロファイルを 1 つ以上持っている必要があります。 1 つのクレーム プロバイダーが複数の技術プロファイルを持つことができます。 たとえば、クレーム プロバイダーが複数のプロトコルや異なる機能を持つ多様なエンドポイントをサポートしていること、異なる保証レベルでさまざまな要求をリリースしていることが理由で、技術プロファイルが複数定義される場合があります。 あるユーザー体験では変動しやすい要求をリリースすることが許されても、別のユーザー体験ではそうでないこともあります。

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | さまざまなユーザー体験で活用できる認定クレーム プロバイダー。 |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** 要素には、以下の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ---------- | ----------- |
| Domain | 0:1 | クレーム プロバイダーのドメイン名を含む文字列。 たとえば、クレーム プロバイダーが Facebook 技術プロファイルを含めている場合、ドメイン名は Facebook.com です。 このドメイン名は、技術プロファイルによってオーバーライドされない限り、クレーム プロバイダーで定義されているすべての技術プロファイルに使用されます。 ドメイン名も **domain_hint** で参照されます。 詳細については、「[Azure Active Directory B2C を使用した直接サインインの設定](direct-signin.md)」の「**サインインをソーシャル プロバイダーにリダイレクトする**」セクションを参照してください。 |
| DisplayName | 0:1 | ユーザーに表示できるクレーム プロバイダーの名前を含む文字列。 |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | クレーム プロバイダーによってサポートされている技術プロファイルのセット |

**ClaimsProvider** は、そのクレーム プロバイダーに関係がある技術プロファイルをまとめています。 次の例は、Azure Active Directory の技術プロファイルがある Azure Active Directory のクレーム プロバイダーを示しています。

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

次の例は、**Facebook OAUTH** 技術プロファイルがある Facebook のクレーム プロバイダーを示しています。

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
