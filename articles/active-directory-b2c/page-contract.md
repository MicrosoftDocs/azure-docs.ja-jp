---
title: Azure Active Directory B2C でページ コントラクトを選択する | Microsoft Docs
description: Azure Active Directory B2C でページ コントラクトを選択する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 39198c0093f018b64a1292f023914651b51b4faf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403784"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>カスタム ポリシーを使用して Azure Active Directory B2C でページ コントラクトを選択する | Microsoft Docs

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

ユーザー フローを使用しているか、カスタム ポリシーを使用しているかに関係なく、Azure Active Directory (Azure AD) B2C ポリシーでクライアント側 JavaScript コードを有効にできます。 この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)でページ コントラクトを構成して、Azure AD B2C でページ コントラクトを選択する方法について説明します。 ページ コントラクトは、Azure AD B2C が提供する要素とお客様が指定するコンテンツの関連付けです。 [Javascript](javascript-samples.md) を使用する場合は、カスタム ポリシー内のすべてのコンテンツ定義に対してページ コントラクト バージョンを定義する必要があります。

> [!NOTE]
> ユーザー フローに対して JavaScript を有効にする場合、「[About using JavaScript and page contract versions in a user flow](user-flow-javascript-overview.md)」(ユーザー フローでの JavaScript とページ コントラクト バージョンの使用について) を参照してください。

## <a name="replace-datauri-values"></a>DataUri 値を置換する

カスタム ポリシー内には、ユーザー体験で使用される HTML テンプレートを定義する、[ContentDefinitions](contentdefinitions.md) が存在する場合があります。 **ContentDefinition** には、Azure AD B2C によって提供されるページ要素を参照する **DataUri** が含まれています。 **LoadUri** は、指定した HTML コンテンツと CSS コンテンツへの相対パスです。

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

ページ コントラクトを選択するには、ポリシーで [ContentDefinitions](contentdefinitions.md) 内の **DataUri** 値を変更します。 古い **DataUri** 値から新しい値に切り替えると、不変パッケージを選択することになります。 このパッケージを使用するメリットは、それが変更されたり、ページ上で予期しない動作が生じたりすることがないとわかっている点です。 

ページ コントラクトを設定するには、次の表を使用して **DataUri** 値を見つけます。 

| 古い DataUri 値 | 新しい DataUri 値 |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>次の手順

アプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細については、「[Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](active-directory-b2c-ui-customization-custom.md)」を参照してください。



