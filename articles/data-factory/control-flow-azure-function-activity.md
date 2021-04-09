---
title: Azure Data Factory の Azure Functions アクティビティ
description: Azure Functions アクティビティを使用して、Data Factory パイプライン内で Azure Functions を実行する方法について説明します
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 13f00907737a99bc5dcd8c21d660ce83aa681908
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783797"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory の Azure Functions アクティビティ
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Azure Functions アクティビティを使用すると、Data Factory パイプライン内で [Azure Functions](../azure-functions/functions-overview.md) を実行できます。 Azure Functions を実行するには、リンクされたサービスの接続と、実行する Azure Functions を指定するアクティビティを作成する必要があります。

この機能の概要とデモンストレーションについては、以下の 8 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure Functions のリンクされたサービス

Azure Functions の戻り値の型は、有効な `JObject` である必要があります。 ([JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) は `JObject` では "*ない*" ことに留意してください。)`JObject`以外の戻り値の型が失敗し、ユーザー エラー *応答コンテンツは有効な JObject ではない* が発生します。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| type   | type プロパティは、次のように設定する必要があります:**AzureFunction** | はい |
| function app url | Azure 関数アプリの URL。 形式は `https://<accountname>.azurewebsites.net` です。 この URL は、Azure portal で関数アプリを表示した際に **URL** セクションに表示される値です  | はい |
| function key | Azure 関数のアクセス キーです。 それぞれの関数の **[管理]** セクションをクリックし、**ファンクション キー** または **ホスト キー** をコピーします。 詳しくは、次の記事をご覧ください:[Azure Functions の HTTP トリガーとバインド](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | はい |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Functions アクティビティ

| **プロパティ**  | **説明** | **指定できる値** | **必須** |
| --- | --- | --- | --- |
| name  | パイプラインのアクティビティの名前。  | String | はい |
| type  | アクティビティの種類は 'AzureFunctionActivity' です | String | はい |
| linked service | 対応する Azure Functions アプリの、Azure Functions のリンクされたサービス  | リンクされたサービスの参照 | はい |
| 関数名  | このアクティビティによって呼び出される Azure Functions アプリ内の関数の名前 | String | はい |
| method  | 関数呼び出しのための REST API メソッド | 文字列がサポートされている型:"GET"、"POST"、"PUT"   | はい |
| header  | 要求に送信されるヘッダー。 たとえば、要求に種類と言語を設定する場合: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 文字列 (または文字列の resultType を含む式) | いいえ |
| body  | 関数 API メソッドへの要求と共に送信される本文  | 文字列 (または文字列の resultType を含む式) またはオブジェクト。   | PUT/POST メソッドには必須です |
|   |   |   | |

「[要求ペイロードのスキーマ](control-flow-web-activity.md#request-payload-schema)」セクションにある要求ペイロードのスキーマを参照してください。

## <a name="routing-and-queries"></a>ルーティングとクエリ

Azure Functions アクティビティでは、**ルーティング** がサポートされます。 たとえば、Azure Functions にエンドポイント`https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`がある場合、その後で Azure Functions のアクティビティを使用する `functionName` は`<functionName>/<value>`です。 実行時に任意の`functionName`を提供するようこの関数をパラメーター化できます。

また、Azure Functions アクティビティでは **クエリ** がサポートされます。 クエリは`functionName`の一部として含まなければなりません。 たとえば、関数名が`HttpTriggerCSharp`であり含めるクエリは`name=hello`である場合、Azure Functions のアクティビティにおいて`functionName`を`HttpTriggerCSharp?name=hello`として構築できます。 この関数は、実行時に値を決定できるように、パラメーター化できます。

## <a name="timeout-and-long-running-functions"></a>タイムアウトと長期関数

Azure Functions は、設定で構成した`functionTimeout`設定に関係無く 230 秒後にタイムアウトします。 詳細については、 [こちらの記事](../azure-functions/functions-versions.md#timeout)を参照してください。 この振る舞いを回避するには、非同期パターンに従うか Durable Functions を使用します。 Durable Functions の利点は独自の状態追跡メカニズムを提供する点にあり、独自に実装する必要はありません。

[この記事](../azure-functions/durable/durable-functions-overview.md)で Durable Functions について詳しく説明します。 Azure Functions のアクティビティを設定して Durable 関数を呼び出すことができます。これにより、[この例](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)など異なる URI で応答を返します。 `statusQueryGetUri`は関数の実行中に HTTP ステータス 202 を返すため、Web アクティビティを使用して、関数の状態をポーリングできます。 Web アクティビティの`url`フィールドを`@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`に設定するだけです。 Durable 関数が完了したら、関数の出力は、Web アクティビティの出力になります。


## <a name="sample"></a>サンプル

Azure Functions を使用して tar ファイルのコンテンツを抽出する Data Factory のサンプルについては、[こちら](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Data Factory のパイプラインとアクティビティ](concepts-pipelines-activities.md)について学習する。
