---
title: "Azure IoT Hub クエリ言語について | Microsoft Docs"
description: "開発者ガイド - デバイス ツインとジョブに関する情報を IoT Hub から取得するための、SQL のような IoT Hub クエリ言語の説明。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 44169ba74f6af2b1c27ea4c2a8fd0214892f90d5
ms.lasthandoff: 03/10/2017


---
# <a name="reference---iot-hub-query-language-for-device-twins-and-jobs"></a>リファレンス - デバイス ツインとジョブの IoT HUb クエリ言語
## <a name="overview"></a>概要
IoT Hub には SQL に似た強力な言語が備わっており、[デバイス ツイン][lnk-twins]や[ジョブ][lnk-jobs]に関する情報を取得できます。 この記事で取り扱う内容は次のとおりです。

* IoT Hub のクエリ言語の主な機能の説明
* 言語の詳しい説明

## <a name="get-started-with-device-twin-queries"></a>デバイス ツインのクエリの概要
[デバイス ツイン][lnk-twins]には、任意の JSON オブジェクトをタグおよびプロパティとして含めることができます。 IoT Hub では、デバイス ツインに関するすべての情報を含む&1; つの JSON ドキュメントとしてデバイス ツインにクエリを実行できます。
たとえば、IoT Hub デバイス ツインが次の構造を持っていると仮定します。

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT Hub はデバイス ツインを **devices** という名前のドキュメント コレクションとして公開します。
したがって、次のクエリでは、デバイス ツインのセット全体が取得されます。

        SELECT * FROM devices

> [!NOTE]
> [Azure IoT Hub SDK][lnk-hub-sdks] では、大量の結果をページングできます。
>
>

IoT Hub は、任意の条件でフィルター処理してデバイス ツインを取得できます。 たとえば、

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

の場合、**US** に設定された **location.region** タグの付いたデバイス ツインを取得します。
ブール演算子と算術比較も同様にサポートされています。たとえば、

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

の場合、米国にあり、テレメトリの送信頻度が&1; 分未満に設定されているすべてのデバイス ツインが取得されます。 必要に応じて、**IN** や **NIN** (含まれない) 演算子とともに配列定数も使用できます。 たとえば、

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

の場合、Wi-Fi 接続か有線接続を報告したデバイス ツインをすべて取得します。 特定のプロパティを含むすべてのデバイス ツインを識別する必要がある場合があります。 IoT Hub では、この目的で関数 `is_defined()` がサポートされています。 たとえば、

        SELECT * FROM devices
        WHERE is_defined(property.reported.connectivity)

の場合、`connectivity` 報告プロパティを定義するデバイス ツインをすべて取得します。 フィルター処理機能の詳細なリファレンスは、「[WHERE 句][lnk-query-where]」セクションをご覧ください。

グループ化と集計もサポートされています。 たとえば、

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

の場合、各テレメトリ構成状態のデバイスの数を返します。

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

上述の例では、3 つのデバイスが正常な構成を報告し、2 つは構成を適用中であり、1 つがエラーを報告しています。

### <a name="c-example"></a>C# の例
クエリ機能は **RegistryManager** クラスの [C# service SDK][lnk-hub-sdks] で公開されます。
簡単なクエリの使用例を次に示します。

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page)
            {
                // do work on twin object
            }
        }

**query** オブジェクトがページ サイズ (最大 1000) でインスタンス化され、**GetNextAsTwinAsync** メソッドを複数回呼び出すことによって複数のページを取得できる様子をご確認ください。
クエリ オブジェクトは、クエリが必要とする非シリアル化オプション (デバイス ツインやジョブ オブジェクトなど) や、プロジェクションを使用する際に使うプレーン JSON に応じて、複数の **Next\*** を公開します。

### <a name="nodejs-example"></a>Node.js の使用例
クエリ機能は **Registry** オブジェクトの [Azure IoT service SDK for Node.js][lnk-hub-sdks] で公開されます。
簡単なクエリの使用例を次に示します。

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

**query** オブジェクトがページ サイズ (最大 1000) でインスタンス化され、**nextAsTwin** メソッドを複数回呼び出すことによって複数のページを取得できる様子をご確認ください。
クエリ オブジェクトは、クエリが必要とする非シリアル化オプション (デバイス ツインやジョブ オブジェクトなど) や、プロジェクションを使用する際に使うプレーン JSON に応じて、複数の **Next\*** を公開します。

### <a name="limitations"></a>制限事項
現時点では、比較はプリミティブ型間 (オブジェクトなし) でのみサポートされます。たとえば、`... WHERE properties.desired.config = properties.reported.config` は、これらのプロパティがプリミティブ値を持つ場合にのみサポートされます。

## <a name="get-started-with-jobs-queries"></a>ジョブのクエリの概要
[ジョブ][lnk-jobs]には、一連のデバイスで演算子を実行する機能が備わっています。 各デバイス ツインにはジョブに関する情報が含まれます。これは、**jobs**.という名前のコレクションの一部です。
論理的には、次のようになります。

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                {
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

現在のところ、このコレクションには IoT Hub のクエリ言語の **devices.jobs** でクエリを実行できます。

> [!IMPORTANT]
> 現時点では、デバイス ツインにクエリを実行した場合 (つまりクエリに 'FROM devices' が含まれる場合)、ジョブのプロパティは返されません。 `FROM devices.jobs` を使用したクエリによる直接のアクセスのみが可能です。
>
>

たとえば、1 つのデバイスに影響を与えるすべてのジョブ (過去のジョブおよびスケジュールが設定されたジョブ) を取得するには、次のクエリを使用することができます。

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

このクエリによって、返されたそれぞれのジョブのデバイス固有の状態 (および、ダイレクト メソッドの応答) がどのように適用されるのかをご確認ください。
さらに、**devices.jobs** コレクションのオブジェクトのすべてのプロパティに対して任意のブール条件でフィルター処理をすることもできます。
たとえば、次のクエリについて考えてみましょう。

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

この場合、デバイス **myDeviceId** に対して 2016 年 9 月以降に作成された、すべての完了したデバイス ツインの更新ジョブが取得されます。

また、1 つのジョブによるデバイスごとの結果を取得することもできます。

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>制限事項
現在のところ、**devices.jobs** に対するクエリはサポートされていません。

* プロジェクション。`SELECT *` のみ実行可能
* 条件。ジョブのプロパティ以外にデバイス ツインを参照するもの (上述のセクションを参照)
* 集計の実行。カウント、平均、グループ化など

## <a name="get-started-with-device-to-cloud-message-routes-query-expressions"></a>デバイスからクラウドへのメッセージ ルートのクエリ式の概要

[デバイスからクラウドへのルート][lnk-devguide-messaging-routes]を使用して、個々のメッセージに対して評価される式に基づき、デバイスからクラウドへのメッセージをさまざまなエンドポイントに送るよう、IoT Hub を構成することができます。

ルートの[条件][lnk-query-expressions]では、ツインおよびジョブ クエリの条件と同じ IoT Hub クエリ言語を使用します。 ルートの条件は、次の JSON の表現を仮定して、メッセージのプロパティで評価されます。

        {
            "$messageId": "",
            "$enqueuedTime": "",
            "$to": "",
            "$expiryTimeUtc": "",
            "$correlationId": "",
            "$userId": "",
            "$ack": "",
            "$connectionDeviceId": "",
            "$connectionDeviceGenerationId": "",
            "$connectionAuthMethod": "",
            "$content-type": "",
            "$content-encoding": ""

            "userProperty1": "",
            "userProperty2": ""
        }

メッセージのシステム プロパティには、`'$'` シンボルが付きます。
ユーザー プロパティは、常にその名前でアクセスされます。 ユーザー プロパティの名前が、システム プロパティと一致する場合 (`$to` など)、とユーザー プロパティは、`$to` 式で取得されます。
システム プロパティは、常にかっこ `{}` を使用してアクセスできます。たとえば、`{$to}` 式を使用してシステム プロパティ `to` にアクセスできます。 かっこで囲まれたプロパティ名は、常に対応するシステム プロパティを取得します。

プロパティ名では大文字と小文字は区別されません。

> [!NOTE]
> メッセージのプロパティはすべて文字列です。 [開発者ガイド][lnk-devguide-messaging-format]に記載されているように、システムのプロパティは現在クエリで使用できません。
>

たとえば、`messageType` プロパティを使用する場合、すべてのテレメトリを&1; つのエンドポイントにルーティングし、すべての警告を別のエンドポイントにルーティングすることを望むかもしれません。 次の式を記述してテレメトリをルーティングします。

        messageType = 'telemetry'

また、次の式を記述して警告メッセージをルーティングします。

        messageType = 'alert'

ブール式と関数もサポートされています。 この機能により、例えば次のように重大度レベルを区別できます。

        messageType = 'alerts' AND as_number(severity) <= 2

サポートされている演算子と関数の完全な一覧については、「[式と条件][lnk-query-expressions]」セクションを参照してください。

## <a name="basics-of-an-iot-hub-query"></a>IoT Hub クエリの基礎
IoT Hub のすべてのクエリは、SELECT 句と FROM 句、オプションの WHERE 句と GROUP BY で構成されます。 各クエリは JSON ドキュメントのコレクション (デバイス ツインなど) で実行されます。 FROM 句は (**devices** や **devices.jobs**) で繰り返されるドキュメント コレクションを示します。 次に、WHERE 句でフィルター処理が適用されます。 集計の場合、この手順の結果は GROUP BY 句で指定した方法でグループ化され、グループごとに、SELECT 句で指定した方法で列が生成されます。

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>FROM 句
**FROM <from_specification>** 句には、デバイス ツインのクエリを実行するための **FROM devices** とデバイスの詳細ごとにジョブのクエリを実行するための **FROM devices.jobs** の&2; つの値のみを使用できます。

## <a name="where-clause"></a>WHERE 句
**WHERE <filter_condition>** 句はオプションです。 WHERE 句は、FROM コレクションの JSON ドキュメントを結果に含めるために満たす必要がある条件を指定します。 結果に含めるためには、指定された条件についてすべての JSON ドキュメントが "true" と評価される必要があります。

使用できる条件は、「[式と条件][lnk-query-expressions]」セクションに記載されています。

## <a name="select-clause"></a>SELECT 句
SELECT 句 (**SELECT <select_list>**) は必須であり、クエリで取得する値の種類を指定します。 これは、新しい JSON オブジェクトを生成するために使用する JSON 値を指定します。
FROM コレクションのフィルター処理されたサブセット (さらにオプションでグループ化されたもの) の各要素に対して、プロジェクション フェーズでは、SELECT 句で指定した値で構成される新しい JSON オブジェクトを生成します。

SELECT 句の文法を次に示します。

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count()
            | avg(<projection_element>)
            | sum(<projection_element>)
            | min(<projection_element>)
            | max(<projection_element>)

この場合、**attribute_name** は FROM コレクションの JSON ドキュメントのプロパティを参照します。 SELECT 句の例は、「[ツインのクエリの概要][lnk-query-getstarted]」セクションに記載されています。

現在のところ、**SELECT \*** 以外の選択句は、デバイス ツインの集計クエリでのみサポートされています。

## <a name="group-by-clause"></a>GROUP BY 句
**GROUP BY <group_specification>** 句はオプションの手順であり、WHERE 句で指定したフィルターの後か、SELECT で指定したプロジェクションの前で実行できます。 属性の値に基づいてドキュメントをグループ化します。 これらのグループを使用すると、SELECT 句で指定した方法で、集計された値が生成されます。

次は GROUP BY を使用したクエリの例です。

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

GROUP BY の正式な構文は次のとおりです。

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

この場合、**attribute_name** は FROM コレクションの JSON ドキュメントのプロパティを参照します。

現在のところ、GROUP BY 句はデバイス ツインに対するクエリの場合にのみサポートされています。

## <a name="expressions-and-conditions"></a>式と条件
*式* とはおおまかに次のように言い表すことができます。

* JSON 型 (ブール値、数値、文字列、配列、オブジェクトなど) のインスタンスに対して評価を行う
* 組み込みの演算子と関数を使用するデバイス JSON ドキュメントと定数からくるデータの操作として定義される

*条件*はブール値に対して評価する式のことです。 そのため、ブール値 **true** と異なる定数はすべて **false** (**null**、**undefined**、オブジェクトまたは配列インスタンス、文字列、明らかなブール値 **false** を含む) と見なされます。

式の構文は次のとおりです。

        <expression> ::=
            <constant> |
            attribute_name |
            <function_call> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <function_call> ::=
            <function_name> '(' expression ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant>
            | <number_constant>
            | <string_constant>
            | <array_constant>

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

各値の説明:

| シンボル | 定義 |
| --- | --- |
| attribute_name | **FROM** コレクションの JSON ドキュメントのプロパティ。 |
| binary_operator | 「[演算子](#operators)」セクションに記載されている&2; 項演算子。 |
| function_name| 「[関数](#functions)」セクションに記載されている関数。 |
| decimal_literal |10 進表記で表される浮動小数点数。 |
| hexadecimal_literal |文字列 ‘0x’ の後に&16; 進数の文字列を続けて表された数値。 |
| string_literal |文字列リテラルは、0 個以上の Unicode 文字のシーケンスまたはエスケープ シーケンスによって表される Unicode 文字列です。 文字列リテラルは、単一引用符 (アポストロフィ: ') または二重引用符 (引用符:") で囲みます。 使用できるエスケープ: 4 つの 16 進数字によって定義された Unicode 文字の `\'`、`\"`、`\\`、`\uXXXX`。 |

### <a name="operators"></a>演算子
次の演算子がサポートされています。

| ファミリ | 演算子 |
| --- | --- |
| 算術 |+、-、*、/、% |
| 論理 |AND、OR、NOT |
| 比較 |=、!=、<、>、<=、>=、<> |

### <a name="functions"></a>関数
ツインとジョブのクエリにおいて、サポートされている唯一の関数は次のとおりです。

| 関数 | Description |
| -------- | ----------- |
| IS_DEFINED(property) | プロパティに値が代入されているかどうかを示すブール値を返します (`null` を含む)。 |

ルートの条件では、次の計算関数がサポートされています。

| 関数 | Description |
| -------- | ----------- |
| ABS(x) | 指定された数値式の絶対値 (正の値) を返します。 |
| EXP(x) | 指定された数値式の指数値を返します (e^x)。 |
| POWER(x,y) | 指定されたべき乗の指定された式の値を返します (x^y)。|
| SQUARE(x)    | 指定された数値の&2; 乗を返します。 |
| CEILING(x) | 指定された数値式以上の最小の整数値を返します。 |
| FLOOR(x) | 指定された数値式未満の最大の整数を返します。 |
| SIGN(x) | 指定された数値式の正 (+1)、ゼロ (0)、または負 (-1) の符号を返します。|
| SQRT(x) | 指定された数値の&2; 乗を返します。 |

ルートの条件では、次の型の確認とキャスト関数がサポートされています。

| 関数 | Description |
| -------- | ----------- |
| AS_NUMBER | 入力文字列を数値に変換します。 入力が数値の場合は `noop`、文字列が数値を表していない場合は `Undefined` です。|
| IS_ARRAY | 指定した式の型が配列であるかどうかを示すブール値を返します。 |
| IS_BOOL | 指定した式の型がブール値であるかどうかを示すブール値を返します。 |
| IS_DEFINED | プロパティに値が代入されているかどうかを示すブール値を返します。 |
| IS_NULL | 指定した式の型が null であるかどうかを示すブール値を返します。 |
| IS_NUMBER | 指定した式の型が数値であるかどうかを示すブール値を返します。 |
| IS_OBJECT | 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。 |
| IS_PRIMITIVE | 指定した式の型がプリミティブ (文字列、ブール値、数値、または `null`) であるかどうかを示すブール値を返します。 |
| IS_STRING | 指定した式の型が文字列であるかどうかを示すブール値を返します。 |

ルートの条件では、次の文字列関数がサポートされています。

| 関数 | 説明 |
| -------- | ----------- |
| CONCAT(x, …) | 2 つ以上の文字列値を連結した結果である文字列を返します。 |
| LENGTH(x) | 指定された文字列式の文字数を返します。|
| LOWER(x) | 文字列式の大文字データを小文字に変換して返します。 |
| UPPER(x) | 文字列式の小文字データを大文字に変換して返します。 |
| SUBSTRING(string, start [, length]) | 指定された文字のゼロベースの位置で始まる文字列式の一部を返し、指定された長さまたは文字列の末尾まで続きます。 |
| INDEX_OF(string, fragment) | 1 つ目に指定された文字列式内で&2; つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。|
| STARTS_WITH(x, y) | 1 つ目の文字列式が&2; つ目の文字列で始まっているかどうかを示すブール値を返します。 |
| ENDS_WITH(x, y) | 1 つ目の文字列式が&2; つ目の文字列で終了しているかどうかを示すブール値を返します。 |
| CONTAINS(x,y) | 1 つ目の文字列式に&2; つ目の文字列式が含まれているかどうかを示すブール値を返します。 |

## <a name="next-steps"></a>次のステップ
[Azure IoT Hub SDK][lnk-hub-sdks] を使用して、アプリでクエリを実行する方法を説明します。

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messaging.md#routing-rules
[lnk-devguide-messaging-format]: iot-hub-devguide-messaging.md#message-format


[lnk-hub-sdks]: iot-hub-devguide-sdks.md

