---
title: Translator Text API V2.0
titleSuffix: Azure Cognitive Services
description: V2.0 Translator Text API のリファレンス ドキュメント。
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: 961dd277034db7e5406e671233f26b4fd8fe5f26
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527287"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> Translator Text API のこのバージョンは非推奨になっています。 Translator Text API v3 のドキュメントは、[こちら](v3-0-reference.md)をご覧ください。

Translator Text API V2 は、アプリケーション、Web サイト、ツール、またはその他のソリューションにシームレスに統合して、多言語ユーザー エクスペリエンスを提供することができます。 業界標準を活用することで、任意のハードウェア プラットフォーム上で使用でき、また任意のオペレーティング システムを使用して、言語翻訳や、テキストの言語の検出やテキスト読み上げなど、その他の言語に関連する操作を実行できます。 Microsoft Translator API の詳細については、ここをクリックしてください。

## <a name="getting-started"></a>使用の開始
Translator Text API にアクセスするには、[Microsoft Azure にサインアップ](../translator-text-how-to-signup.md)する必要があります。

## <a name="authorization"></a>Authorization
Translator Text API への呼び出しでは、いずれも認証にサブスクリプション キーが必要です。 API では、2 つの認証モードがサポートされています。

* アクセス トークンを使用します。 **ステップ** 9 で参照されているサブスクリプション キーを使用して、承認サービスに POST 要求を行うことで、アクセス トークンを生成します。 詳細については、トークン サービスに関するドキュメントを参照してください。 Authorization ヘッダーまたは access_token クエリ パラメーターを使用して、アクセス トークンを Translator サービスに渡します。 アクセス トークンは 10 分間有効です。 10 分ごとに新しいアクセス トークンを取得し、このような 10 分以内で繰り返す要求に対して同じアクセス トークンを使い続けます。

* サブスクリプション キーを直接使用します。 サブスクリプション キーを、Translator API への要求に含まれる `Ocp-Apim-Subscription-Key` ヘッダー内の値として渡します。 このモードでは、アクセス トークンを生成するために認証トークン サービスを呼び出す必要はありません。

サブスクリプション キーとアクセス トークンはシークレットと見なし、非表示にする必要があります。

## <a name="profanity-handling"></a>不適切な表現の処理
通常、Translator サービスでは、ソースに存在する不適切な表現は翻訳でも保たれます。 不適切な表現の程度や、言葉に不敬な意味を込めるコンテキストは文化によって異なります。結果として、ターゲット言語での不適切な表現の程度は強められることもあれば、弱められることもあります。

ソース テキスト内での不適切な表現の有無に関係なく、翻訳で不適切な表現が生じるのを防ぐには、不適切な表現のフィルター処理オプションを、それをサポートしているメソッドで使用できます。 このオプションを使用すると、不適切な表現が削除されているか、適切なタグでマークされているかどうかを確認する、あるいはアクションを起こさないかを選択できます。 `ProfanityAction` に指定できる値は、`NoAction` (既定値)、Marked、`Deleted` です。


|ProfanityAction    |Action |サンプル ソース (日本語)  |翻訳の例 (英語)  |
|:--|:--|:--|:--|
|NoAction   |[既定]。 オプションを設定しない場合と同じです。 不適切な表現はソースからターゲットに渡されます。        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |不適切な単語は XML タグ \<profanity> および \</profanity> によって囲まれます。       |彼はジャッカスです。 |He is a \<profanity>jackass\</profanity>.  |
|Deleted    |不適切な単語は、置換されずに出力から削除されます。     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>翻訳からのコンテンツの除外
HTML (`contentType=text/html`) などのタグを持つコンテンツを翻訳するときに、翻訳から特定のコンテンツを除外する方が便利な場合があります。 属性 `class=notranslate` を使用すると、元の言語のまま残す必要があるコンテンツを指定できます。 次の例では、1 番目の `div` 要素内のコンテンツは翻訳されませんが、2 番目の `div` 要素内のコンテンツは翻訳されます。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>実装に関するメモ
1 つの言語から別の言語にテキスト文字列を翻訳します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/Translate` です。

**戻り値:** 翻訳されたテキストを表す文字列です。

以前に `AddTranslation` または `AddTranslationArray` を使用して、同じソース文に対して評価 5 以上の翻訳を入力した場合、`Translate` はシステムで使用可能な最上位の選択肢のみを返します。 "同じソース文" とは、大文字と小文字、空白文字、タグ値、文末の句読点を除き、まったく同じ (100% 一致) であることを意味します。 5 以上の評価が格納されていない場合、返される結果は、Microsoft Translator による自動翻訳になります。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application/xml 

### <a name="parameters"></a>parameters

|パラメーター|値|説明    |パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid  |(空)    |必須。 Authorization ヘッダーまたは Ocp-Apim-Subscription-Key ヘッダーを使用する場合は、appid フィールドを空白のままにするか、"Bearer" + " " + "access_token" を含む文字列を含めます。|query|string|
|text|(空)   |必須。 翻訳するテキストを表す文字列。 テキストのサイズは、10,000 文字を超えてはいけません。|query|文字列|
|from|(空)   |省略可能。 翻訳テキストの言語コードを表す文字列。 たとえば、en は英語を表します。|query|文字列|
|to|(空) |必須。 テキストの翻訳先の言語コードを表す文字列。|query|string|
|contentType|(空)    |省略可能。 翻訳されるテキストの形式。 サポートされている形式は、text/plain (既定値) および text/html です。 HTML の場合は、適切な形式の完全な要素である必要があります。|query|string|
|category|(空)   |省略可能。 翻訳のカテゴリ (ドメイン) を含む文字列。 既定値は "general" です。|query|文字列|
|Authorization|(空)  |appid フィールドまたは Ocp-Apim-Subscription-Key ヘッダーが指定されていない場合は必須。 認証トークン:"Bearer" + " " + "access_token"。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |appid フィールドまたは Authorization ヘッダーが指定されていない場合は必須。|header|文字列|


### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー X-MS-Trans-Info に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>実装に関するメモ
`TranslateArray` メソッドを使用して、複数のソース テキストに対する翻訳を取得します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray` です。

要求本文の形式は、次のようにする必要があります。

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

`TranslateArrayRequest` 内の要素は次のとおりです。


* `appid`:必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。
* `from`:省略可能。 テキストの翻訳元の言語コードを表す文字列。 空のままにすると、応答に自動検出の言語の結果が含まれます。
* `options`:省略可能。 以下に示す値を含む `Options` オブジェクト。 これらはすべて省略可能で、最も一般的な設定が既定値です。 指定された要素は、アルファベット順に一覧表示する必要があります。
    - `Category`:翻訳のカテゴリ (ドメイン) を含む文字列。 既定値は `general` です。
    - `ContentType`:翻訳されるテキストの形式。 サポートされている形式は、`text/plain` (既定値)、`text/xml`、`text/html` です。 HTML の場合は、適切な形式の完全な要素である必要があります。
    - `ProfanityAction`:上記で説明したように、不適切な表現を処理する方法を指定します。 `ProfanityAction` に指定できる値は、`NoAction` (既定値)、`Marked`、`Deleted` です。
    - `State`:要求と応答を関連付けるのに役立つユーザー状態。 応答では同じ内容が返されます。
    - `Uri`:この URI で結果をフィルター処理します。 既定値: `all`。
    - `User`:このユーザーで結果をフィルター処理します。 既定値: `all`。
* `texts`:必須。 翻訳のためのテキストを格納する配列。 すべての文字列が同じ言語である必要があります。 翻訳するすべてのテキストの合計が、10,000 文字を超えてはいけません。 配列要素の最大数は 2,000 です。
* `to`:必須。 テキストの翻訳先の言語コードを表す文字列。

省略可能な要素は省略できます。 TranslateArrayRequest の直接の子である要素は、アルファベット順に一覧表示する必要があります。

TranslateArray メソッドは、`Content-Type` に `application/xml` または `text/xml` を受け入れます。

**戻り値:**`TranslateArrayResponse` 配列。 各 `TranslateArrayResponse` には次の要素があります。

* `Error`:エラーが発生した場合に示します。 それ以外の場合は null に設定されます。
* `OriginalSentenceLengths`:元のソース テキスト内の各文の長さを示す整数の配列。 配列の長さは、文の数を示します。
* `TranslatedText`:翻訳されたテキスト。
* `TranslatedSentenceLengths`:翻訳されたテキスト内の各文の長さを示す整数の配列。 配列の長さは、文の数を示します。
* `State`:要求と応答を関連付けるのに役立つユーザー状態。 要求と同じコンテンツを返します。

応答本文の形式は次のとおりです。

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
成功した応答には、`TranslateArrayResponse` の配列が上で説明した形式で含まれています。

string

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|Authorization|(空) |appid フィールドまたは Ocp-Apim-Subscription-Key ヘッダーが指定されていない場合は必須。 認証トークン:"Bearer" + " " + "access_token"。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|appid フィールドまたは Authorization ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード   |理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。 一般的なエラーの理由は、次のとおりです。 <ul><li>配列要素を空にすることはできない</li><li>無効なカテゴリ</li><li>翻訳元の言語が無効</li><li>翻訳先の言語が無効</li><li>要求に含まれている要素が多すぎる</li><li>翻訳元の言語がサポートされていない</li><li>翻訳先の言語がサポートされていない</li><li>翻訳要求に含まれるデータが多すぎる</li><li>HTML が正しい形式ではない</li><li>翻訳要求で渡された文字列が多すぎた</li></ul>|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー X-MS-Trans-Info に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>実装に関するメモ
パラメーター `languageCodes` として渡され、渡されたロケールの言語を使用してローカライズされる言語の表示名を取得します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames` です。

要求本文には、表示名を取得する ISO 639-1 言語コードを表す文字列配列が含まれています。 例: 

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**戻り値:** Translator サービスでサポートされ、要求された言語にローカライズされる言語名を含む文字列配列。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
Translator サービスでサポートされ、要求された言語にローカライズされる言語名を含む文字列配列。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|locale|(空) |必須。 言語に関連付けられている ISO 639 の小文字 2 文字のカルチャ コードと、言語名をローカライズする ISO 3166 の大文字 2 文字のサブカルチャ コードまたは ISO 639 自体の小文字のカルチャ コードの組み合わせを表す文字列。|query|文字列|
|Authorization|(空)  |appid フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |appid フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー X-MS-Trans-Info に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>実装に関するメモ
翻訳サービスでサポートされている言語を表す言語コードの一覧を取得します。  `Translate` および `TranslateArray` は、これらの言語のうち、任意の 2 つの言語間を翻訳できます。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate` です。

**戻り値:** Translator サービスによってサポートされている言語コードを含む文字列配列。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
Translator サービスによってサポートされている言語コードを含む文字列配列。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|文字列|
|Authorization|(空)  |`appid` フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー X-MS-Trans-Info に含まれている要求 ID をお知らせください。|
|503|サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>実装に関するメモ
音声合成に使用できる言語を取得します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak` です。

**戻り値:** Translator サービスによって音声合成がサポートされている言語コードを含む文字列配列。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
Translator サービスによって音声合成がサポートされている言語コードを含む文字列配列。

string

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|文字列|
|Authorization|(空)|`appid` フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|
 
### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400|無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401|無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID を提供してください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>実装に関するメモ
目的の言語で読み上げられる、渡されたテキストの wave または mp3 ストリームを返します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/Speak` です。

**戻り値:** 目的の言語で話されている、渡されたテキストの wave または mp3 ストリーム。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

binary

応答コンテンツ タイプ: application/xml 

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|text|(空)   |必須。 wave ストリームの読み上げに指定されている言語の文または文章を含む文字列。 読み上げるテキストのサイズは、2,000 文字以内にする必要があります。|query|string|
|language|(空)   |必須。 テキストを読み上げるサポートされる言語コードを表す文字列。 コードは、`GetLanguagesForSpeak` メソッドから返されるコードの一覧に存在している必要があります。|query|文字列|
|format|(空)|省略可能。 content-type ID を指定する文字列。 現在、`audio/wav` と `audio/mp3` が使用できます。 既定値は `audio/wav` です。|query|string|
|options|(空)    |<ul><li>省略可能。 次の合成音声のプロパティを指定する文字列。<li>`MaxQuality` と `MinSize` は、音声信号の品質を指定するために使用できます。 `MaxQuality` を使用すると、最高品質と音声を取得することができ、`MinSize` を使用すると、最小サイズの音声を取得することができます。 既定値は `MinSize` です。</li><li>`female` と `male` は、音声の性別を指定できます。 既定値は `female` です。 垂直バー <code>\|</code> を使用して複数のオプションを含めます。 例: `MaxQuality|Male`。</li></li></ul> |query|文字列|
|Authorization|(空)|`appid` フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID を提供してください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>実装に関するメモ
`Detect` メソッドを使用して、選択したテキストの一部の言語を識別します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/Detect` です。

**戻り値:** 指定したテキストの 2 文字の言語コードを含む文字列。 。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)  |必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|text|(空)|必須。 言語が識別されるいくつかのテキストを含む文字列。 テキストのサイズは、10,000 文字を超えてはいけません。|query| 文字列|
|Authorization|(空)|`appid` フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key  |(空)    |`appid` フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400|無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID を提供してください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>実装に関するメモ
`DetectArray` メソッドを使用して、文字列の配列の言語を一度に識別します。 個々の配列要素の独立した検出を実行し、配列の各行に対して結果を返します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/DetectArray` です。

要求本文の形式は、次のようにする必要があります。

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

テキストのサイズは、10,000 文字を超えてはいけません。

**戻り値:** 入力配列の各行の 2 文字の言語コードを含む文字列配列。

応答本文の形式は次のとおりです。

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
DetectArray が成功しました。 入力配列の各行の 2 文字の言語コードを含む文字列配列を返します。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|文字列|
|Authorization|(空)|`appid` フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドまたは Authorization ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー X-MS-Trans-Info に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>実装に関するメモ

> [!IMPORTANT]
> **非推奨のお知らせ:** 2018 年 1 月 31 日以降は、このメソッドは新しい文の送信を受け付けなくなり、エラー メッセージが表示されます。 コラボレーション翻訳関数への変更に関するこのお知らせを参照してください。

翻訳メモリに翻訳を追加します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation` です。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型   |
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|originalText|(空)|必須。 翻訳元のテキストを含む文字列。 文字列の最大長は 1,000 文字です。|query|string|
|translatedText|(空) |必須。 ターゲット言語で翻訳されたテキストを含む文字列。 文字列の最大長は 2,000 文字です。|query|文字列|
|from|(空)   |必須。 翻訳テキストの言語コードを表す文字列。 en = 英語、de = ドイツ語など。|query|文字列|
|to|(空)|必須。 テキストの翻訳先の言語コードを表す文字列。|query|string|
|評価|(空) |省略可能。 この文字列の品質評価を表す整数。 値の範囲は -10 から 10 です。 既定値は 1 です。|query|integer|
|contentType|(空)    |省略可能。 翻訳されるテキストの形式。 サポートされている形式は、"text/plain" と "text/html" です。 HTML の場合は、適切な形式の完全な要素である必要があります。   |query|string|
|category|(空)|省略可能。 翻訳のカテゴリ (ドメイン) を含む文字列。 既定値は "general" です。|query|string|
|user|(空)|必須。 送信者を追跡するのに使用される文字列。|query|string|
|uri|(空)|省略可能。 この翻訳のコンテンツの場所を含む文字列。|query|文字列|
|Authorization|(空)|appid フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。    |header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|410|AddTranslation は現在サポートされていません。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー X-MS-Trans-Info に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>実装に関するメモ

> [!IMPORTANT]
> **非推奨のお知らせ:** 2018 年 1 月 31 日以降は、このメソッドは新しい文の送信を受け付けなくなり、エラー メッセージが表示されます。 コラボレーション翻訳関数への変更に関するこのお知らせを参照してください。

翻訳メモリを追加するために翻訳の配列を追加します。 これは `AddTranslation` の配列バージョンです。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray` です。

要求本文の形式は次のとおりです。

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

AddtranslationsRequest 要素内の要素は次のとおりです。

* `AppId`:必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。
* `From`:必須。 ソース言語の言語コードを含む文字列。 `GetLanguagesForTranslate` メソッドによって返されるいずれかの言語である必要があります。
* `To`:必須。 ターゲット言語の言語コードを含む文字列。 `GetLanguagesForTranslate` メソッドによって返されるいずれかの言語である必要があります。
* `Translations`:必須。 翻訳メモリに追加する翻訳の配列。 各翻訳には、originalText、translatedText、rating を含める必要があります。 originalText と translatedText のサイズは、それぞれ 1,000 文字に制限されます。 originalText と translatedText のすべての合計は、10,000 文字以内にする必要があります。 配列要素の最大数は 100 です。
* `Options`:必須。 オプションのセット (Category、ContentType、Uri、User など)。 User は必須です。 Category、ContentType、Uri は省略可能です。 指定された要素は、アルファベット順に一覧表示する必要があります。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
AddTranslationArray メソッドが正常に完了しました。 2018 年 2 月 1 日以降は、文の送信は受け付けられません。 サービスでエラー コード 410 が返されます。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|Authorization|(空)|appid フィールドまたは Ocp-Apim-Subscription-Key ヘッダーが指定されていない場合は必須。 認証トークン:"Bearer" + " " + "access_token"。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|appid フィールドまたは Authorization ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|410    |AddTranslation は現在サポートされていません。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID を提供してください。|
|503|サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>実装に関するメモ
テキストの一部を複数の文に分割し、各文の長さを含む配列を返します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences` です。

**戻り値:** 文の長さを表す整数の配列。 配列の長さは文の数であり、値は各文の長さです。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
文の長さを表す整数の配列。 配列の長さは文の数であり、値は各文の長さです。

integer

応答コンテンツ タイプ: application/xml 

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)  |必須。 Authorization ヘッダーまたは Ocp-Apim-Subscription-Key ヘッダーを使用する場合は、appid フィールドを空白のままにするか、"Bearer" + " " + "access_token" を含む文字列を含めます。|query| string|
|text|(空)   |必須。 複数の文に分割するテキストを表す文字列。 テキストのサイズは、10,000 文字を超えてはいけません。|query|string|
|language   |(空)    |必須。 入力テキストの言語コードを表す文字列。|query|文字列|
|Authorization|(空)|appid フィールドまたは Ocp-Apim-Subscription-Key ヘッダーが指定されていない場合は必須。 認証トークン:"Bearer" + " " + "access_token"。    |header|文字列|
|Ocp-Apim-Subscription-Key|(空)|appid フィールドまたは Authorization ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400|無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401|無効な資格情報|
|500|サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー X-MS-Trans-Info に含まれている要求 ID をお知らせください。|
|503|サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>実装に関するメモ
ストアと MT エンジンから、指定した言語ペアの翻訳の配列を取得します。 GetTranslations は、使用可能なすべての翻訳を返すため、Translate とは異なります。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations` です。

要求の本文には、次の形式の省略可能な TranslationOptions オブジェクトが含まれています。

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions` オブジェクトには、以下に示す値が含まれます。 これらはすべて省略可能で、最も一般的な設定が既定値です。 指定された要素は、アルファベット順に一覧表示する必要があります。

* `Category`:翻訳のカテゴリ (ドメイン) を含む文字列。 既定値は "general" です。
* `ContentType`:唯一サポートされていて、既定値となっているオプションは "text/plain" です。
* `IncludeMultipleMTAlternatives`: MT エンジンから複数の翻訳候補を返すかどうかを決定するブール型のフラグ。 有効な値は true および false (大文字と小文字を区別) です。 既定値は false で、翻訳候補が 1 つだけ含まれます。 フラグを true に設定すると、コラボレーション翻訳フレームワーク (CTF) と完全に統合されている翻訳に、人工的な翻訳候補を生成することができます。 この機能は、デコーダーの n-best リストから人工的な翻訳候補を追加して、CTF には翻訳候補がない文章の翻訳候補を返すことができます。
    - 評価: 評価は、次のように適用されます。1) 最善の自動翻訳は評価 5 になります。 2) CTF からの翻訳候補は、レビュー担当者の権限を、-10 から + 10 で反映します。 3) 自動的に生成された (n-best) 翻訳候補の評価は 0 になり、一致度は 100 になります。
    - 翻訳候補の数: 返される翻訳候補の数は最大で maxTranslations までですが、それより少ない場合もあります。
    - 言語ペア: この機能は、簡体字中国語と繁体字中国語の間では、翻訳方向を問わず使用できません。 Microsoft Translator がサポートしている他のすべての言語ペアで使用できます。
* `State`:要求と応答を関連付けるのに役立つユーザー状態。 応答では同じ内容が返されます。
* `Uri`:この URI で結果をフィルター処理します。 値を設定しなかった場合は、既定値として all が使用されます。
* `User`:このユーザーで結果をフィルター処理します。 値を設定しなかった場合は、既定値として all が使用されます。

要求 `Content-Type` は `text/xml` にする必要があります。

**戻り値:** 応答の形式は次のとおりです。

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

これには、次の値を含む `GetTranslationsResponse` 要素が含まれています。

* `Translations`:一致が見つかり、TranslationMatch (下記参照) オブジェクトに格納されている配列。 翻訳には、元のテキストとのわずかな違い (あいまい一致) が含まれている場合があります。 翻訳は、次の順に並べ替えられます:最初に 100% 一致、次にあいまい一致。
* `From`:メソッドで翻訳元の言語を指定しなかった場合、これが自動言語検出の結果になります。 それ以外の場合は、指定された翻訳元の言語になります。
* `State`:要求と応答を関連付けるのに役立つユーザー状態。 TranslateOptions パラメーターで指定されているのと同じ値が含まれています。

TranslationMatch オブジェクトは、以下で構成されます。

* `Error`:特定の入力文字列でエラーが発生している場合、そのエラー コードが格納されます。 それ以外の場合は、このフィールドは空です。
* `MatchDegree`:システムにより、不正確な一致を含むストアに対して入力文が照合されます。  MatchDegree は、入力テキストがストアで見つかった元のテキストにどのくらい一致しているかを示します。 返される値の範囲は 0 から 100 です。0 は類似性なしで、100 は大文字と小文字が完全に一致します。
MatchedOriginalText:この結果の一致した元のテキストです。 一致した元のテキストが、入力されたテキストと異なる場合にのみ返されます。 あいまい一致のソース テキストを返すために使用されます。 Microsoft Translator の結果に対しては返されません。
* `Rating`:品質を決定する人の権限を示します。 機械翻訳の結果は、評価 5 になります。 匿名で提供された翻訳が通常、評価 1 から 4 であるのに対し、信頼性のある提供元からの翻訳は通常、評価 6 から 10 になります。
* `Count`:この評価のこの翻訳が選択された回数。 自動的に翻訳された応答の値は 0 になります。
* `TranslatedText`:翻訳されたテキスト。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
上で説明した形式の `GetTranslationsResponse` オブジェクト。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|text|(空)|必須。 翻訳するテキストを表す文字列。 テキストのサイズは、10,000 文字を超えてはいけません。|query|文字列|
|from|(空)|必須。 翻訳テキストの言語コードを表す文字列。|query|文字列|
|to |(空)    |必須。 テキストの翻訳先の言語コードを表す文字列。|query|string|
|maxTranslations|(空)|必須。 返す翻訳結果の最大数を表す整数。|query|integer|
|Authorization| (空)|`appid` フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|string| header|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID を提供してください。|
|503|サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>実装に関するメモ
`GetTranslationsArray` メソッドを使用して、複数のソース テキストに対する複数の翻訳候補を取得します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray` です。

要求本文の形式は次のとおりです。

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` には次の要素が含まれます。

* `AppId`:必須。 Authorization ヘッダーを使用している場合は、appid フィールドは空のままにするか、`"Bearer" + " " + "access_token"` を含む文字列を含めます。
* `From`:必須。 翻訳テキストの言語コードを表す文字列。
* `MaxTranslations`:必須。 返す翻訳結果の最大数を表す整数。
* `Options`:省略可能。 以下に示す値を含む Options オブジェクト。 これらはすべて省略可能で、最も一般的な設定が既定値です。 指定された要素は、アルファベット順に一覧表示する必要があります。
    - Category`:翻訳のカテゴリ (ドメイン) を含む文字列。 既定値は "general" です。
    - `ContentType`:唯一サポートされていて、既定値となっているオプションは "text/plain" です。
    - `IncludeMultipleMTAlternatives`: MT エンジンから複数の翻訳候補を返すかどうかを決定するブール型のフラグ。 有効な値は true および false (大文字と小文字を区別) です。 既定値は false で、翻訳候補が 1 つだけ含まれます。 フラグを true に設定すると、コラボレーション翻訳フレームワーク (CTF) と完全に統合されている翻訳に、人工的な翻訳候補を生成することができます。 この機能は、デコーダーの n-best リストから人工的な翻訳候補を追加して、CTF には翻訳候補がない文章の翻訳候補を返すことができます。
        - 評価: 評価は、次のように適用されます。1) 最善の自動翻訳は評価 5 になります。 2) CTF からの翻訳候補は、レビュー担当者の権限を、-10 から + 10 で反映します。 3) 自動的に生成された (n-best) 翻訳候補の評価は 0 になり、一致度は 100 になります。
        - 翻訳候補の数: 返される翻訳候補の数は最大で maxTranslations までですが、それより少ない場合もあります。
        - 言語ペア: この機能は、簡体字中国語と繁体字中国語の間では、翻訳方向を問わず使用できません。 Microsoft Translator がサポートしている他のすべての言語ペアで使用できます。
* `State`:要求と応答を関連付けるのに役立つユーザー状態。 応答では同じ内容が返されます。
* `Uri`:この URI で結果をフィルター処理します。 値を設定しなかった場合は、既定値として all が使用されます。
* `User`:このユーザーで結果をフィルター処理します。 値を設定しなかった場合は、既定値として all が使用されます。
* `Texts`:必須。 翻訳のためのテキストを格納する配列。 すべての文字列が同じ言語である必要があります。 翻訳するすべてのテキストの合計が、10,000 文字を超えてはいけません。 配列要素の最大数は 10 です。
* `To`:必須。 テキストの翻訳先の言語コードを表す文字列。

省略可能な要素は省略できます。 `GetTranslationsArrayRequest` の直接の子である要素は、アルファベット順に一覧表示する必要があります。

要求 `Content-Type` は `text/xml` にする必要があります。

**戻り値:** 応答の形式は次のとおりです。

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

各 `GetTranslationsResponse` 要素には、次の値が含まれています。

* `Translations`:一致が見つかり、`TranslationMatch` (下記参照) オブジェクトに格納されている配列。 翻訳には、元のテキストとのわずかな違い (あいまい一致) が含まれている場合があります。 翻訳は、次の順に並べ替えられます:最初に 100% 一致、次にあいまい一致。
* `From`:メソッドで `From` 言語を指定しなかった場合、これが自動言語検出の結果になります。 それ以外の場合は、指定された翻訳元の言語になります。
* `State`:要求と応答を関連付けるのに役立つユーザー状態。 `TranslateOptions` パラメーターで指定されているのと同じ値が含まれています。

`TranslationMatch` オブジェクトは、以下で構成されます。
* `Error`:特定の入力文字列でエラーが発生している場合、そのエラー コードが格納されます。 それ以外の場合は、このフィールドは空です。
* `MatchDegree`:システムにより、不正確な一致を含むストアに対して入力文が照合されます。  `MatchDegree` は、入力テキストがストアで見つかった元のテキストにどのくらい一致しているかを示します。 返される値の範囲は 0 から 100 です。0 は類似性なしで、100 は大文字と小文字が完全に一致します。
* `MatchedOriginalText`:この結果の一致した元のテキストです。 一致した元のテキストが、入力されたテキストと異なる場合にのみ返されます。 あいまい一致のソース テキストを返すために使用されます。 Microsoft Translator の結果に対しては返されません。
* `Rating`:品質を決定する人の権限を示します。 機械翻訳の結果は、評価 5 になります。 匿名で提供された翻訳が通常、評価 1 から 4 であるのに対し、信頼性のある提供元からの翻訳は通常、評価 6 から 10 になります。
* `Count`:この評価のこの翻訳が選択された回数。 自動的に翻訳された応答の値は 0 になります。
* `TranslatedText`:翻訳されたテキスト。


### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|Authorization  |(空)    |`appid` フィールドまたは `Ocp-Apim-Subscription-Key` ヘッダーが指定されていない場合は必須。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドまたは `Authorization` ヘッダーが指定されていない場合は必須。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID を提供してください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [v3 Translator Text API への移行](../migrate-to-v3.md)










