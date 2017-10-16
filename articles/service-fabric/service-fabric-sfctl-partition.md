---
title: Azure Service Fabric CLI- sfctl partition | Microsoft Docs
description: "Service Fabric CLI- sfctl partition のコマンドについて説明します。"
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
ms.openlocfilehash: a5d0ff59803212403281063f47e706433cee64b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-partition"></a>sfctl partition
任意のサービスで、クエリを実行し、パーティションを管理します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    data-loss      | この API は、指定したパーティションのデータ損失を誘発します。|
|    data-loss-status  | StartDataLoss API を使用して開始されたパーティション データ損失操作の進行状況を取得します。|
|    health         | 指定した Service Fabric パーティションの正常性を取得します。|
|    info           | Service Fabric パーティションに関する情報を取得します。|
|    list           | Service Fabric サービスのパーティションの一覧を取得します。|
|    load           | 指定した Service Fabric パーティションの負荷を取得します。|
|    load-reset     | Service Fabric パーティションの現在の負荷をリセットします。|
|    quorum-loss    | 特定のステートフル サービス パーティションをクォーラム損失状態にします。|
|    quorum-loss-status| StartQuorumLoss API を使用して開始されたパーティションのクォーラム損失操作の進行状況を取得します。|
|    recover        | クォーラム損失で現在スタックしている特定のパーティションの復旧を試みる必要があることを Service Fabric クラスターに示します。|
|    recover-all    | クォーラム損失で現在スタックしているすべてのサービス (システム サービスを含む) の回復を試みる必要があることを Service Fabric クラスターに示します。|
|    report-health  | Service Fabric パーティションの正常性レポートを送信します。|
|    restart        | この API は、指定したパーティションのレプリカやインスタンスの一部またはすべてを再起動します。|
|    restart-status | StartPartitionRestart を使用して開始されたパーティション再起動操作の進行状況を取得します。|
|    svc-name       | パーティションの Service Fabric サービスの名前を取得します。|


## <a name="sfctl-partition-health"></a>sfctl partition health
指定した Service Fabric パーティションの正常性を取得します。

指定したパーティションの正常性情報を取得します。 正常性状態に基づいてサービスで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。
パーティションの ReplicaHealthState オブジェクトのコレクションをフィルター処理するには、ReplicasHealthStateFilter を使用します。 正常性ストアに存在しないパーティションを指定した場合、このコマンドレットはエラーを返します。 が必要です。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --partition-id [必須]| パーティションの ID。|
| --events-health-state-filter  | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。                フィルターに一致するイベントのみが返されます。 集計された正常性の状態を評価するために、すべてのイベントが使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値 が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値 が Warning の入力に一致するフィルター。 値は 4 です。 - Error - HealthState 値 が Error の入力に一致するフィルター。 値は 8 です。                - All - 任意の HealthState 値の入力に一致するフィルター。                値は 65535 です。|
|--exclude-health-statistics   | 正常性の統計情報がクエリ結果の一部として返されるかどうかを示します。 既定では false です。 統計情報は、正常性の状態が Ok、Warning、および Error の子エンティティの数を示します。|
| --replicas-health-state-filter| パーティションの ReplicaHealthState オブジェクトのコレクションをフィルター処理できます。 値は、HealthStateFilter のメンバーまたはメンバーのビットごとの演算から取得できます。 フィルターに一致するレプリカのみが返されます。 すべてのレプリカが、集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値 が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値 が Warning の入力に一致するフィルター。 値は 4 です。 - Error - HealthState 値 が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。|
| --timeout -t               | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                    | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                  | このヘルプ メッセージを表示して終了します。|
| --output -o                | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                既定値は json です。|
| --query                    | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。 |
| --verbose                  | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-partition-info"></a>sfctl partition info
Service Fabric パーティションに関する情報を取得します。

パーティション エンドポイントは、指定したパーティションに関する情報を返します。 応答には、パーティション ID、パーティション構成情報、パーティションでサポートされるキー、状態、正常性、およびパーティションに関するその他の詳細が含まれています。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --partition-id [必須]| パーティションの ID。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-partition-list"></a>sfctl partition list
Service Fabric サービスのパーティションの一覧を取得します。

Service Fabric サービスのパーティションの一覧を取得します。 応答には、パーティション ID、パーティション構成情報、パーティションでサポートされるキー、状態、正常性、およびパーティションに関するその他の詳細が含まれています。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --service-id [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "~" 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならばサービス ID は "myapp~app1~svc1" になり、以前のバージョンでは "myapp/app1/svc1" になります。|
| --continuation-token| 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。         システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値をエンコードされた URL にすることはできません。|
| --timeout -t        | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug             | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h           | このヘルプ メッセージを表示して終了します。|
| --output -o         | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query             | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose           | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-partition-load"></a>sfctl partition load
指定した Service Fabric パーティションの負荷を取得します。

指定したパーティションに関する情報を返します。 応答には、負荷情報の一覧が含まれます。 それぞれの情報には、負荷メトリック名、値、および最終報告時刻 (UTC) が含まれます。 が必要です。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --partition-id [必須]| パーティションの ID。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
クォーラム損失で現在スタックしている指定されたパーティションの復旧を試みる必要があることを Service Fabric クラスターに示します。

クォーラム損失で現在スタックしている指定されたパーティションの復旧を試みる必要があることを Service Fabric クラスターに示します。 この操作は、停止しているレプリカを復旧できないことがわかっている場合にのみ実行してください。 この API を不適切に使用すると、データ損失が発生する可能性があります。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --partition-id [必須]| パーティションの ID。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
この API は、指定したパーティションのレプリカやインスタンスの一部またはすべてを再起動します。

この API は、フェールオーバーをテストするために役立ちます。 ステートレス サービス パーティションを対象に使用する場合、RestartPartitionMode を AllReplicasOrInstances にする必要があります。 同じ OperationId を使用して GetPartitionRestartProgress API を呼び出し、進行状況を取得します。 が必要です。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --operation-id [必須]| この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。|
| --partition-id [必須]| パーティションの ID。|
| --restart-partition-mode [必須]| - Invalid - 予約済み。  API に渡すことはできません。 - AllReplicasOrInstances - パーティション内のすべてのレプリカまたはインスタンスが一度に再起動されます。 - OnlyActiveSecondaries - セカンダリ レプリカのみが再起動されます。 が必要です。|
| --service-id [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "~" 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならばサービス ID は "myapp~app1~svc1" になり、以前のバージョンでは "myapp/app1/svc1" になります。|
| --timeout -t                    | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                         | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                       | このヘルプ メッセージを表示して終了します。|
| --output -o                     | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                     既定値は json です。|
| --query                         | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                       | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。