---
title: Azure Service Fabric CLI- sfctl replica| Microsoft Docs
description: "Service Fabric CLI sfctl replica のコマンドについて説明します。"
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
ms.openlocfilehash: 50d185294131e42aaf8b541ab17495fcb40c89dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-replica"></a>sfctl replica
サービス パーティションに属しているレプリカを管理します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    deployed  | Service Fabric ノードにデプロイされているレプリカの詳細を取得します。|
|    deployed-list| Service Fabric ノードにデプロイされているレプリカの一覧を取得します。|
|    health    | Service Fabric ステートフル サービス レプリカまたはステートレス サービス インスタンスの正常性を
                   取得します。|
|    info      | Service Fabric パーティションのレプリカに関する情報を取得します。|
|    list      | Service Fabric サービス パーティションのレプリカに関する情報を取得します。|
|    remove    | ノードで実行されているサービス レプリカを削除します。|
|    report-health| Service Fabric レプリカの正常性レポートを送信します。|
|    restart   | ノードで実行されている永続化されたサービスのサービス レプリカを再起動します。|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Service Fabric ノードにデプロイされているレプリカの詳細を取得します。

Service Fabric ノードにデプロイされているレプリカの詳細を取得します。 情報には、サービスの種類、サービス名、現在のサービス操作、現在のサービス操作の開始日時、パーティション ID、レプリカ/インスタンス ID、報告された負荷およびその他の情報が含まれます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name    [必須]| ノード名。|
| --partition-id [必須]| パーティションの ID。|
| --replica-id   [必須]| レプリカの識別子。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-replica-health"></a>sfctl replica health
Service Fabric ステートフル サービス レプリカまたはステートレス サービス インスタンスの正常性を取得します。

Service Fabric レプリカの正常性を取得します。 正常性状態に基づいてレプリカで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 が必要です。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --partition-id    [必須]| パーティションの ID。|
| --replica-id      [必須]| レプリカの識別子。|
| --events-health-state-filter| 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルターできます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。 - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。|
| --timeout -t             | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                  | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                | このヘルプ メッセージを表示して終了します。|
| --output -o              | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                  | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-replica-info"></a>sfctl replica info
Service Fabric パーティションのレプリカに関する情報を取得します。

応答には、ID、ロール、状態、正常性、ノード名、アップタイム、およびその他のレプリカに関する詳細が含まれます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --partition-id [必須]| パーティションの ID。|
| --replica-id   [必須]| レプリカの識別子。|
| --continuation-token  | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-replica-list"></a>sfctl replica list
Service Fabric サービス パーティションのレプリカに関する情報を取得します。

GetReplicas エンドポイントは、指定したパーティションのレプリカに関する情報を返します。
応答には、ID、ロール、状態、正常性、ノード名、アップタイム、およびその他のレプリカに関する詳細が含まれます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --partition-id [必須]| パーティションの ID。|
| --continuation-token  | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-replica-remove"></a>sfctl replica remove
ノードで実行されているサービス レプリカを削除します。

この API は、Service Fabric クラスターからレプリカを削除することで、Service Fabric レプリカの障害をシミュレートします。 削除により、レプリカが閉じられ、ロールが None のレプリカに移行され、レプリカのすべての状態情報がクラスターから削除されます。 この API は、レプリカの状態の削除パスをテストし、クライアント API を介してレポート フォールトの永続的なパスをシミュレートします。 警告: この API を使用すると、安全性チェックは実行されません。 この API の不適切な使用は、ステートフル サービスのデータ損失につながる場合があります。さらに、forceRemove フラグは、同じプロセスでホストされているその他のすべてのレプリカに影響します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name    [必須]| ノード名。|
| --partition-id [必須]| パーティションの ID。|
| --replica-id   [必須]| レプリカの識別子。|
| --force-remove        | 正常なシャットダウン シーケンスを経由せずに、Service Fabric アプリケーションまたはサービスを強制的に削除します。 このパラメーターを使用して、レプリカの正常な終了を妨げるサービス コードでの問題により、削除がタイムアウトしたアプリケーションまたはサービスを強制的に削除することができます。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-replica-restart"></a>sfctl replica restart
ノードで実行されている永続化されたサービスのサービス レプリカを再起動します。

ノードで実行されている永続化されたサービスのサービス レプリカを再起動します。 警告: この API を使用すると、安全性チェックは実行されません。 この API の不適切な使用は、ステートフル サービスの可用性の損失につながる場合があります。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name    [必須]| ノード名。|
| --partition-id [必須]| パーティションの ID。|
| --replica-id   [必須]| レプリカの識別子。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。