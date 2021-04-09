---
title: 要求テレメトリ用のデータ モデル - Azure Application Insights
description: 要求テレメトリ用の Application Insights データ モデル
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320563"
---
# <a name="request-telemetry-application-insights-data-model"></a>要求テレメトリ:Application Insights データ モデル

要求テレメトリ項目 ([Application Insights](./app-insights-overview.md)) は、アプリケーションに対する外部要求によってトリガーされた実行の論理シーケンスを表します。 すべての要求の実行は、一意の `ID` と、すべての実行パラメーターが含まれる `url` によって識別されます。 要求は、論理的な `name` によってグループ化でき、要求の `source` が定義されます。 コードの実行は、`success` または `fail`という結果になる可能性があり、特定の `duration` を持っています。 成功した実行と失敗した実行は、どちらも `resultCode` によってさらにグループ化することができます。 要求テレメトリの開始時刻は、エンベロープ レベルで定義されます。

要求テレメトリは、カスタムの `properties` と `measurements` を使用する標準的な機能拡張モデルをサポートします。

## <a name="name"></a>名前

要求の名前は、要求を処理するために使用されたコード パスを表します。 小さなカーディナリティの値を使用すると、要求をより適切にグループ化できます。 HTTP 要求では、これは、実際の `id` 値ではなく、`GET /values/{id}`のような HTTP メソッドとURL パス テンプレートを表します。

Application Insights Web SDK は、要求の名前の大文字小文字を "そのまま" 送信します。 UI では大文字と小文字を区別してグループ化されるため、`GET /Home/Index` と `GET /home/INDEX` は別々にカウントされます (多くの場合、これらは同じコントローラーとアクションの実行に至ります)。 区別する理由は、URL では一般的に[大文字と小文字が区別](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)されるためです。 大文字で入力した URL で `404` が発生するかどうかを確認できます。 ASP.NET Web SDK による要求名のコレクションについては、[ブログの投稿](https://apmtips.com/posts/2015-02-23-request-name-and-url/)で詳細を確認できます。

最大長:1024 文字

## <a name="id"></a>id

要求呼び出しインスタンスの識別子。 要求とその他のテレメトリ項目を相関付けるために使用されます。 ID はグローバルに一意である必要があります。 詳細については、[相関付け](./correlation.md)に関するページを参照してください。

最大長:128 文字

## <a name="url"></a>url

すべてのクエリ文字列パラメーター付きの要求 URL。

最大長:2048 文字

## <a name="source"></a>source

要求のソース。 例: 呼び出し元のインストルメンテーション キーや呼び出し元の IP アドレス。 詳細については、[相関付け](./correlation.md)に関するページを参照してください。

最大長:1024 文字

## <a name="duration"></a>Duration

`DD.HH:MM:SS.MMMMMM` 形式の要求時間。 正の `1000` 日未満の値にする必要があります。 要求テレメトリは開始と終了によって操作を表すため、このフィールドは必須です。

## <a name="response-code"></a>応答コード

要求の実行結果。 HTTP 要求の HTTP 状態コード。 `HRESULT` 値または他の種類の要求用の例外の種類になる場合があります。

最大長:1024 文字

## <a name="success"></a>Success

呼び出しの成功または失敗を示す値。 このフィールドは必須です。 明示的に `false` が設定されない場合、要求は成功したとみなされます。 操作が例外によって中断された、またはエラー結果コードが返された場合は、この値を `false` に設定します。

Web アプリケーションでは、応答コードが `400` 未満または `401` に相当する場合は、Application Insights によって要求は成功と定義されます。 ただし、この既定のマッピングが、アプリケーションのセマンティックと一致しない場合があります。 応答コード `404` は、通常のフローの一部になることができる "レコードなし" を示していることがあります。 それは、リンクが壊れていることを示していることもあります。 リンクが壊れている場合は、さらに高度なロジックを実装することもできます。 URL の参照元を分析してリンクが同じサイトに配置されている場合のみ、壊れたリンクをエラーとマークすることができます。 または、会社のモバイル アプリケーションからアクセスされた場合にエラーとマークできます。 同様に、`301` と `302` は、リダイレクトをサポートしていないクライアントからアクセスされた場合のエラーを示します。

コンテンツを一部受け入れる `206` が、要求全体のエラーを示していることがあります。 たとえば、Application Insights のエンドポイントがテレメトリ項目のバッチを 1 つの要求として受信している場合です。 バッチ内の一部の項目が正常に処理されなかった場合、`206` が返されます。 `206` の割合の上昇は、調査する必要がある問題の存在を示唆しています。 同様のロジックが `207` マルチステータスにも適用され、複数の応答コードの中で、最も悪いステータスが "成功" である場合があります。

要求の結果コードと状態コードの詳細については、[ブログの投稿](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/)で確認できます。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>カスタム測定値

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>次のステップ

- [カスタム要求テレメトリを記述します](./api-custom-events-metrics.md#trackrequest)。
- Application Insights の型とデータ モデルについては、[データ モデル](data-model.md)に関するページを参照してください。
- Application Insights で [ASP.NET Core を構成する](./asp-net.md)方法を確認します。
- Application Insights でサポートされている[プラットフォーム](./platforms.md)を確認します。

