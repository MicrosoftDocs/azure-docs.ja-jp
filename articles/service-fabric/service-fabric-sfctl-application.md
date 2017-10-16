---
title: Azure Service Fabric CLI- sfctl application | Microsoft Docs
description: "Service Fabric CLI- sfctl application のコマンドについて説明します。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: dc57c813a6aecabc21ac3931b7294bce909778d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-application"></a>sfctl application
アプリケーションおよびアプリケーションの種類を作成、削除、および管理します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
| create       | 指定された説明を使用して、Service Fabric アプリケーションを作成します。|
| 削除       | 既存の Service Fabric アプリケーションを削除します。|
| deployed     | Service Fabric ノードにデプロイされたアプリケーションに関する情報を取得します。|
| deployed-health | Service Fabric ノードにデプロイされたアプリケーションの正常性に関する情報を
                      取得します。|
| deployed-list| Service Fabric ノードにデプロイされたアプリケーションの一覧を取得します。|
| health       | Service Fabric アプリケーションの正常性を取得します。|
| info         | Service Fabric アプリケーションの情報を取得します。|
| list         | パラメーターとして指定されたフィルターに一致する Service Fabric クラスターで作成されたアプリケーションの一覧を
                      取得します。|
| load | Service Fabric アプリケーションの負荷情報を取得します。 |
| manifest     | アプリケーションの種類を記述するマニフェストを取得します。|
| provision    | Service Fabric アプリケーションの種類をプロビジョニングするか、クラスターに登録します。|
| report-health| Service Fabric アプリケーションの正常性レポートを送信します。|
| type         | 指定した名前と完全に一致する Service Fabric クラスター内のアプリケーションの種類の一覧を
                      取得します。|
| type-list    | Service Fabric クラスター内のアプリケーションの種類の一覧を取得します。|
| unprovision  | Service Fabric アプリケーションの種類をクラスターから削除または登録解除します。|
| upgrade      | Service Fabric クラスターで、アプリケーションのアップグレードを開始します。|
| upgrade-resume  | Service Fabric クラスターで、アプリケーションのアップグレードを再開します。|
| upgrade-rollback| Service Fabric クラスター内で現在進行中のアプリケーションのアップグレードのロールバックを
                      開始します。|
| upgrade-status  | このアプリケーションで実行された最新のアップグレードの詳細を取得します。|
| upload       | Service Fabric アプリケーション パッケージをイメージ ストアにコピーします。|

## <a name="sfctl-application-create"></a>sfctl application create
指定された説明を使用して、Service Fabric アプリケーションを作成します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --app-name    [必須]| 'fabric:' URI スキームを含むアプリケーションの名前。|
| --app-type    [必須]| アプリケーション マニフェストで検出されたアプリケーションの種類名。|
| --app-version [必須]| アプリケーション マニフェストで定義されているアプリケーションの種類のバージョン。|
| --max-node-count     | Service Fabric がこのアプリケーション用に容量を予約するノードの最大数。 このアプリケーションのサービスがこれらすべてのノードに配置されるわけではありません。|
| --metrics            | アプリケーションの容量のメトリックの説明の JSON でエンコードされた一覧。 メトリックは名前として定義され、アプリケーションが存在する各ノードの一連の容量に関連付けられます。|
| --min-node-count     | Service Fabric がこのアプリケーション用に容量を予約するノードの最小数。 このアプリケーションのサービスがこれらすべてのノードに配置されるわけではありません。|
| --parameters         | アプリケーションの作成時に適用される、アプリケーション パラメーター オーバーライドの JSON でエンコードされた一覧。|
| --timeout -t         | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug              | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h            | このヘルプ メッセージを表示して終了します。|
| --output -o          | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query              | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose            | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-delete"></a>sfctl application delete
既存の Service Fabric アプリケーションを削除します。

既存の Service Fabric アプリケーションを削除します。 アプリケーションを削除するには、その前にアプリケーションが作成されている必要があります。 アプリケーションを削除すると、そのアプリケーションの一部となっているすべてのサービスが削除されます。 既定では Service Fabric は、安全な方法でサービスのレプリカを閉じてからサービスを削除しようとします。 ただし、サービスにレプリカを正常に終了できない問題がある場合は、削除操作に時間がかかったり、スタックする場合があります。 正常に閉じるシーケンスをスキップし、アプリケーションとそのすべてのサービスを強制的に削除するには、省略可能な ForceRemove フラグを使用します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-id [必須]| アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。 次から始まります。
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --force-remove          | 正常なシャットダウン シーケンスを経由せずに、Service Fabric アプリケーションまたはサービスを強制的に削除します。 このパラメーターを使用して、レプリカの正常な終了を妨げるサービス コードでの問題により、削除がタイムアウトしたアプリケーションまたはサービスを強制的に削除することができます。| | --timeout -t            | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h               | このヘルプ メッセージを表示して終了します。|
| --output -o             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Service Fabric ノードにデプロイされたアプリケーションに関する情報を取得します。|
|     
### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-id [必須]| アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。 次から始まります。
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --node-name      [必須]| ノード名。| | --timeout -t            | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h               | このヘルプ メッセージを表示して終了します。|
| --output -o             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-health"></a>sfctl application health
Service Fabric アプリケーションの正常性を取得します。

Service Fabric アプリケーションの正常性の状態を返します。 応答で、Ok、Error、Warning のいずれかの正常性の状態が報告されます。 正常性ストアにエンティティが見つからない場合は、Error が返されます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-id                 [必須]| アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、
                                                 hierarchical names are delimited with the "~"
                                                 character. For example, if the application name is
                                                 "fabric://myapp/app1", the application identity
                                                 would be "myapp~app1" in 6.0+ and "myapp/app1" in
                                                 previous versions.|
| --deployed-applications-health-state-filter| 正常性の状態に基づくアプリケーションの正常性クエリの結果で返されるデプロイされたアプリケーションの正常性の状態オブジェクトをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するデプロイ済みアプリケーションのみが返されます。 すべてのデプロイ済みアプリケーションは、集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。                        たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のデプロイ済みアプリケーションの正常性の状態が返されます。 -                        Default - 既定値。 任意の HealthState と一致します。                        値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。                        値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 -                        Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。 - Error                        - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。| | --events-health-state-filter            | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルターできます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。                        状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 -                        Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。 - Error                        - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。| | --exclude-health-statistics | 正常性の統計情報がクエリ結果の一部として返されるかどうかを示します。 既定では false です。 統計情報は正常性の状態が Ok、Warning、および Error の子エンティティの数を示します。| | --services-health-state-filter          | 正常性の状態に基づいたサービスの正常性クエリの結果で返されたサービスの正常性の状態オブジェクトをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するサービスのみが返されます。 すべてのサービスが集計された正常性の状態を評価するために使用されます。                        指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のサービスの正常性の状態が返されます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。                        - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。 - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All -                        任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。| | --timeout -t                            | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                               | このヘルプ メッセージを表示して終了します。|
| --output -o                             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-info"></a>sfctl application info
Service Fabric アプリケーションの情報を取得します。

Service Fabric クラスターで作成された、または作成中の、パラメーターとして指定されているものと同じ名前のアプリケーションの情報を返します。 応答には、名前、種類、状態、パラメーター、およびアプリケーションに関するその他の詳細が含まれています。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-id      [必須]| アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は区切られます。
                                      with the "~" character. For example, if the application name
                                      is "fabric://myapp/app1", the application identity would be
                                      "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --exclude-application-parameters| アプリケーションのパラメーターを結果から除外するかどうかを指定するフラグ。| | --timeout -t                 | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                      | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                    | このヘルプ メッセージを表示して終了します。|
| --output -o                  | 出力形式。  使用できる値は、json、jsonc、table、tsv です。             既定値は json です。|
| --query                      | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                    | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-list"></a>sfctl application list
パラメーターとして指定されたフィルターに一致する Service Fabric クラスターで作成されたアプリケーションの一覧を取得します。

Service Fabric クラスターで作成された、または作成中の、パラメーターとして指定されているフィルターに一致するアプリケーションの情報を返します。 応答には、名前、種類、状態、パラメーター、およびアプリケーションに関するその他の詳細が含まれています。 アプリケーションが 1 ページに収まらない場合、1 ページの結果と継続トークンが返されます。これは次のページを取得するために使用できます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
|--application-definition-kind-filter| ApplicationDefinitionKind にフィルターを適用するために使用
                                          application query operations. - Default - Default value.
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 0. - All -
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 65535. -
                                          ServiceFabricApplicationDescription - Filter that matches
                                          input with ApplicationDefinitionKind value
                                          ServiceFabricApplicationDescription. The value is 1. -
                                          Compose - Filter that matches input with
                                          ApplicationDefinitionKind value Compose. The value is 2.
                                          Default: 65535.|
| --application-type-name      | アプリケーションの種類名はクエリを実行するアプリケーションをフィルター処理するために使用されます。 この値には、アプリケーションの種類のバージョンを含めることはできません。| | --continuation-token         | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。| --exclude-application-parameters| アプリケーションのパラメーターを結果から除外するかどうかを指定するフラグ。| | --timeout -t                 | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                      | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                    | このヘルプ メッセージを表示して終了します。|
| --output -o                  | 出力形式。  使用できる値は、json、jsonc、table、tsv です。             既定値は json です。|
| --query                      | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                    | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-load"></a>sfctl application load
Service Fabric アプリケーションの負荷情報を取得します。

        Returns the load information about the application that was created or in the process of
        being created in the Service Fabric cluster and whose name matches the one specified as the
        parameter. The response includes the name, minimum nodes, maximum nodes, the number of nodes
        the app is occupying currently, and application load metric information about the
        application.

### <a name="arguments"></a>引数
|引数|Description|
| --- | --- |
|--application-id [必須]| アプリケーションの ID。 これは、通常の完全な名前
                                 the application without the 'fabric:' URI scheme. Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions. |
| --timeout -t               | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数
|引数|Description|
| --- | --- |
|--debug                    | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
    --help -h                  | このヘルプ メッセージを表示して終了します。|
    --output -o                | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は
                                 json です。|
    --query                    | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を
                                 参照してください。|
    --verbose                  | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
アプリケーションの種類を記述するマニフェストを取得します。
        
アプリケーションの種類を記述するマニフェストを取得します。 応答には、アプリケーション マニフェスト XML が文字列として含まれています。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-type-name    [必須]| アプリケーションの種類名。|
| --application-type-version [必須]| アプリケーションの種類のバージョン。|
| --timeout -t                      | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                           | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                         | このヘルプ メッセージを表示して終了します。|
| --output -o                       | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                  既定値は json です。|
| --query                           | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                         | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-provision"></a>sfctl application provision
Service Fabric アプリケーションの種類をプロビジョニングするか、クラスターに登録します。
        
Service Fabric アプリケーションの種類をプロビジョニングするか、クラスターに登録します。 すべての新しいアプリケーションをインスタンス化するには、これが必要です。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-type-build-path [必須]| アプリケーション パッケージへのイメージ ストアの相対パス。|
| --timeout -t                         | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                              | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                            | このヘルプ メッセージを表示して終了します。|
| --output -o                          | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                              | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                            | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-type"></a>sfctl application type

指定した名前と完全に一致する Service Fabric クラスター内のアプリケーションの種類の一覧を取得します。

Service Fabric クラスターでプロビジョニングされた、またはプロビジョニング中のアプリケーションの種類に関する情報を返します。 これらは、名前がパラメーターとして指定されたものと完全に一致し、指定されたクエリ パラメーターに適合するアプリケーションの種類の結果です。 アプリケーションの種類の名前に一致するアプリケーションの種類のすべてのバージョンが返されます。各バージョンは 1 つのアプリケーションの種類として返されます。 応答には、名前、バージョン、状態、およびアプリケーションの種類に関するその他の詳細が含まれています。 これがページング クエリです。つまり、すべてのアプリケーションの種類が 1 ページに収まらない場合、1 ページの結果と次のページを取得するために使用できる継続トークンが返されます。 たとえば、10 個のアプリケーションの種類があって、1 ページに最初の 3 つのアプリケーションの種類しか収まらない場合、または max results が 3 に設定されている場合は、3 が返されます。 残りの結果にアクセスするには、返された継続トークンを次のクエリで使用して、後続のページを取得します。 後続のページが存在しない場合は、空の継続トークンが返されます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-type-name [必須]| アプリケーションの種類名。|
| --continuation-token           | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。|
| --exclude-application-parameters  | アプリケーションのパラメーターを結果から除外するかどうかを指定するフラグ。|
| --max-results                  | ページング クエリの一部として返される結果の最大数。 このパラメーターは、返される結果の数に上限を定義します。 返された結果が、構成で定義したメッセージの最大サイズの制限に収まらない場合は、指定した最大結果数よりも少なくなる場合があります。 このパラメーターがゼロまたは指定されていない場合、ページング クエリには、応答メッセージに収まるできるだけ多くの結果が含まれます。|
| --timeout -t                   | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                        | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                      | このヘルプ メッセージを表示して終了します。|
| --output -o                    | 出力形式。  使用できる値は、json、jsonc、table、tsv です。               既定値は json です。|
| --query                        | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                      | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Service Fabric アプリケーションの種類をクラスターから削除または登録解除します。

Service Fabric アプリケーションの種類をクラスターから削除または登録解除します。 この操作は、アプリケーションの種類のすべてのアプリケーション インスタンスが削除されている場合にのみ実行できます。 アプリケーションの種類が登録解除されると、この特定のアプリケーションの種類に対して新しいアプリケーション インスタンスを作成できません。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-type-name    [必須]| アプリケーションの種類名。|
| --application-type-version [必須]| アプリケーションの種類のバージョン。|
| --timeout -t                      | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                           | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                         | このヘルプ メッセージを表示して終了します。|
| --output -o                       | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                  既定値は json です。|
| --query                           | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                         | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Service Fabric クラスターで、アプリケーションのアップグレードを開始します。

指定されたアプリケーションのアップグレード パラメーターを検証し、パラメーターが有効であれば、アプリケーションのアップグレードを開始します。 アップグレードの説明により既存のアプリケーションの説明が置き換えられることに注意してください。 これは、パラメーターが指定されていない場合、アプリケーションの既存のパラメーターが空のパラメーター リストで上書きされることを意味します。 これにより、アプリケーションがアプリケーション マニフェストからのパラメーターの既定値を使用することになります。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --app-id             [必須]| アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 次に
        example, if the application name is 'fabric://myapp/app1', the application identity would be
        'myapp~app1' in 6.0+ and 'myapp/app1' in previous versions.|
| --app-version        [必須]| ターゲット アプリケーションのバージョン。| | --parameters         [必須]| アプリケーションのアップグレード時に適用される、アプリケーション パラメーター オーバーライドの JSON でエンコードされた一覧。| | --default-service-health-policy| サービスの種類の正常性を評価するために既定で使用される正常性ポリシーの JSON でエンコードされた仕様。| | --failure-action            | 監視対象のアップグレードで監視ポリシーまたは正常性ポリシーの違反が発生したときに実行するアクション。| | --force-restart             | コードのバージョンが変更されていないときでも、アップグレード中にプロセスを強制的に再起動します。| | --health-check-retry-timeout| アプリケーションまたはクラスターが正常でない場合に、正常性評価を再試行する時間。この時間を超えると、failure action が実行されます。 ミリ秒で測定されます。  既定値は PT0H10M0S です。| | --health-check-stable-duration | アプリケーションまたはクラスターが正常な状態である必要がある時間。この時間を超えると、アップグレードが次のアップグレード ドメインに進みます。            ミリ秒で測定されます。  既定値は PT0H2M0S です。| | --health-check-wait-duration| ドメインのアップグレードを完了後、待機する時間。この時間を超えると、正常性ポリシーが適用されます。 ミリ秒で測定されます。            既定値は 0 です。| | --max-unhealthy-apps        | 異常なデプロイ済みアプリケーションの割合の最大許容値。 0 から 100 の数値として表されます。| | --mode                      | ローリング アップグレード中の正常性を監視するために使用するモード。            既定値は UnmonitoredAuto です。| | --replica-set-check-timeout | アップグレード ドメインの処理をブロックし、予期しない問題が発生したときに可用性の損失を防ぐための最長時間。 秒単位で測定されます。| | --service-health-policy     | サービスの種類名ごとにサービスの種類の正常性ポリシーを持つ JSON でエンコードされたマップ。 既定ではマップは空です。| | --timeout -t                | サーバー タイムアウト (秒)。  既定値は 60 です。| | --upgrade-domain-timeout    | 各ドメインがアップグレードを完了する必要がある時間。この時間を超えると、FailureAction が実行されます。 ミリ秒で測定されます。  既定値は P10675199DT02H48M05.4775807S です。| | --upgrade-timeout           | 全体のアップグレードを完了する必要がある時間。この時間を超えると、FailureAction が実行されます。 ミリ秒で測定されます。  既定値は P10675199DT02H48M05.4775807S です。| | --warning-as-error          | 正常性評価の警告をエラーと同じ重大度で扱います。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                     | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                   | このヘルプ メッセージを表示して終了します。|
| --output -o                 | 出力形式。  使用できる値は、json、jsonc、table、tsv です。            既定値は json です。|
| --query                     | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                   | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-application-upload"></a>sfctl application upload
Service Fabric アプリケーション パッケージをイメージ ストアにコピーします。

必要に応じて、パッケージ内の各ファイルのアップロードの進行状況を表示します。 アップロードの進行状況は `stderr` に送信されます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --path [必須]| ローカル アプリケーション パッケージへのパス。|
|--imagestore-string| アプリケーション パッケージのアップロード先のイメージ ストア。  既定値は
                         fabric: ImageStore です。|
| --show-progress  | 大きいパッケージのファイル アップロードの進行状況を表示します。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug       | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h     | このヘルプ メッセージを表示して終了します。|
| --output -o   | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query       | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を
                       参照してください。|
| --verbose     | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。