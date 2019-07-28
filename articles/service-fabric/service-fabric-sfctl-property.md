---
title: Azure Service Fabric CLI- sfctl property | Microsoft Docs
description: Service Fabric CLI- sfctl property のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: christina-kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 54cb9f604e9d1b817947990e657390387df6c881
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556594"
---
# <a name="sfctl-property"></a>sfctl property
Service Fabric の名前でプロパティを格納およびクエリします。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| delete | 指定した Service Fabric プロパティを削除します。 |
| get | 指定した Service Fabric プロパティを取得します。 |
| list | 指定した名前の下にあるすべての Service Fabric プロパティに関する情報を取得します。 |
| put | Service Fabric プロパティを作成または更新します。 |

## <a name="sfctl-property-delete"></a>sfctl property delete
指定した Service Fabric プロパティを削除します。

指定した名前の下にある指定した Service Fabric プロパティを削除します。 プロパティを削除するには、その前にプロパティが作成されている必要があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name-id       [必須] | "fabric\:" URI スキームを除いた Service Fabric の名前。 |
| --property-name [必須] | 取得するプロパティの名前を指定します。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-property-get"></a>sfctl property get
指定した Service Fabric プロパティを取得します。

指定した名前の下にある指定した Service Fabric プロパティを取得します。 メタデータと値の両方が常に返ります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name-id       [必須] | "fabric\:" URI スキームを除いた Service Fabric の名前。 |
| --property-name [必須] | 取得するプロパティの名前を指定します。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-property-list"></a>sfctl property list
指定した名前の下にあるすべての Service Fabric プロパティに関する情報を取得します。

Service Fabric 名には、カスタム情報を格納している 1 つまたは複数の名前付きプロパティを指定できます。 この操作は、これらのプロパティに関する情報をページ単位の一覧で取得します。 情報には、各プロパティに関する名前、値、メタデータが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name-id [必須] | "fabric\:" URI スキームを除いた Service Fabric の名前。 |
| --continuation-token | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらない場合は、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。 |
| --include-values | 返されるプロパティの値を含めるかどうかを指定できます。 メタデータと共に値を返す場合は true、プロパティのメタデータのみを返す場合は false。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-property-put"></a>sfctl property put
Service Fabric プロパティを作成または更新します。

指定した名前の下にある指定した Service Fabric プロパティを作成または更新します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name-id       [必須] | "fabric\:" URI スキームを除いた Service Fabric の名前。 |
| --property-name [必須] | Service Fabric プロパティの名前。 |
| --value         [必須] | Service Fabric プロパティの値を記述します。 これは、JSON 文字列です。 <br><br> JSON 文字列には、データの "Kind" とデータの "Value" の 2 つのフィールドがあります。 "Kind" の値は、JSON 文字列に出現する最初の項目でなければならず、指定できる値は "Binary"、"Int64"、"Double"、"String"、"Guid" です。 値は、特定の型にシリアル化可能である必要があります。 "Kind" と "Data" の値はどちらも、文字列として指定する必要があります。 |
| --custom-id-type | プロパティのカスタム型 ID。このプロパティを使用して、ユーザーはプロパティの値の型にタグを付けることができます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。