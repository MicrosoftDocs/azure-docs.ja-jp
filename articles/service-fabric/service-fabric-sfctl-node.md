---
title: Azure Service Fabric CLI- sfctl node | Microsoft Docs
description: "Service Fabric CLI sfctl node のコマンドについて説明します。"
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
ms.openlocfilehash: 76037c7b4a2f7ada314a9360e3990245e6fbc06c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-node"></a>sfctl node
クラスターを形成するノードを管理します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    disable       | 指定した非アクティブ化インテントを使用して、Service Fabric クラスターのノードを非アクティブ化します。|
|    enable        | 現在非アクティブ化されている Service Fabric クラスター ノードをアクティブにします。|
|    health        | Service Fabric ノードの正常性を取得します。|
|    info          | Service Fabric クラスター内のノードの一覧を取得します。|
|    list          | Service Fabric クラスター内のノードの一覧を取得します。|
|    load          | Service Fabric ノードの読み込み情報を取得します。|
|    remove-state  | ノード上の永続化状態が完全に削除または失われたことを Service Fabric に通知します。|
|    report-health | Service Fabric ノードの正常性レポートを送信します。|
|    restart       | Service Fabric クラスター ノードを再起動します。|
|    transition    | クラスター ノードを開始または停止します。|
|    transition-status| StartNodeTransition を使用して開始された操作の進行状況を取得します。|


## <a name="sfctl-node-disable"></a>sfctl node disable
指定した非アクティブ化インテントを使用して、Service Fabric クラスターのノードを非アクティブ化します。

指定した非アクティブ化インテントを使用して、Service Fabric クラスターのノードを非アクティブ化します。 非アクティブ化の進行中は、非アクティブ化インテントを増やすことができますが、減らすことはできません (たとえば、Pause インテントを使用して非アクティブ化されたノードは、Restart を使用してさらに非アクティブ化できますが、逆はできません)。 ノードを非アクティブ化したら、後からノードのアクティブ化操作を使用していつでも再アクティブ化することができます。 非アクティブ化が完了していない場合、これは非アクティブ化をキャンセルします。 非アクティブ化中にダウンして復帰するノードは、再アクティブ化してからでないと、サービスをそのノードに配置できません。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name [必須]| ノード名。|
| --deactivation-intent | ノードを非アクティブ化するインテントまたは理由について記述します。 指定できる値は次のとおりです。 - Pause - 一時停止する必要があるノードを示します。 値は 1 です。 - Restart - インテントが短時間の後に再起動されるノード用であることを示します。 値は 2 です。 -        RemoveData - インテントがデータを削除するノード用であることを示します。 値は 3 です。 が必要です。|
| --timeout -t       | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug            | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h          | このヘルプ メッセージを表示して終了します。|
| --output -o        | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query            | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose          | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-node-enable"></a>sfctl node enable
現在非アクティブ化されている Service Fabric クラスター ノードをアクティブにします。

現在非アクティブ化されている Service Fabric クラスター ノードをアクティブにします。 アクティブ化すると、ノードは再び新しいレプリカを配置するための有効なターゲットになり、ノードに残っている非アクティブ化されたすべてのレプリカが再アクティブ化されます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name [必須]| ノード名。|
| --timeout -t       | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug            | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h          | このヘルプ メッセージを表示して終了します。|
| --output -o        | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query            | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose          | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-node-health"></a>sfctl node health
Service Fabric ノードの正常性を取得します。

Service Fabric ノードの正常性を取得します。 正常性状態に基づいてノードで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 名前を指定したノードが正常性ストアに存在しない場合、エラーが返されます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name       [必須]| ノード名。|
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

## <a name="sfctl-node-info"></a>sfctl node info
Service Fabric クラスター内のノードの一覧を取得します。

Service Fabric クラスター内の特定のノードに関する情報を取得します。 応答には、名前、状態、ID、正常性、アップタイム、およびノードに関するその他の詳細が含まれます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name [必須]| ノード名。|
| --timeout -t       | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug            | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h          | このヘルプ メッセージを表示して終了します。|
| --output -o        | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query            | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose          | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-node-list"></a>sfctl node list
Service Fabric クラスター内のノードの一覧を取得します。

ノード エンドポイントは、Service Fabric クラスター内のノードに関する情報を返します。 応答には、名前、状態、ID、正常性、アップタイム、およびノードに関するその他の詳細が含まれます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --continuation-token| 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。      この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。|
| --node-status-filter| NodeStatus に基づいてノードをフィルターできます。 指定したフィルター値に一致するノードだけが返されます。 フィルター値には、次のいずれかを指定できます。 - default - このフィルター値は、状態が Unknown または Removed を除くすべてのノードと一致します。 -      all - このフィルター値は、すべてのノードと一致します。 - up - このフィルター値は、アップしているノードと一致します。 - down - このフィルター値は、ダウンしているノードと一致します。 - enabling - このフィルター値は、Enabling の状態を持つ有効化が進行中のノードと一致します。 - disabling -      このフィルター値は、Disabling の状態を持つ無効化が進行中のノードと一致します。 - disabled - このフィルター値は、無効化されたノードと一致します。 - unknown - このフィルター値は、状態が Unknown のノードと一致します。 Service Fabric がそのノードに関する権限情報を持っていない場合は、ノードは Unknown 状態になります。 これは、システムが実行時にノードについて学習する場合に発生します。 - removed - このフィルター値は、状態が Removed のノードと一致します。 これらは、RemoveNodeState API を使用して、クラスターから削除されたノードです。 が必要です。      既定値は default です。|
| --timeout -t     | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug          | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h        | このヘルプ メッセージを表示して終了します。|
| --output -o      | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query          | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose        | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-node-load"></a>sfctl node load
Service Fabric ノードの読み込み情報を取得します。

Service Fabric ノードの読み込み情報を取得します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name [必須]| ノード名。|
| --timeout -t       | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug            | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h          | このヘルプ メッセージを表示して終了します。|
| --output -o        | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query            | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose          | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-node-restart"></a>sfctl node restart
Service Fabric クラスター ノードを再起動します。

既に起動している Service Fabric クラスター ノードを再起動します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-name [必須]| ノード名。|
| --create-fabric-dump  | Fabric ノード プロセスのダンプを作成する場合は True を指定します。 これは大文字小文字を区別します。  既定値は False です。|
| --node-instance-id | ターゲット ノードのインスタンス ID。 インスタンス ID が指定されている場合、ノードの現在のインスタンスと一致する場合にのみ、ノードが再起動されます。 既定値の "0" は任意のインスタンス ID に一致します。 インスタンス ID は、get node query を使用して取得できます。  既定値: 0。|
| --timeout -t       | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug            | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h          | このヘルプ メッセージを表示して終了します。|
| --output -o        | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query            | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose          | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-node-transition"></a>sfctl node transition
クラスター ノードを開始または停止します。

クラスター ノードを開始または停止します。  クラスター ノードはプロセスで、OS インスタンスそのものではありません。
ノードを開始するには、NodeTransitionType パラメーターに "Start" を渡します。 ノードを停止するには、NodeTransitionType パラメーターに "Stop" を渡します。 この API は、移行がまだ完了していない可能性のあるノードを API が返したときに操作を開始します。 操作の進行状況を取得するには、同じ OperationId を使用して GetNodeTransitionProgress を呼び出します。 が必要です。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --node-instance-id         [必須]| ターゲット ノードのノード インスタンス ID。 これは GetNodeInfo API を通じて決定できます。|
| --node-name                [必須]| ノード名。|
| --node-transition-type     [必須]| 実行する移行の種類を示します。                       NodeTransitionType.Start は、停止したノードを開始します。                       NodeTransitionType.Stop は、稼働しているノードを停止します。 -                       Invalid - 予約済み。  API に渡すことはできません。 - Start -                       停止しているノードをアップに移行します。 - Stop - 稼働しているノードを停止に移行します。 が必要です。|
| --operation-id             [必須]| この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。|
| --stop-duration-in-seconds [必須]| ノードを停止させたままにする期間 (秒単位)。  最小値は 600、最大値は 14400 です。 この時間を過ぎると、ノードは自動的に復帰します。|
| --timeout -t                      | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                           | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                         | このヘルプ メッセージを表示して終了します。|
| --output -o                       | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                       既定値は json です。|
| --query                           | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                         | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。