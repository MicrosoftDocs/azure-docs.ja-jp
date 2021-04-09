---
title: Azure AD ユーザー フローでの言語のカスタマイズ
description: Azure Active Directory でのユーザー フローの言語エクスペリエンスのカスタマイズについて説明します。
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: a199c207e8ea35f1471df9bfd0c4134551b9995f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653492"
---
# <a name="language-customization-in-azure-active-directory"></a>Azure Active Directory での言語のカスタマイズ

Azure Active Directory (Azure AD) の言語のカスタマイズを使うと、ユーザーのニーズに応じて、ユーザー フローをさまざまな言語に対応させることができます。 Microsoft では、[36 言語](#supported-languages)の翻訳を提供しています。 1 つの言語のエクスペリエンスしか提供されない場合でも、属性コレクション ページで属性名をカスタマイズできます。

## <a name="how-language-customization-works"></a>言語のカスタマイズのしくみ

既定では、一貫したサインアップ エクスペリエンスを保証するために、サインアップしているユーザーに対して言語のカスタマイズが有効になります。 言語を使用して、サインアップ中に属性コレクション プロセスの一部としてユーザーに表示される文字列を変更できます。

> [!NOTE]
> カスタム ユーザー属性を使っている場合は、独自の翻訳を提供する必要があります。 詳しくは、「[文字列のカスタマイズ](#customize-your-strings)」をご覧ください。

## <a name="customize-your-strings"></a>文字列のカスタマイズ

言語のカスタマイズを使うと、ユーザー フローの文字列をカスタマイズできます。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[ユーザー フロー]** を選択します。
3. 翻訳を有効にするユーザー フローを選択します。
4. **[言語]** を選択します。
5. ユーザー フローの **[言語]** ページで、カスタマイズする言語を選択します。
6. **属性コレクション ページ** を展開します。
7. **[既定値のダウンロード]** (この言語を前に編集した場合は **[オーバーライドのダウンロード]** ) を選びます。

上記の手順により、文字列の編集を開始する際に使用できる JSON ファイルを入手できます。

### <a name="change-any-string-on-the-page"></a>ページ上の文字列を変更する

1. 前の手順でダウンロードした JSON ファイルを JSON エディターで開きます。
1. 変更する要素を探します。 探している文字列の `StringId` を見つけるか、変更する `Value` 属性を見つけます。
1. `Value` 属性を表示する文字列に更新します。
1. 変更するすべての文字列について、`Override` を `true` に変更します。
1. ファイルを保存し、変更内容をアップロードします。 (アップロード コントロールは、JSON ファイルをダウンロードした場所にあります)

> [!IMPORTANT]
> 文字列をオーバーライドする必要がある場合は、必ず `Override` の値を `true` に設定してください。 値が変更されていない場合、そのエントリは無視されます。

### <a name="change-extension-attributes"></a>拡張属性を変更する

カスタム ユーザー属性の文字列を変更する場合や、JSON に追加する場合は、次の形式を使います。

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>` をカスタム ユーザー属性の名前に置き換えます。

`<ExtensionAttributeValue>` を表示する新しい文字列に置き換えます。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections を使って値の一覧を提供する

応答の値セットの一覧を指定する場合は、`LocalizedCollections` 属性を作成する必要があります。 `LocalizedCollections` は、`Name` と `Value` のペアの配列です。 項目の順序は、表示される順序になります。 `LocalizedCollections` を追加するには、次の形式を使います。

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` は、この `LocalizedCollections` 属性が応答になるユーザー属性です。
* `Name` は、ユーザーに表示される値です。
* `Value` は、このオプションが選択された場合に要求で返される値です。

### <a name="upload-your-changes"></a>変更のアップロード

1. JSON ファイルの変更が完了したら、テナントに戻ります。
1. **[ユーザー フロー]** を選択し、翻訳を有効にするユーザー フローをクリックします。
1. **[言語]** を選択します。
1. 翻訳する言語を選びます。
1. **属性コレクション ページ** を選択します。
1. フォルダー アイコンを選び、アップロードする JSON ファイルを選びます。

変更がユーザー フローに自動的に保存されます。

## <a name="additional-information"></a>関連情報

### <a name="page-ui-customization-labels-as-overrides"></a>オーバーライドとしてのページ UI カスタマイズ ラベル

言語のカスタマイズを有効にすると、ページ UI のカスタマイズを使った、ラベルの以前の編集内容は、英語 (en) の JSON ファイルに保存されます。 引き続き、言語のカスタマイズで言語リソースをアップロードして、ラベルなどの文字列を変更できます。

### <a name="up-to-date-translations"></a>翻訳を更新する

Microsoft は、最新の翻訳を提供するよう努めています。 継続的に翻訳を改善し、コンプライアンスを維持します。 バグやグローバルな用語の変更を特定し、ユーザー フローにシームレスに適用される更新を行います。

### <a name="support-for-right-to-left-languages"></a>右から左へ記述する言語のサポート

現在、右から左に書く言語はサポートしていません。 これには、カスタム ロケールと CSS を使用して、文字列の表示方法を変更することで対応できます。 この機能が必要な場合は、[Azure フィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)で投票してください。

### <a name="social-identity-provider-translations"></a>ソーシャル ID プロバイダーの翻訳

Microsoft は、`ui_locales` OIDC パラメーターをソーシャル ログインに提供しています。 ただし、Facebook や Google などの一部のソーシャル ID プロバイダーは、それを利用していません。

### <a name="browser-behavior"></a>ブラウザーの動作

Chrome と Firefox はどちらも、それぞれに設定されている言語を要求します。 サポートされている言語の場合は、既定値の前に表示されます。 Microsoft Edge は現在、言語を要求せず、既定の言語が表示されます。

## <a name="supported-languages"></a>サポートされている言語

Azure AD では、以下の言語がサポートされています。 ユーザー フローの言語は、Azure AD によって提供されます。 Multi-Factor Authentication (MFA) 通知の言語は、[Azure AD MFA](../authentication/concept-mfa-howitworks.md) によって提供されます。

| Language              | 言語コード | ユーザー フロー         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| アラビア語                | ar            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ブルガリア語             | bg            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ベンガル語                | bn            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| カタロニア語               | ca            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| チェコ語                 | cs            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| デンマーク語                | da            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ドイツ語                | de            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ギリシャ語                 | el            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| 英語               | en            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| スペイン語               | es            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| エストニア語              | et            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| バスク語                | eu            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| フィンランド語               | fi            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| フランス語                | fr            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ガリシア語              | gl            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| グジャラート語              | gu            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| ヘブライ語                | he            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ヒンディー語                 | hi            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| クロアチア語              | hr            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ハンガリー語             | hu            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| インドネシア語            | id            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| イタリア語               | it            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| 日本語              | ja            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| カザフ語                | kk            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| カンナダ語               | kn            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| 韓国語                | ko            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| リトアニア語            | lt            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ラトビア語               | lv            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| マラヤーラム語             | ml            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| マラーティー語               | mr            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| マレー語                 | ms            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ノルウェー語 - ブークモール      | nb            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| オランダ語                 | nl            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ノルウェー語             | no            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| パンジャーブ語               | pa            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| ポーランド語                | pl            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ポルトガル語 - ブラジル   | pt-br         | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ポルトガル語 - ポルトガル | pt-pt         | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ルーマニア語              | ro            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ロシア語               | ru            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| スロバキア語                | sk            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| スロベニア語             | sl            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| セルビア語 - キリル    | sr-cryl-cs    | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| セルビア語 - ラテン       | sr-latn-cs    | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| スウェーデン語               | sv            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| タミル語                 | ta            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| テルグ語                | te            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) |
| タイ語                  | th            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| トルコ語               | tr            | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ウクライナ語             | uk            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| ベトナム語            | vi            | ![X はサポートされないことを示します。](./media/user-flow-customize-language/no.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| 中国語 - 簡体字  | zh-hans       | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |
| 中国語 - 繁体字 | zh-hant       | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) | ![緑色のチェック マーク。](./media/user-flow-customize-language/yes.png) |