---
title: "ロジック アプリに Microsoft Translator を追加する | Microsoft Docs"
description: "Microsoft Translator コネクタと REST API パラメーターの概要"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: add307420b9e16a60b0e57b0ba08188b2509978e
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Microsoft Translator コネクタの使用
Microsoft Translator に接続して、テキストの翻訳、言語の検出などを行います。 Microsoft Translator では、次の操作を実行できます。 

* Microsoft Translator から取得したデータに基づいてビジネス フローを構築できます。 
* アクションを使用して、テキストの翻訳、言語の検出などを行うことができます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、Dropbox で新しいファイルが作成されたときに、Microsoft Translator を使用してファイル内のテキストを別の言語に翻訳することができます。

ロジック アプリに操作を追加する方法については、「 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Microsoft Translator には、次のアクションがあります。 トリガーはありません。

| トリガー | アクション |
| --- | --- |
| なし |<ul><li>言語を検出する</li><li>テキストを音声に変換する</li><li>テキストを翻訳する</li><li>言語を取得する</li><li>音声の言語を取得する</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## <a name="create-a-connection-to-microsoft-translator"></a>Microsoft Translator への接続を作成する
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API リファレンス
適用されるバージョン: 1.0。

### <a name="detect-language"></a>言語を検出する
指定したテキストのソース言語を検出します。  
```GET: /Detect```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |言語を特定するテキスト |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="text-to-speech"></a>テキストを音声に変換する
指定したテキストを wave 形式のオーディオ ストリームとして音声に変換します。  
```GET: /Speak```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |変換するテキスト |
| 言語 |string |○ |query |なし |音声を生成する言語コード (例: 'en-us') |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="translate-text"></a>テキストを翻訳する
Microsoft Translator を使用して、指定した言語にテキストを翻訳します。  
```GET: /Translate```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |翻訳するテキスト |
| languageTo |string |○ |query |なし |ターゲット言語コード (例: "fr") |
| languageFrom |string |× |query |なし |ソース言語。 指定しない場合、Microsoft Translator で自動検出が試行されます (例: en)。 |
| カテゴリ |string |× |query |全般 |翻訳のカテゴリ (既定値: 'general') |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-languages"></a>言語を取得する
Microsoft Translator がサポートするすべての言語を取得します。  
```GET: /TranslatableLanguages```

この呼び出しには、パラメーターはありません。 

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-speech-languages"></a>音声の言語を取得する
音声合成に使用できる言語を取得します。  
```GET: /SpeakLanguages``` 

この呼び出しには、パラメーターはありません。

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Language: Microsoft Translator で翻訳できる言語の言語モデル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| コード |string |× |
| 名前 |string |× |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。

[API リスト](apis-list.md)に戻ります。

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

