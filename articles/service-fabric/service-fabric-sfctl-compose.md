---
title: Azure Service Fabric CLI- sfctl compose| Microsoft Docs
description: "Service Fabric CLI sfctl compose のコマンドについて説明します。"
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
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 64852471a848cb42fd8a2e67622d309d5e050cfd
ms.contentlocale: ja-jp
ms.lasthandoff: 09/27/2017

---
# <a name="sfctl-compose"></a>sfctl compose
Docker Compose アプリケーションを作成、削除、管理します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    create| Compose ファイルから Service Fabric アプリケーションを作成します。|
|    list  | Service Fabric クラスターで作成された Compose アプリケーションの一覧を取得します。|
|   remove| クラスターから既存の Service Fabric Compose アプリケーションを削除します。|
|   status| Service Fabric Compose アプリケーションの情報を取得します。|
|upgrade       | Service Fabric クラスターで、Compose デプロイのアップグレードを開始します。|
|    upgrade-status| この Service Fabric Compose のデプロイで実行された最新のアップグレードの詳細を取得します。|


## <a name="sfctl-compose-create"></a>sfctl compose create
Service Fabric Compose のデプロイを作成します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --file-path [必須]| ターゲット Docker Compose ファイルへのパス。|
 |   --name      [必須]| デプロイの ID。|
|    --encrypted-pass      | コンテナー レジストリ パスワードの入力を求めるのではなく、既に暗号化されているパスフレーズを使用します。|
|    --has-pass            | コンテナー レジストリへのパスワードを要求します。|
|    --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|
 |   --user                | コンテナー レジストリに接続するユーザー名。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h               | このヘルプ メッセージを表示して終了します。|
| --output -o             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-compose-list"></a>sfctl compose list
Service Fabric クラスターで作成された Compose デプロイの一覧を取得します。

Service Fabric クラスターで作成されたまたは作成中の Compose デプロイの状態を取得します。 応答には、名前、状態、および Compose デプロイに関するその他の詳細が含まれています。 デプロイが 1 ページに収まらない場合、1 ページの結果と継続トークンが返されます。これは次のページを取得するために使用できます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --continuation-token| 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。      この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。|
| --max-results    | ページング クエリの一部として返される結果の最大数。      このパラメーターは、返される結果の数に上限を定義します。      返された結果が、構成で定義したメッセージの最大サイズの制限に収まらない場合は、指定した最大結果数よりも少なくなる場合があります。 このパラメーターがゼロまたは指定されていない場合、ページング クエリには、応答メッセージに収まるできるだけ多くの結果が含まれます。|
| --timeout -t     | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug          | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h        | このヘルプ メッセージを表示して終了します。|
| --output -o      | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query          | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose        | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-compose-remove"></a>sfctl compose remove
クラスターから既存の Service Fabric Compose デプロイを削除します。

既存の Service Fabric Compose デプロイを削除します。 

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --deployment-name [必須]| デプロイの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。|
| --timeout -t            | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h               | このヘルプ メッセージを表示して終了します。|
| --output -o             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-compose-status"></a>sfctl compose status
Service Fabric Compose デプロイの情報を取得します。

Service Fabric クラスターで作成された、または作成中の、パラメーターとして指定されているものと同じ名前の Compose デプロイの状態を返します。 応答には、名前、状態、およびアプリケーションに関するその他の詳細が含まれています。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --deployment-name [必須]| デプロイの ID。 |
| --timeout -t            | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h               | このヘルプ メッセージを表示して終了します。|
| --output -o             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Service Fabric クラスターで、Compose デプロイのアップグレードを開始します。

指定されたアップグレード パラメーターを検証し、デプロイのアップグレードを開始します。

### <a name="arguments"></a>引数
|引数|Description|
| --- | --- |
|    --file-path        [必須]| ターゲット Docker Compose ファイルへのパス。|
|    --name             [必須]| デプロイの ID。|
|    --default-svc-type-health-map| サービスの正常性を評価するために使用する正常性ポリシーについて説明している JSON でエンコードされたディクショナリ。|
|    --encrypted-pass             | コンテナー レジストリ パスワードの入力を求めるのではなく、既に暗号化されているパスフレーズを使用します。|
 |   --failure-action             | 使用可能な値には、'Invalid'、'Rollback'、'Manual' が含まれます。|
|    --force-restart              | 強制的に再起動します。|
 |   --has-pass                   | コンテナー レジストリへのパスワードを要求します。|
|    --health-check-retry         | 正常性チェックの再試行タイムアウト (ミリ秒単位)。|
|    --health-check-stable        | 正常性チェックの安定期間 (ミリ秒単位)。|
|    --health-check-wait          | 正常性チェックの待機期間 (ミリ秒単位)。|
|    --replica-set-check          | アップグレードするレプリカ セット チェックのタイムアウト (秒単位)。|
|    --svc-type-health-map        | さまざまなサービスの種類の正常性を評価するために使用される正常性ポリシーを記述するオブジェクトの JSON でエンコードされた一覧。|
|    --timeout -t                 | サーバー タイムアウト (秒)。  既定値は 60 です。|
|    --unhealthy-app              | エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。        たとえば、アプリケーションの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるアプリケーションの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なアプリケーションがある場合は、正常性は Warning として評価されます。 このパーセンテージは、異常なアプリケーションの数をクラスター内のアプリケーション インスタンスの総数で割ることによって計算されます。|
|    --upgrade-domain-timeout     | アップグレード ドメインのタイムアウト (ミリ秒単位)。|
|    --upgrade-kind               | 既定値は Rolling です。|
|    --upgrade-mode               | 使用可能な値には、'Invalid'、'UnmonitoredAuto'、'UnmonitoredManual'、'Monitored' が含まれます。  既定値は UnmonitoredAuto です。|
|    --upgrade-timeout            | アップグレードのタイムアウト (ミリ秒単位)。|
|    --user                       | コンテナー レジストリに接続するユーザー名。|
|    --warning-as-error           | Warnings は Error と同じ重大度で扱われます。|

### <a name="global-arguments"></a>グローバル引数
 |引数|Description|
| --- | --- |
|   --debug                      | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
|    --help -h                    | このヘルプ メッセージを表示して終了します。|
 |   --output -o                  | 出力形式。  使用できる値は、json、jsonc、table、tsv です。
                                   既定値は json です。|
 |   --query                      | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
 |   --verbose                    | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。
