---
title: Azure Service Fabric CLI- sfctl choas| Microsoft Docs
description: "Service Fabric CLI sfctl chaos のコマンドについて説明します。"
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
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 7118ec3f5aeae40fa7ecc592309a28b585c651a1
ms.contentlocale: ja-jp
ms.lasthandoff: 09/26/2017

---
# <a name="sfctl-chaos"></a>sfctl chaos
chaos テスト サービスの開始、停止、レポートを実行します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    report| 渡された継続トークン、または渡された時間範囲に基づく Chaos レポートの次のセグメントを取得します。|
|    start | クラスターで Chaos がまだ実行されていない場合、Chaos パラメーターで指定した Chaos の実行を開始します。|
|    stop  | クラスターで Chaos が実行されている場合は停止します。実行されていない場合は何も行われません。|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
渡された継続トークン、または渡された時間範囲に基づく Chaos レポートの次のセグメントを取得します。

混乱レポートの次のセグメントを取得するように ContinuationToken を指定するか、StartTimeUtc と EndTimeUtc を使用して時間範囲を指定することができますが、同じ呼び出しで、ContinuationToken と時間範囲の両方を指定することはできません。 100 を超える混乱イベントが存在する場合、混乱レポートがセグメントで返され、セグメントに 100 を超える混乱イベントが含まれています。 

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --continuation-token| 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。|
| --end-time-utc   | 時間範囲の終了時刻を表すティック数
                          Chaos report is to be generated. Please consult [DateTime.Ticks
                          Property](https://msdn.microsoft.com/en-
                          us/library/system.datetime.ticks%28v=vs.110%29) for details about tick.|
| --start-time-utc | Chaos レポートが生成される時間範囲の開始時刻を表すティック数。 ティックの詳細については、[DateTime.Ticks プロパティ](https://msdn.microsoft.com/en- us/library/system.datetime.ticks%28v=vs.110%29) を参照してください。| | --timeout -t     | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug          | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h        | このヘルプ メッセージを表示して終了します。|
| --output -o      | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query          | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose        | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
クラスターで Chaos がまだ実行されていない場合、Chaos パラメーターで指定した Chaos の実行を開始します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --app-type-health-policy-map  | 特定のアプリケーションの種類の異常なアプリケーションの最大割合の JSON でエンコードされた一覧。 各エントリは、キーとしてアプリケーションの種類名を指定し、値として指定したアプリケーションの種類のアプリケーションを評価するために使用される MaxPercentUnhealthyApplications を表す整数を指定します。|
| --disable-move-replica-faults | move primary と move secondary の障害を無効にします。|
| --max-cluster-stabilization| すべてのクラスター エンティティが安定し正常な状態になるまで待機する最長時間。  既定値は 60 です。|
| --max-concurrent-faults    | 各イテレーションで誘発される同時実行のエラーの最大数。           既定値は 1 です。|
| --max-percent-unhealthy-apps  | Chaos 中にクラスターの正常性を評価するときに、エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。|
| --max-percent-unhealthy-nodes | Chaos 中にクラスターの正常性を評価するときに、エラーを報告する前の異常なノードの最大許容パーセンテージ。|
| --time-to-run              | 自動停止するまでに Chaos が実行される合計時間 (秒)。 最大許容値は、4,294,967,295 (System.UInt32.MaxValue) です。  既定値は 4294967295 です。|
| --timeout -t               | サーバー タイムアウト (秒)。  既定値は 60 です。|
| --wait-time-between-faults | 1 つのイテレーション内で連続する障害の間の時間 (秒単位)。  既定値は 20 です。|
| --wait-time-between-iterations| Chaos の 2 つの連続するイテレーション間の時間区切り (秒単位)。  既定値は 30 です。|
| --warning-as-error         | Chaos 中にクラスターの正常性を評価するときは、warning を error と同じ重大度で扱います。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                    | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                  | このヘルプ メッセージを表示して終了します。|
| --output -o                | 出力形式。  使用できる値は、json、jsonc、table、tsv です。           既定値は json です。|
| --query                    | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                  | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
クラスターで Chaos が実行されている場合は停止します。実行されていない場合は何も行われません。

Chaos がこれ以降の障害のスケジュールを行うのを停止しますが、実行中の障害には影響しません。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --timeout -t| サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug  | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h| このヘルプ メッセージを表示して終了します。|
| --output -o | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query  | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose| ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。
