---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーの BuildingBlocks 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a42c1d06051c283f0e911c4cd166884ddd060f45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633276"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[TrustFrameworkPolicy](trustframeworkpolicy.md)要素内に **BuildingBlocks** 要素が追加されます。

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

**BuildingBlocks** 要素は次の要素で構成されます｡これらの要素は､定義されている順序通りに指定する必要があります｡

- [ClaimsSchema](claimsschema.md) - ポリシーの一部として参照可能な要求の種類を定義します。 要求スキーマは、要求の種類を宣言する場所です。 要求の種類は、多くのプログラム言語の変数に似ています。 要求の種類を利用して､アプリケーションのユーザーからのデータの収集やソーシャル ID プロバイダーからの要求の受信､カスタム REST API との間のデータの送受信､カスタム ポリシーで使用する内部データの格納を行うことができます｡

- [Predicates と PredicateValidationsInput](predicates.md) -適切な形式のデータのみが要求に入力されていることを確認する検証プロセスを実行することができます。

- [ClaimsTransformations](claimstransformations.md) -ポリシーで使用できる要求変換の一覧が含まれます。  要求変換では、 要求が別の要求に変換されます。 要求変換では､以下のような変換メソッドを指定します。
  - 文字列要求の大文字と小文字を指定されたものに変換する｡ たとえば、文字列を小文字から大文字に変換します。
  - 2 つの要求を比較し､要求が一致することを示す場合は true､それ以外の場合は false を返す｡
  - ポリシーで指定されているパラメーターから文字列要求を作成する｡
  - 乱数ジェネレーターを使用してランダム文字列を作成する｡
  - 指定された書式文字列に従って要求の書式を設定する｡ この変換では､C# の `String.Format` メソッドを使用します。

- InputValidation - この要素により、 *and* や *or* に似たブール値の集計を実行できます。

- [ContentDefinitions](contentdefinitions.md) - ユーザー体験に使用する HTML5 テンプレートの URL を定義します｡ カスタム ポリシーのコンテンツ定義では、ユーザー体験中の指定した手順に使用する HTML5 ページの URI を定義します｡ たとえば、サインインまたはサインアップ、パスワード リセット、またはエラー ページなどです。 HTML5 ファイルの LoadUri をオーバーライドすることで、外観を変更できます。 あるいは、必要に応じて新しいコンテンツの定義を作成することもできます。 この要素には、ローカリゼーション ID を使用してローカライズされたリソース参照を含めることができます。

- [Localization](localization.md) - 複数の言語をサポートすることができます。 ポリシーでのローカライズ サポートにより、ポリシーでサポートする言語の一覧を設定し､既定の言語を選択できます。 また、言語固有の文字列とコレクションもサポートされます。

- [DisplayControls](display-controls.md) - ページに表示するコントロールを定義します。 表示コントロールには特別な機能があり、バックエンドの検証技術プロファイルと対話します。 
