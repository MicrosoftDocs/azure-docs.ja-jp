---
title: コラボレーション翻訳フレームワーク (CTF) レポート - Translator Text API
titlesuffix: Azure Cognitive Services
description: コラボレーション翻訳フレームワーク (CTF) レポートを使用する方法。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 79a645b0b41f200c384c165f244efa679be65171
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443456"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>コラボレーション翻訳フレームワーク (CTF) レポートを使用する方法

> [!NOTE]
> このメソッドは非推奨です。 これは Translator Text API の V3.0 では使用できません。
> 
> コラボレーション翻訳フレームワーク (CTF) は、以前 Translator Text API の V2.0 で利用可能でしたが、2018 年 2 月 1 日をもって廃止されました。 AddTranslation と AddTranslationArray 関数により、ユーザーは、コラボレーション翻訳フレームワークによる修正を有効にできます。 2018 年 1 月 31 日以降、これら 2 つの関数は、新しい文の送信を受け付けなくなり、ユーザーにはエラー メッセージが表示されます。 これらの関数は廃止されており、置き換えられることはありません。

コラボレーション翻訳フレームワーク (CTF) レポート API は、CTF ストア内の統計情報と実際のコンテンツを返します。 この API は、次の理由により、GetTranslations() メソッドと異なります。
* ユーザーのアカウント (appId または Azure Marketplace アカウント) からのみ、翻訳されたコンテンツとその合計数を返します。
* 翻訳されたコンテンツとその合計数を返し、ソース文の一致は必要ありません。
* 自動翻訳 (機械翻訳) は返しません。

## <a name="endpoint"></a>エンドポイント
CTF Reporting API のエンドポイントは https://api.microsofttranslator.com/v2/beta/ctfreporting.svc です。


## <a name="methods"></a>メソッド
| Name |    説明|
|:---|:---|
| GetUserTranslationCounts メソッド | ユーザーによって作成される翻訳の数を取得します。 |
| GetUserTranslations メソッド | ユーザーによって作成される翻訳を取得します。 |

これらのメソッドを使用すると、次の処理が可能になります。
* ダウンロード用のアカウント ID で、ユーザーの翻訳および修正の一式を取得する。
* 常連の共同作成者の一覧を取得する。 AddTranslation() に正しいユーザー名が入力されていることを確認してください。
* URI プレフィックスに基づいて、必要な場合にサイトの一部に制限される、信頼できるユーザーがすべての候補者を表示できるようにするユーザー インターフェイス (UI) をビルドする。

> [!NOTE]
> どちらのメソッドも比較的低速で高価です。 控えめに使用することをお勧めします。

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts メソッド

このメソッドは、ユーザーによって作成される翻訳の数を取得します。 uriPrefix、from、to、user、minRating、maxRating の要求パラメーターでグループ化した翻訳数の一覧を提供します。

**構文**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**パラメーター**

| パラメーター | 説明 |
|:---|:---|
| appId | **必須**: Authorization ヘッダーを使用する場合は、appid フィールドを空白のままにするか、"Bearer" + " " + アクセス トークンを含む文字列を指定します。|
| uriPrefix | **省略可能**: 翻訳の URI のプレフィックスを含む文字列。|
| from | **省略可能**: 翻訳テキストの言語コードを表す文字列。 |
| to | **省略可能**: テキストを翻訳するターゲットの言語コードを表す文字列。|
| minRating| **省略可能**: 翻訳されたテキストの最低限の品質評価を表す整数値。 有効な値は -10 から 10 です。 既定値は 1 です。|
| maxRating| **省略可能**: 翻訳されたテキストの最高の品質評価を表す整数値。 有効な値は -10 から 10 です。 既定値は 1 です。|
| user | **省略可能**: 送信の発信元に基づいた結果のフィルタリングに使用する文字列。 |
| category| **省略可能**: 翻訳のカテゴリまたは領域を含む文字列。 このパラメーターは、既定のオプション general のみをサポートしています。|
| minDateUtc| **省略可能**: 翻訳の取得の開始日の日付。 日付は UTC 形式でなければなりません。 |
| maxDateUtc| **省略可能**: 翻訳の取得の終了日の日付。 日付は UTC 形式でなければなりません。 |
| skip| **省略可能**: ページでスキップする結果の数。 たとえば、結果の最初の 20 行をスキップして、21 行目の結果レコードから表示する場合、このパラメーターに 20 を指定します。 パラメーターの既定値は 0 です。|
| take | **省略可能**: 取得する結果の数。 各要求の最大数は 100 です。 既定値は 100 です。|

> [!NOTE]
> skip および take 要求パラメーターは、多数の結果レコードの改ページ位置の自動修正を有効にします。

**戻り値**

結果セットには **UserTranslationCount** の配列が含まれます。 各 UserTranslationCount には、次の要素があります。

| フィールド | 説明 |
|:---|:---|
| Count| 取得される結果の数|
| ソース | ソース言語|
| Rating| AddTranslation() メソッド呼び出しで送信者により適用される評価|
| ターゲット| 対象言語|
| Uri| AddTranslation() メソッド呼び出しで適用される URI|
| User| ユーザー名|

**Exceptions**

| 例外 | Message | 条件 |
|:---|:---|:---|
| ArgumentOutOfRangeException | パラメーター '**maxDateUtc**' は、'**minDateUtc**' 以上にする必要があります。| パラメーター **maxDateUtc** の値は、パラメーター **minDateUtc** の値より小さいです。|
| TranslateApiException | IP がクォータを超えています。| <ul><li>1 分あたりの要求の数の上限に達しました。</li><li>要求のサイズは 10000 文字に制限されたままです。</li><li>時間単位および日単位のクォータは、Microsoft Translator API が許容する文字数を制限します。</li></ul>|
| TranslateApiException | Appid がクォータを超えています。| アプリケーション ID は、時間単位または日単位のクォータを超えました。|

> [!NOTE]
> クォータは、サービスのすべてのユーザー間での公平性を確保するように調整されます。

**GitHib のコード例の表示**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations メソッド

このメソッドは、ユーザーによって作成される翻訳を取得します。 uriPrefix、from、to、user、minRating、maxRating の要求パラメーターでグループ化した翻訳を提供します。

**構文**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**パラメーター**

| パラメーター | 説明 |
|:---|:---|
| appId | **必須**: Authorization ヘッダーを使用する場合は、appid フィールドを空白のままにするか、"Bearer" + " " + アクセス トークンを含む文字列を指定します。|
| uriPrefix| **省略可能**: 翻訳の URI のプレフィックスを含む文字列。|
| from| **省略可能**: 翻訳テキストの言語コードを表す文字列。|
| to| **省略可能**: テキストを翻訳するターゲットの言語コードを表す文字列。|
| minRating| **省略可能**: 翻訳されたテキストの最低限の品質評価を表す整数値。 有効な値は -10 から 10 です。 既定値は 1 です。|
| maxRating| **省略可能**: 翻訳されたテキストの最高の品質評価を表す整数値。 有効な値は -10 から 10 です。 既定値は 1 です。|
| user| **省略可能。送信の発信元に基づいた結果のフィルタリングに使用する文字列。**|
| category| **省略可能**: 翻訳のカテゴリまたは領域を含む文字列。 このパラメーターは、既定のオプション general のみをサポートしています。|
| minDateUtc| **省略可能**: 翻訳の取得の開始日の日付。 日付は UTC 形式でなければなりません。|
| maxDateUtc| **省略可能**: 翻訳の取得の終了日の日付。 日付は UTC 形式でなければなりません。|
| skip| **省略可能**: ページでスキップする結果の数。 たとえば、結果の最初の 20 行をスキップして、21 行目の結果レコードから表示する場合、このパラメーターに 20 を指定します。 パラメーターの既定値は 0 です。|
| take| **省略可能**: 取得する結果の数。 各要求の最大数は 100 です。 既定値は 50 です。|

> [!NOTE]
> skip および take 要求パラメーターは、多数の結果レコードの改ページ位置の自動修正を有効にします。

**戻り値**

結果セットには **UserTranslation** の配列が含まれます。 各 UserTranslation には、次の要素があります。

| フィールド | 説明 |
|:---|:---|
| CreatedDateUtc| AddTranslation() を使用したエントリの作成日|
| ソース| ソース言語|
| OriginalText| 要求を送信するときに使用されるソース言語のテキスト|
|Rating |AddTranslation() メソッド呼び出しで送信者により適用される評価|
|ターゲット|    対象言語|
|TranslatedText|    AddTranslation() メソッド呼び出しで送信される翻訳|
|Uri|   AddTranslation() メソッド呼び出しで適用される URI|
|User   |ユーザー名|

**Exceptions**

| 例外 | Message | 条件 |
|:---|:---|:---|
| ArgumentOutOfRangeException | パラメーター '**maxDateUtc**' は、'**minDateUtc**' 以上にする必要があります。| パラメーター **maxDateUtc** の値は、パラメーター **minDateUtc** の値より小さいです。|
| TranslateApiException | IP がクォータを超えています。| <ul><li>1 分あたりの要求の数の上限に達しました。</li><li>要求のサイズは 10000 文字に制限されたままです。</li><li>時間単位および日単位のクォータは、Microsoft Translator API が許容する文字数を制限します。</li></ul>|
| TranslateApiException | Appid がクォータを超えています。| アプリケーション ID は、時間単位または日単位のクォータを超えました。|

> [!NOTE]
> クォータは、サービスのすべてのユーザー間での公平性を確保するように調整されます。

**GitHib のコード例の表示**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
