---
title: "Azure Active Directory B2C: 言語のカスタマイズの使用 | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: 言語のカスタマイズの使用

>[!NOTE] 
>この機能はパブリック プレビュー段階にあります。  この機能を使用する場合は、テスト テナントを使用することをお勧めします。  プレビューから一般公開リリースに切り替える際に互換性に影響する変更を加える予定はありませんが、機能向上のためにそうした変更を加える権利を留保します。  機能を試してみたら、感想や改善案をフィードバックしてください。  フィードバックは、Azure Portal の右上にあるスマイル アイコン ツールを使用して送信できます。   プレビュー段階のこの機能を使用して運用を開始するためのビジネス要件がある場合は、シナリオをお知らせください。適切なガイダンスとサポートを提供します。  [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com) までお問い合わせください。

言語のカスタマイズを使用すると、顧客のニーズに合わせてユーザー体験を異なる言語に変更できます。  36 言語の翻訳を提供しています (「[追加情報](#additional-information)」を参照)。  ページが 1 つの言語でのみ提供されている場合でも、ページ上の任意のテキストをニーズに合わせてカスタマイズできます。  

## <a name="how-does-language-customization-work"></a>言語のカスタマイズのしくみ
言語のカスタマイズを使用すると、ユーザー体験で利用できる言語を選択できます。  この機能を有効にすると、アプリケーションからクエリ文字列パラメーター ui_locales を指定できます。  Azure AD B2C を呼び出すと、ページが指定したロケールに翻訳されます。  構成により、ユーザー体験の言語を完全に制御し、顧客のブラウザーの言語設定を無視できます。 また、顧客に表示される言語をそこまで制御する必要がない場合もあります。  ui_locales パラメーターを指定しない場合、顧客のエクスペリエンスはブラウザーの設定によって決まります。  その場合でも、ユーザー体験の翻訳先言語を、サポートされる言語として追加することで制御できます。  サポートしない言語を表示するように顧客のブラウザーが設定されている場合は、サポートされているカルチャで既定として選択した言語が代わりに表示されます。

1. **ui-locales で指定した言語** - [言語のカスタマイズ] を有効にすると、ユーザー体験はここで指定された言語に翻訳されます
2. **ブラウザーが要求した言語** - ui-locales が指定されていない場合は、ブラウザーが要求した言語に翻訳されます (**サポートされている言語に含まれている場合**)
3. **ポリシーの既定の言語** - ブラウザーで言語が指定されていない場合、またはサポートされていない言語が指定されている場合は、ポリシーの既定の言語に翻訳されます

>[!NOTE]
>カスタム ユーザー属性を使用している場合は、独自の翻訳を提供する必要があります。 詳細については、「[文字列のカスタマイズ](#customize-your-strings)」を参照してください。
>

## <a name="support-uilocales-requested-languages"></a>ui_locales で要求した言語のサポート 
ポリシーで [言語のカスタマイズ] を有効にし、ui_locales パラメーターを追加することで、ユーザー体験の言語を制御できるようになりました。
1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します。](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. 翻訳を有効にするポリシーに移動します。
3. **[言語のカスタマイズ]** をクリックします。
4. 警告をよく読みます。  [言語のカスタマイズ] は、一度有効にすると、オフにすることはできません。
5. 機能をオンにし、**[OK]** をクリックします。
6. **[ポリシーの編集]** ブレードの左上隅でポリシーを保存します。

## <a name="select-which-languages-your-user-journey-supports"></a>ユーザー体験でサポートする言語の選択 
ui_locales パラメーターを指定していない場合にユーザー体験の翻訳先として許可する言語の一覧を作成します。
1. 前の手順で、ポリシーの [言語のカスタマイズ] を有効にしていることを確認します。
2. **[ポリシーの編集]** ブレードで、**[言語のカスタマイズ]** を選択します。
3. **[Supported languages]\(サポートされている言語\)** ブレードに移動します。  ここでは、**[言語の追加]** を選択できます。
4. サポートする言語をすべて選択します。  

>[!NOTE]
>ui_locales パラメーターが指定されていない場合、ページは顧客のブラウザーの言語に翻訳されます (この一覧に含まれている場合のみ)。
>

5. 下部にある **[OK]** をクリックします。
6. **[言語のカスタマイズ]** ブレードを閉じ、ポリシーを**保存**します。

## <a name="customize-your-strings"></a>文字列のカスタマイズ
[言語のカスタマイズ] を使用すると、ユーザー体験における文字列をカスタマイズできます。
1. 前の手順で、ポリシーの [言語のカスタマイズ] を有効にしていることを確認します。
2. **[ポリシーの編集]** ブレードで、**[言語のカスタマイズ]** を選択します。
3. 左側のナビゲーション メニューで、**[コンテンツのダウンロード]** を選択します。
4. 編集するページを選択します。
5. ドロップダウンで、編集する言語を選択します。
6. **[Download]**をクリックします。 

上記の手順により、文字列の編集を開始する際に使用できる JSON ファイルを入手できます。

### <a name="changing-any-string-on-the-page"></a>ページ上の文字列の変更
1. 前の手順でダウンロードした JSON ファイルを JSON エディターで開きます。
2. 変更する要素を探します。  探している文字列の `StringId` を見つけるか、変更する `Value` を見つけます。
3. `Value` 属性を表示する文字列に更新します。
4. ファイルを保存し、変更内容をアップロードします。

### <a name="changing-extension-attributes"></a>拡張属性の変更
カスタム ユーザー属性の文字列を変更する場合や、JSON に追加する場合は、次の形式を使用します。
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>文字列を上書きする必要がある場合は、必ず `Override` の値を `true` に設定してください。  値が変更されていない場合、そのエントリは無視されます。 
>

`<ExtensionAttribute>` をカスタム ユーザー属性の名前に置き換えます。  
`<ExtensionAttributeValue>` を表示する新しい文字列に置き換えます。

### <a name="using-localizedcollections"></a>LocalizedCollections の使用
応答の値セットの一覧を指定する場合は、`LocalizedCollections` を作成する必要があります。  `LocalizedCollections` は、`Name` と `Value` のペアの配列です。  `Name` は表示される値、`Value` は要求で返される値です。  `LocalizedCollections` を追加するには、次の形式を使用します。

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": false,
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
>[!IMPORTANT]
>文字列を上書きする必要がある場合は、必ず `Override` の値を `true` に設定してください。  値が変更されていない場合、そのエントリは無視されます。 
>

* `ElementId` は、この `LocalizedCollections` が応答になるユーザー属性です
* `Name` は、ユーザーに表示される値です
* `Value` は、このオプションが選択された場合に要求で返される値です

### <a name="upload-your-changes"></a>変更のアップロード
1. JSON ファイルの変更が完了したら、B2C テナントに戻ります。
2. **[ポリシーの編集]** ブレードで、**[言語のカスタマイズ]** を選択します。
3. 左側のナビゲーション メニューで、**[コンテンツのアップロード]** を選択します。
4. 変更内容をアップロードするページを選択します。
5. アップロードする言語の JSON を以前に指定している場合は、以前のエントリを削除する必要があります。  その言語の右側にある [`...`] をクリックし、**[削除]** を選択して、削除できます。
6. 左上の **[追加]** をクリックします。
7. JSON ファイルの言語を選択します。
8. 右側のフォルダー ボタンをクリックし、JSON ファイルを参照します。
9. ブレードの下部にある **[アップロード]** をクリックします。
10. **[ポリシーの編集]** ブレードに戻り、**[保存]** をクリックします。

## <a name="additional-information"></a>追加情報
### <a name="recommendations-for-language-customization"></a>[言語のカスタマイズ] に関する推奨事項
明示的に置き換える文字列の言語リソースのエントリのみを追加することをお勧めします。  すべての JSON 翻訳からコンパイルされたファイルにサイズ制限が適用されます。  ファイルが大きすぎると、ユーザー体験のパフォーマンスに影響します。
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>[言語のカスタマイズ] を有効にすると、ページ UI のカスタマイズ ラベルが削除される
[言語のカスタマイズ] を有効にすると、[ページ UI のカスタマイズ] を使用したラベルの以前の編集内容が、カスタム ユーザー属性を除いて削除されます。  この変更は、文字列を編集できる場所での競合を避けるために行われます。  引き続き、[言語のカスタマイズ] で言語リソースをアップロードして、ラベルなどの文字列を変更できます。
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft は、最新の翻訳を提供するよう努めています。
継続的に翻訳を改善し、コンプライアンスを維持します。  バグやグローバルな用語の変更を特定し、ユーザー体験にシームレスに適用される更新を行います。
### <a name="support-for-right-to-left-languages"></a>右から左へ記述する言語のサポート
右から左へ記述する言語はサポートされていないため、この機能を必要とする場合は、[Azure フィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)でこの機能へのご要望をお寄せください。
### <a name="social-identity-provider-translations"></a>ソーシャル ID プロバイダーの翻訳
Microsoft ではソーシャル ログインに ui_locales OIDC パラメーターを渡していますが、Facebook や Google など、一部のソーシャル ID プロバイダーでは適用されません。 
### <a name="browser-behavior"></a>ブラウザーの動作
Chrome と Firefox はどちらもそれぞれの設定言語を要求し、その言語がサポートされている場合は、既定よりも優先して表示されます。  Edge は現在、言語を要求せず、既定の言語が表示されます。
### <a name="known-issues"></a>既知の問題
* 現在、プロファイル編集ポリシーで MFA ページの言語リソースをアップロードすることはできません。
* `LocalizedCollections` は、応答の種類で必須の場合に、値に対して生成されません。
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>サポートされていない言語が必要な場合はどうすればよいのでしょうか。
"カスタム言語" に対する JSON リソースをアップロードできる、この機能の拡張機能の提供を予定しています。  この機能を使用すると、任意の言語の名前と言語コードを指定し、その言語の翻訳を*すべて*提供することができます。  この機能が必要な場合は、シナリオを [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com) にお送りください。  

### <a name="what-languages-are-supported"></a>どの言語がサポートされていますか。

| 言語              | 言語コード |
|-----------------------|---------------|
| バングラ語                | bn            |
| チェコ語                 | cs            |
| デンマーク語                | da            |
| ドイツ語                | de            |
| ギリシャ語                 | el            |
| 英語               | en            |
| スペイン語               | es            |
| フィンランド語               | fi            |
| フランス語                | fr            |
| グジャラート語              | gu            |
| ヒンディー語                 | hi            |
| クロアチア語              | hr            |
| ハンガリー語             | hu            |
| イタリア語               | it            |
| 日本語              | ja            |
| カンナダ語               | kn            |
| 韓国語                | ko            |
| マラヤーラム語             | ml            |
| マラーティー語               | mr            |
| マレー語                 | ms            |
| ノルウェー語 - ブークモール      | nb            |
| オランダ語                 | nl            |
| パンジャーブ語               | pa            |
| ポーランド語                | pl            |
| ポルトガル語 - ブラジル   | pt-br         |
| ポルトガル語 - ポルトガル | pt-pt         |
| ルーマニア語              | ro            |
| ロシア語               | ru            |
| スロバキア語                | sk            |
| スウェーデン語               | sv            |
| タミール語                 | ta            |
| テルグ語                | te            |
| タイ語                  | th            |
| トルコ語               | tr            |
| 中国語 - 簡体字  | zh-hans       |
| 中国語 - 繁体字 | zh-hant       |

