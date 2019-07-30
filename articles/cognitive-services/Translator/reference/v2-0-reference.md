---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: Translator Text API v2.0 のリファレンス ドキュメント。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: c18c062d5537603284acb37081ac0a4eb8d2fd20
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797809"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> Translator Text API のこのバージョンは非推奨になっています。 [Translator Text API バージョン 3 のドキュメントをご覧ください](v3-0-reference.md)。

Translator Text API バージョン 2 は、アプリ、Web サイト、ツール、またはその他のソリューションにシームレスに統合して、多言語ユーザー エクスペリエンスを提供することができます。 任意のハードウェア プラットフォーム上で使用でき、また任意のオペレーティング システムによって言語の翻訳のほか、テキストの言語検出やテキスト読み上げなど、その他の言語に関連したタスクを業界標準に従って実行できます。 詳細については、[Translator Text API](../translator-info-overview.md) に関するページを参照してください。

## <a name="getting-started"></a>使用の開始
Translator Text API にアクセスするには、[Microsoft Azure にサインアップする](../translator-text-how-to-signup.md)必要があります。

## <a name="authentication"></a>Authentication 
Translator Text API への呼び出しでは、いずれも認証にサブスクリプション キーが必要です。 この API では、3 つの認証方法がサポートされています。

- アクセス トークン。 ステップ 9 で参照されているサブスクリプション キーを使用して、認証サービスに POST 要求を行うことで、アクセス トークンを作成します。 詳細については、トークン サービスに関するドキュメントを参照してください。 `Authorization` ヘッダーまたは `access_token` クエリ パラメーターを使用して、アクセス トークンを Translator サービスに渡します。 アクセス トークンは 10 分間有効です。 10 分ごとに新しいアクセス トークンを取得し、その 10 分間に繰り返される要求に対して同じアクセス トークンを使い続けます。
- 直接使用されるサブスクリプション キー。 サブスクリプション キーを、Translator Text API への要求に含まれる `Ocp-Apim-Subscription-Key` ヘッダー内の値として渡します。 サブスクリプション キーを直接使用する場合、トークン認証サービスを呼び出してアクセス トークンを作成する必要はありません。
- [Azure Cognitive Services のマルチサービスのサブスクリプション](https://azure.microsoft.com/pricing/details/cognitive-services/)。 この方法では、単一の秘密鍵を使用して複数のサービスに対する要求を認証することができます。
マルチサービスの秘密鍵を使用するときは、2 つの認証ヘッダーを要求に含める必要があります。 最初のヘッダーで、秘密鍵を渡します。 2 つ目のヘッダーでは、サブスクリプションに関連付けられているリージョンを指定します。
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

マルチサービスの Text API サブスクリプションではリージョンが必須です。 選択したリージョンが、マルチサービスのサブスクリプション キーを使用する際にテキスト翻訳に使用できる唯一のリージョンとなります。 Azure portal でマルチサービスのサブスクリプションへのサインアップ時に選択したリージョンと同じにする必要があります。

利用可能なリージョンは `australiaeast`、`brazilsouth`、`canadacentral`、`centralindia`、`centraluseuap`、`eastasia`、`eastus`、`eastus2`、`japaneast`、`northeurope`、`southcentralus`、`southeastasia`、`uksouth`、`westcentralus`、`westeurope`、`westus`、`westus2` です。

サブスクリプション キーとアクセス トークンは、非表示で扱う必要のあるシークレットです。

## <a name="profanity-handling"></a>不適切な表現の処理
通常、Translator サービスでは、ソース内に存在する不適切な表現がそのまま維持されます。 不適切な表現の程度と、単語を不適切にしているコンテキストは、文化により異なります。 そのため、対象言語における不適切な表現の程度は増減する可能性があります。

ソース テキスト内に不適切な表現があっても、翻訳に含まれないようにする場合は、不適切な表現のフィルター処理オプションをサポートしているメソッドで、そのオプションを使用できます。 このオプションを使用すると、不適切な表現が削除されているか、適切なタグでマークされていることを確認するかどうか、ターゲットでの不適切な表現を許可するかどうかを選択できます。 `ProfanityAction` に指定できる値は、`NoAction` (既定値)、`Marked`、`Deleted` です。


|ProfanityAction    |Action |ソースの例 (日本語)  |翻訳の例 (英語)  |
|:--|:--|:--|:--|
|NoAction   |[既定]。 オプションを設定しない場合と同じです。 不適切な表現はソースからターゲットに渡されます。        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |不適切な単語は XML タグ \<profanity> および \</profanity> によって囲まれます。       |彼はジャッカスです。 |He is a \<profanity>jackass\</profanity>.  |
|Deleted    |不適切な単語は、置換されずに出力から削除されます。     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>翻訳からのコンテンツの除外
HTML (`contentType=text/html`) などのタグが含まれているコンテンツを翻訳するときに、翻訳から特定のコンテンツを除外する方が便利な場合があります。 属性 `class=notranslate` を使用すると、元の言語のまま残す必要があるコンテンツを指定できます。 次の例では、1 番目の `div` 要素内のコンテンツは翻訳されませんが、2 番目の `div` 要素内のコンテンツは翻訳されます。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>実装に関するメモ
1 つの言語から別の言語にテキスト文字列を翻訳します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/Translate` です。

**戻り値:** 翻訳されたテキストを表す文字列。

以前に `AddTranslation` または `AddTranslationArray` を使用して、同じソース文に対して評価 5 以上の翻訳を入力した場合、`Translate` はシステムで使用可能な最上位の選択肢のみを返します。 "同じソース文" とは、大文字と小文字、空白文字、タグ値、文末の句読点を除き、まったく同じ (100% 一致) であることを意味します。 5 以上の評価が格納されていない場合、返される結果は、Microsoft Translator による自動翻訳になります。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明    |パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid  |(空)    |必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|text|(空)   |必須。 翻訳するテキストを表す文字列。 テキストは 10,000 文字以内にする必要があります。|query|文字列|
|from|(空)   |省略可能。 翻訳されるテキストの言語コードを表す文字列。 たとえば、en は英語を表します。|query|文字列|
|to|(空) |必須。 テキストの翻訳先の言語コードを表す文字列。|query|string|
|contentType|(空)    |省略可能。 翻訳されるテキストの形式。 サポートされている形式は、`text/plain` (既定値) と `text/html` です。 HTML 要素は、いずれも整形式の完全な要素にする必要があります。|query|string|
|category|(空)   |省略可能。 翻訳のカテゴリ (ドメイン) を含む文字列。 既定では、 `general`です。|クエリ|文字列|
|Authorization|(空)  |`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|


### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>実装に関するメモ
複数のソース テキストに対する翻訳を取得します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray` です。

要求本文の形式は次のとおりです。

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

`TranslateArrayRequest` には、以下の要素が含まれています。


* `AppId`:必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`AppId` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。
* `From`:省略可能。 翻訳されるテキストの言語コードを表す文字列。 このフィールドを空のままにすると、応答に自動言語検出の結果が含まれます。
* `Options`:省略可能。 以下の値を含む `Options` オブジェクト。 これらはすべて省略可能で、最も一般的な設定が既定値です。 指定された要素は、アルファベット順に一覧表示する必要があります。
    - `Category`:翻訳のカテゴリ (ドメイン) を含む文字列。 既定では、 `general`です。
    - `ContentType`:翻訳されるテキストの形式。 サポートされている形式は、`text/plain` (既定値)、`text/xml`、`text/html` です。 HTML 要素は、いずれも整形式の完全な要素にする必要があります。
    - `ProfanityAction`:前述したように、不適切な表現を処理する方法を指定します。 指定できる値は、`NoAction` (既定値)、`Marked`、`Deleted` です。
    - `State`:要求と応答の関連付けに役立つユーザー状態。 応答では同じ内容が返されます。
    - `Uri`:この URI で結果をフィルター処理します。 既定値: `all`。
    - `User`:このユーザーで結果をフィルター処理します。 既定値: `all`。
* `Texts`:必須。 翻訳用のテキストを含む配列。 すべての文字列が同じ言語である必要があります。 翻訳するすべてのテキストの合計を 10,000 文字以内にする必要があります。 配列要素の最大数は 2,000 です。
* `To`:必須。 テキストの翻訳先の言語コードを表す文字列。

省略可能な要素は省略してかまいません。 `TranslateArrayRequest` の直接の子である要素は、アルファベット順に一覧表示されている必要があります。

`TranslateArray` メソッドでは、`Content-Type` に `application/xml` または `text/xml` を指定できます。

**戻り値:** `TranslateArrayResponse` 配列。 各 `TranslateArrayResponse` に、以下の要素が含まれています。

* `Error`:エラーが発生した場合にそのエラーを示します。 それ以外の場合は null に設定されます。
* `OriginalSentenceLengths`:ソース テキスト内の各文の長さを示す整数の配列。 配列の長さは、文の数を示します。
* `TranslatedText`:翻訳されたテキスト。
* `TranslatedSentenceLengths`:翻訳されたテキスト内の各文の長さを示す整数の配列。 配列の長さは、文の数を示します。
* `State`:要求と応答の関連付けに役立つユーザー状態。 要求と同じ内容を返します。

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
成功した応答には、`TranslateArrayResponse` 配列の 1 つの配列が前述した形式で含まれています。

string

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|Authorization|(空)  |`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード   |理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。 一般的なエラーの理由は、次のとおりです。 <ul><li>配列要素を空にすることはできない。</li><li>無効なカテゴリ。</li><li>翻訳元の言語が無効。</li><li>翻訳先の言語が無効。</li><li>要求に含まれている要素が多すぎる。</li><li>翻訳元の言語がサポートされていない。</li><li>翻訳先の言語がサポートされていない。</li><li>翻訳要求に含まれているデータが多すぎる。</li><li>HTML が正しい形式ではない。</li><li>翻訳要求で渡された文字列が多すぎた。</li></ul>|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>実装に関するメモ
パラメーター `languageCodes` として渡され、渡された `locale` 言語にローカライズされる言語のフレンドリ名を取得します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames` です。

要求本文には、フレンドリ名を取得する ISO 639-1 言語コードを表す文字列配列が含まれます。 次に例を示します。

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
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|locale|(空) |必須。 以下のいずれかを表し、言語名のローカライズに使用される文字列。 <ul><li>言語に関連付けられた 2 文字の ISO 639 小文字カルチャ コードと、2 文字の ISO 3166 大文字サブカルチャ コードの組み合わせ。 <li>単独の ISO 639 小文字カルチャ コード。|query|文字列|
|Authorization|(空)  |`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
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
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|文字列|
|Authorization|(空)  |`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
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
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|文字列|
|Authorization|(空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|
 
### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400|無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401|無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>実装に関するメモ
目的の言語で読み上げられる、渡されたテキストの WAV または MP3 ストリームを返します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/Speak` です。

**戻り値:** 目的の言語で読み上げられる、渡されたテキストの WAV または MP3 ストリーム。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

binary

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|text|(空)   |必須。 指定した言語でストリーム用に読み上げられる文を 1 つ以上含む文字列。 テキストは 2,000 文字を超えないようにしてください。|query|string|
|language|(空)   |必須。 テキストの読み上げに使用する言語のサポートされている言語コードを表す文字列。 このコードは、`GetLanguagesForSpeak` メソッドから返されるいずれかのコードにする必要があります。|query|文字列|
|format|(空)|省略可能。 content-type ID を指定する文字列。 現在、`audio/wav` と `audio/mp3` が使用できます。 既定値は `audio/wav` です。|query|string|
|options|(空)    |省略可能。 以下の合成音声のプロパティを指定する文字列。<ul><li>`MaxQuality` と `MinSize` は、音声信号の品質を指定します。 `MaxQuality` を指定すると、最高品質になります。 `MinSize` を指定すると、最小ファイル サイズになります。 既定値は `MinSize` です。</li><li>`female` と `male` は、音声の性別を指定します。 既定では、 `female`です。 垂直バー (<code>\|</code>) を使用して複数のオプションを含めることができます。 たとえば、`MaxQuality|Male` を使用します。</li></li></ul>  |query|文字列|
|Authorization|(空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>実装に関するメモ
テキストの一部の言語を識別します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/Detect` です。

**戻り値:** 対象テキストの 2 文字の言語コードを含む文字列。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)  |必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|text|(空)|必須。 言語が識別されるテキストを含む文字列。 テキストは 10,000 文字を超えないようにしてください。|query|  文字列|
|Authorization|(空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key  |(空)    |`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400|無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>実装に関するメモ

文字列配列内の言語を識別します。 個々の配列要素の言語を個別に検出し、配列の各行に対して結果を返します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/DetectArray` です。

要求本文の形式は次のとおりです。

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

テキストは 10,000 文字以内にする必要があります。

**戻り値:** 入力配列の各行の 2 文字の言語コードを含む文字列配列。

応答本文の形式は次のとおりです。

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
`DetectArray` が成功しました。 入力配列の各行の 2 文字の言語コードを含む文字列配列を返します。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|文字列|
|Authorization|(空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。  認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>実装に関するメモ

> [!IMPORTANT]
> **非推奨のお知らせ:** 2018 年 1 月 31 日以降、このメソッドは新しい文の送信を受け付けなくなります。 ユーザーにはエラー メッセージが表示されます。 Collaborative Translation Framework (CTF) の変更に関するお知らせをご覧ください。

翻訳メモリに翻訳を追加します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation` です。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application: xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型   |
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|originalText|(空)|必須。 翻訳するテキストを含む文字列。 文字列の長さは最大で 1,000 文字です。|query|string|
|translatedText|(空) |必須。 ターゲット言語に翻訳されたテキストを含む文字列。 文字列の長さは最大で 2,000 文字です。|query|文字列|
|from|(空)   |必須。 テキストの元の言語の言語コードを表す文字列。 たとえば、英語の場合は en、ドイツ語の場合は de です。|query|文字列|
|to|(空)|必須。 テキストの翻訳先言語の言語コードを表す文字列。|query|string|
|評価|(空) |省略可能。 文字列の品質評価を表す整数。 値は -10 から 10 です。 既定値は 1 です。|query|integer|
|contentType|(空)    |省略可能。 翻訳されるテキストの形式。 サポートされている形式は、`text/plain` と `text/html` です。 HTML 要素は、いずれも整形式の完全な要素にする必要があります。    |query|string|
|category|(空)|省略可能。 翻訳のカテゴリ (ドメイン) を含む文字列。 既定では、 `general`です。|クエリ|string|
|user|(空)|必須。 送信者を追跡するのに使用される文字列。|query|string|
|uri|(空)|省略可能。 翻訳のコンテンツの場所を含む文字列。|query|文字列|
|Authorization|(空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。  認証トークン: `"Bearer" + " " + "access_token"`。  |header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|410|`AddTranslation` は現在サポートされていません。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>実装に関するメモ

> [!IMPORTANT]
> **非推奨のお知らせ:** 2018 年 1 月 31 日以降、このメソッドは新しい文の送信を受け付けなくなります。 ユーザーにはエラー メッセージが表示されます。 Collaborative Translation Framework (CTF) の変更に関するお知らせをご覧ください。

翻訳メモリに翻訳の配列を追加します。 このメソッドは `AddTranslation` の配列バージョンです。

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

`AddtranslationsRequest` には、以下の要素が含まれています。

* `AppId`:必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`AppId` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を入れます。
* `From`:必須。 ソース言語の言語コードを含む文字列。 `GetLanguagesForTranslate` メソッドによって返されるいずれかの言語である必要があります。
* `To`:必須。 ターゲット言語の言語コードを含む文字列。 `GetLanguagesForTranslate` メソッドによって返されるいずれかの言語である必要があります。
* `Translations`:必須。 翻訳メモリに追加する翻訳の配列。 各翻訳には `OriginalText`、`TranslatedText`、`Rating` を含める必要があります。 各 `OriginalText` および `TranslatedText` の最大サイズは 1,000 文字です。 すべての `OriginalText` および `TranslatedText` 要素の合計は、10,000 文字以内にする必要があります。 配列要素の最大数は 100 です。
* `Options`:必須。 `Category`、`ContentType`、`Uri`、および `User` を含む一連のオプション。 `User` は必須です。 `Category`、`ContentType`、`Uri` は省略可能です。 指定された要素は、アルファベット順に一覧表示する必要があります。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
`AddTranslationArray` メソッドが成功しました。 

2018 年 1 月 31 日を過ぎると、文の送信は受け付けられなくなります。 サービスでエラー コード 410 が返されます。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|Authorization|(空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。  認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|410    |`AddTranslation` は現在サポートされていません。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503|サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>実装に関するメモ
テキストの一部を複数の文に分割し、各文の長さを含む配列を返します。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences` です。

**戻り値:** 文の長さを表す整数の配列。 配列の長さは、文の数を表します。 その値は各文の長さを表します。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
文の長さを表す整数の配列。 配列の長さは、文の数を表します。 その値は各文の長さを表します。

integer

応答コンテンツ タイプ: application/xml

### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)  |必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query| string|
|text|(空)   |必須。 複数の文に分割するテキストを表す文字列。 テキストの最大サイズは 10,000 文字です。|query|string|
|language   |(空)    |必須。 入力テキストの言語コードを表す文字列。|query|文字列|
|Authorization|(空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。   |header|文字列|
|Ocp-Apim-Subscription-Key|(空)|`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400|無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401|無効な資格情報。|
|500|サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503|サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>実装に関するメモ
ストアと MT エンジンから、指定した言語ペアの翻訳の配列を取得します。 `GetTranslations` は、使用可能なすべての翻訳を返すという点で `Translate` と異なります。

要求 URI は `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations` です。

要求の本文には、次の形式の省略可能な `TranslationOptions` オブジェクトが含まれます。

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

`TranslateOptions` オブジェクトには、以下の一覧にある値が含まれています。 これらはすべて省略可能で、最も一般的な設定が既定値です。 指定された要素は、アルファベット順に一覧表示する必要があります。

* `Category`:翻訳のカテゴリ (ドメイン) を含む文字列。 既定では、 `general`です。
* `ContentType`:唯一サポートされていて、既定値となっているオプションは `text/plain` です。
* `IncludeMultipleMTAlternatives`:MT エンジンから複数の翻訳候補を返すかどうかを指定するブール型のフラグ。 有効な値は `true` と `false` です (大文字と小文字は区別されます)。 既定値は `false` で、翻訳候補が 1 つだけ返されます。 フラグを `true` に設定すると、Collaborative Translation Framework (CTF) と完全に統合されている、人工的な翻訳候補の作成機能が有効になります。 この機能では、デコーダーの *n*-best リストから人工的な翻訳候補を追加して、CTF には翻訳がない文章の翻訳候補を返すことができます。
    - 評価。 評価は、以下のように適用されます。 
         - 最善の自動翻訳は評価 5 になります。
       - CTF からの翻訳候補には、レビュー担当者の権限が反映されます。 その範囲は -10 から +10 です。
       - 自動的に生成された (*n*-best) 翻訳候補の評価は 0 になり、一致度は 100 になります。
    - 翻訳候補の数。 返される翻訳候補の数の最大値は `maxTranslations` に指定されている値ですが、それより小さくなる場合もあります。
    - 言語ペア。 この機能は、簡体字中国語と繁体字中国語の間では、翻訳方向を問わず使用できません。 Microsoft Translator でサポートされているその他すべての言語ペアに使用できます。
* `State`:要求と応答の関連付けに役立つユーザー状態。 応答では同じ内容が返されます。
* `Uri`:この URI で結果をフィルター処理します。 値を設定しなかった場合は、既定値として `all` が使用されます。
* `User`:このユーザーで結果をフィルター処理します。 値を設定しなかった場合は、既定値として `all` が使用されます。

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

この応答には、以下の値を含む `GetTranslationsResponse` 要素が含まれます。

* `Translations`:見つかった一致の配列であり、`TranslationMatch` オブジェクトに格納されます (以降のセクションで説明)。 翻訳によっては、元のテキストとわずかに違う (あいまい一致) 場合もあります。 翻訳は、次の順に並べ替えられます:最初に 100% 一致、次にあいまい一致。
* `From`:メソッドで `From` 言語が指定されていない場合、この値は自動言語検出から取得されます。 それ以外の場合は、指定された `From` 言語になります。
* `State`:要求と応答の関連付けに役立つユーザー状態。 `TranslateOptions` パラメーターに入力された値を含みます。

`TranslationMatch` オブジェクトは、以下の値で構成されます。

* `Error`:特定の入力文字列に関してエラーが発生している場合、そのエラー コード。 それ以外の場合は、このフィールドは空です。
* `MatchDegree`:入力テキストがストアで見つかった元のテキストにどのくらい一致しているかを示します。 システムにより、不正確な一致を含むストアに対して入力文が照合されます。 返される値の範囲は 0 から 100 です。0 は類似性なしで、100 は大文字と小文字まで完全に一致します。
* `MatchedOriginalText`:この結果の一致した元のテキストです。 この値は、一致した元のテキストが入力されたテキストと異なる場合にのみ返されます。 あいまい一致のソース テキストを返すために使用されます。 この値は、Microsoft Translator の結果に対しては返されません。
* `Rating`:品質を決定する人の権限を示します。 機械翻訳の結果は、評価 5 になります。 匿名で提供された翻訳は、一般的に評価 1 から 4 になります。 信頼できる提供元からの翻訳は、一般的に評価 6 から 10 になります。
* `Count`:この評価のこの翻訳が選択された回数。 自動的に翻訳された応答の値は 0 です。
* `TranslatedText`:翻訳されたテキスト。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
前述した形式の `GetTranslationsResponse` オブジェクト。

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|appid|(空)|必須。 `Authorization` または `Ocp-Apim-Subscription-Key` ヘッダーが使用されている場合、`appid` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。|query|string|
|text|(空)|必須。 翻訳するテキストを表す文字列。 テキストの最大サイズは 10,000 文字です。|query|文字列|
|from|(空)|必須。 翻訳されるテキストの言語コードを表す文字列。|query|文字列|
|to |(空)    |必須。 テキストの翻訳先言語の言語コードを表す文字列。|query|string|
|maxTranslations|(空)|必須。 返す翻訳結果の最大数を表す整数。|query|integer|
|Authorization| (空)|`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。 認証トークン: `"Bearer" + " " + "access_token"`。|string|  header|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID をお知らせください。|
|503|サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>実装に関するメモ
複数のソース テキストに対する複数の翻訳候補を取得します。

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

`GetTranslationsArrayRequest` には、以下の要素が含まれています。

* `AppId`:必須。 `Authorization` ヘッダーが使用されている場合、`AppId` フィールドは空のままにします。 それ以外の場合は、`"Bearer" + " " + "access_token"` を含む文字列を含めます。
* `From`:必須。 翻訳されるテキストの言語コードを表す文字列。
* `MaxTranslations`:必須。 返す翻訳結果の最大数を表す整数。
* `Options`:省略可能。 以下の値を含む `Options` オブジェクト。 これらはすべて省略可能で、最も一般的な設定が既定値です。 指定された要素は、アルファベット順に一覧表示する必要があります。
    - `Category`:翻訳のカテゴリ (ドメイン) を含む文字列。 既定では、 `general`です。
    - `ContentType`:唯一サポートされていて、既定値となっているオプションは `text/plain` です。
    - `IncludeMultipleMTAlternatives`:MT エンジンから複数の翻訳候補を返すかどうかを指定するブール型のフラグ。 有効な値は `true` と `false` です (大文字と小文字は区別されます)。 既定値は `false` で、翻訳候補が 1 つだけ返されます。 フラグを `true` に設定すると、Collaborative Translations Framework (CTF) と完全に統合されている、人工的な翻訳候補の生成機能が有効になります。 この機能では、デコーダーの *n*-best リストから人工的な翻訳候補を追加して、CTF には翻訳候補がない文章の翻訳候補を返すことができます。
        - 評価。評価は以下のように適用されます。
          - 最善の自動翻訳は評価 5 になります。
          - CTF からの翻訳候補には、レビュー担当者の権限が反映されます。 その範囲は -10 から +10 です。
          - 自動的に生成された (*n*-best) 翻訳候補の評価は 0 になり、一致度は 100 になります。
        - 翻訳候補の数。 返される翻訳候補の数の最大値は `maxTranslations` に指定されている値ですが、それより小さくなる場合もあります。
        - 言語ペア。 この機能は、簡体字中国語と繁体字中国語の間では、翻訳方向を問わず使用できません。 Microsoft Translator でサポートされているその他すべての言語ペアに使用できます。
* `State`:要求と応答の関連付けに役立つユーザー状態。 応答では同じ内容が返されます。
* `Uri`:この URI で結果をフィルター処理します。 値を設定しなかった場合は、既定値として `all` が使用されます。
* `User`:このユーザーで結果をフィルター処理します。 値を設定しなかった場合は、既定値として `all` が使用されます。
* `Texts`:必須。 翻訳用のテキストを含む配列。 すべての文字列が同じ言語である必要があります。 翻訳するすべてのテキストの合計を 10,000 文字以内にする必要があります。 配列要素の最大数は 10 です。
* `To`:必須。 テキストの翻訳先言語の言語コードを表す文字列。

省略可能な要素は省略してかまいません。 `GetTranslationsArrayRequest` の直接の子である要素は、アルファベット順に一覧表示されている必要があります。

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

各 `GetTranslationsResponse` 要素には、以下の値が含まれています。

* `Translations`:見つかった一致の配列であり、`TranslationMatch` オブジェクトに格納されます (以降のセクションで説明)。 翻訳によっては、元のテキストとわずかに違う (あいまい一致) 場合もあります。 翻訳は、次の順に並べ替えられます:最初に 100% 一致、次にあいまい一致。
* `From`:メソッドで `From` 言語が指定されていない場合、この値は自動言語検出から取得されます。 それ以外の場合は、指定された `From` 言語になります。
* `State`:要求と応答の関連付けに役立つユーザー状態。 `TranslateOptions` パラメーターに入力された値を含みます。

`TranslationMatch` オブジェクトには、以下の値が含まれています。
* `Error`:特定の入力文字列に関してエラーが発生している場合、そのエラー コード。 それ以外の場合は、このフィールドは空です。
* `MatchDegree`:入力テキストがストアで見つかった元のテキストにどのくらい一致しているかを示します。 システムにより、不正確な一致を含むストアに対して入力文が照合されます。 返される値の範囲は 0 から 100 です。0 は類似性なしで、100 は大文字と小文字まで完全に一致します。
* `MatchedOriginalText`:この結果の一致した元のテキストです。 この値は、一致した元のテキストが入力されたテキストと異なる場合にのみ返されます。 あいまい一致のソース テキストを返すために使用されます。 この値は、Microsoft Translator の結果に対しては返されません。
* `Rating`:品質を決定する人の権限を示します。 機械翻訳の結果は、評価 5 になります。 匿名で提供された翻訳は、一般的に評価 1 から 4 になります。 信頼できる提供元からの翻訳は、一般的に評価 6 から 10 になります。
* `Count`:この評価のこの翻訳が選択された回数。 自動的に翻訳された応答の値は 0 です。
* `TranslatedText`:翻訳されたテキスト。


### <a name="response-class-status-200"></a>応答クラス (ステータス 200)

string

応答コンテンツ タイプ: application/xml
 
### <a name="parameters"></a>parameters

|パラメーター|値|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|:--|
|Authorization  |(空)    |`appid` フィールドと `Ocp-Apim-Subscription-Key` ヘッダーの両方を空のままにする場合は必須となります。  認証トークン: `"Bearer" + " " + "access_token"`。|header|文字列|
|Ocp-Apim-Subscription-Key|(空)  |`appid` フィールドと `Authorization` ヘッダーの両方を空のままにする場合は必須となります。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400    |無効な要求です。 入力パラメーターと詳細なエラー応答を確認してください。|
|401    |無効な資格情報。|
|500    |サーバー エラー。 エラーが解決しない場合は、お知らせください。 要求のおよその日時と、応答ヘッダー `X-MS-Trans-Info` に含まれている要求 ID を提供してください。|
|503    |サービスが一時的に利用できません。 再試行して、エラーが解決しない場合は、お知らせください。|

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Translator Text API v3 への移行](../migrate-to-v3.md)


