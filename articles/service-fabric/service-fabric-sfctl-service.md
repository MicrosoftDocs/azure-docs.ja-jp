---
title: Azure Service Fabric CLI- sfctl service | Microsoft Docs
description: "Service Fabric CLI sfctl service のコマンドについて説明します。"
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
ms.openlocfilehash: 66649bb6ae317eb227dcdf45aa084905967c117f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-service"></a>sfctl service
サービス、サービスの種類、およびサービス パッケージを作成、削除、管理します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    app-name       | サービスの Service Fabric アプリケーションの名前を取得します。|
|    code-package-list | Service Fabric ノードにデプロイされているコード パッケージの一覧を取得します。|
|    create         | 説明から指定した Service Fabric サービスを作成します。|
|    削除         | 既存の Service Fabric サービスを削除します。|
|    deployed-type  | Service Fabric クラスター内のノードにデプロイされたアプリケーションの指定されたサービスの種類に関する情報を取得します。|
|    deployed-type-list| Service Fabric クラスター内のノードにデプロイされたアプリケーションからサービスの種類に関する情報を含む一覧を取得します。|
|    description    | 既存の Service Fabric サービスの説明を取得します。|
|    health         | 指定した Service Fabric サービスの正常性を取得します。|
|    info           | Service Fabric アプリケーションに属する特定のサービスに関する情報を取得します。|
|    list           | アプリケーション ID で指定されたアプリケーションに属するすべてのサービスに関する情報を取得します。|
|    manifest       | サービスの種類を記述するマニフェストを取得します。|
|    package-deploy | 指定されたサービス マニフェストに関連付けられているパッケージを指定したノードのイメージ キャッシュにダウンロードします。|
|    package-health | Service Fabric ノードとアプリケーションにデプロイされた特定のアプリケーションのサービス パッケージの正常性に関する情報を取得します。|
|    package-info   | 指定された名前と完全に一致する Service Fabric ノードにデプロイされているサービス パッケージの一覧を取得します。|
|    package-list   | Service Fabric ノードにデプロイされているサービス パッケージの一覧を取得します。|
|    recover        | クォーラム損失で現在スタックしている指定されたサービスの復旧を試みる必要があることを Service Fabric クラスターに示します。|
|    report-health  | Service Fabric サービスの正常性レポートを送信します。|
|    resolve        | Service Fabric パーティションを解決します。|
|    type-list      | Service Fabric クラスターでプロビジョニングされたアプリケーションの種類でサポートされているサービスの種類に関する情報を含む一覧を取得します。|
|    update         | 特定の更新プログラムの説明を使用して、指定したサービスを更新します。|


## <a name="sfctl-service-create"></a>sfctl service create
説明から指定した Service Fabric サービスを作成します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --app-id       [必須]| 親アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な ID です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、アプリケーション名が 'fabric://myapp/app1' の場合、6.0 以降ではアプリケーション ID は 'myapp~app1' になり、前のバージョンでは 'myapp/app1' になります。|
| --name         [必須]| サービスの名前。 これは、アプリケーション ID の子である必要があります。           これは、`fabric:` URI を含む完全な名前です。 たとえば、サービス `fabric:/A/B` はアプリケーション `fabric:/A` の子です。|
| --service-type [必須]| サービスの種類の名前。|
| --activation-mode     | サービス パッケージのアクティブ化モード。|
| --constraints         | 文字列としての配置の制約。 配置の制約はノード プロパティ上のブール式で、サービスの要件に基づいて特定のノードにサービスを制限することができます。 たとえば、NodeType が blue のノードでサービスを配置するには、"NodeColor == blue" を指定します。|
| --correlated-service  | 関連付けるターゲット サービスの名前。|
| --correlation         | 配置アフィニティを使用して既存のサービスにサービスを関連付けます。|
| --dns-name            | 作成するサービスの DNS 名。 この設定のために Service Fabric DNS システム サービスを有効にする必要があります。|
| --instance-count      | インスタンス数。 これはステートレス サービスにのみ適用されます。|
| --int-scheme          | サービスを符号なし整数の範囲にわたって均一にパーティション分割する必要があることを示します。|
| --int-scheme-count    | 整数キーの範囲内 (均一な整数パーティション構成) で作成するパーティションの数。|
| --int-scheme-high     | 均一な整数パーティション構成を使用している場合、キー整数の範囲の最後。|
| --int-scheme-low      | 均一な整数パーティション構成を使用している場合、キー整数の範囲の開始。|
| --load-metrics        | サービスをノード間で負荷分散するときに使用されるメトリックの JSON でエンコードされた一覧。|
| --min-replica-set-size| レプリカ セットの最小サイズ (数)。 これはステートフル サービスにのみ適用されます。|
| --move-cost           | サービスの移動コストを指定します。 使用可能な値は、 'Zero'、'Low'、'Medium'、'High' です。|
| --named-scheme        | サービスが複数の名前付きパーティションを持つ必要があることを示します。|
| --named-scheme-list   | 名前付きパーティション構成を使用している場合、サービスを分割するパーティションの名前の JSON でエンコードされた一覧。|
| --no-persisted-state  | これが true の場合、サービスにローカル ディスクに格納されている永続的な状態がないか、メモリに状態のみを格納していることを示します。|
| --placement-policy-list  | サービスの配置ポリシーと関連するすべてのドメイン名の JSON でエンコードされた一覧。 ポリシーには `NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution` の 1 つ以上を指定できます。|
| --quorum-loss-wait    | パーティションがクォーラム損失の状態であることを許可する最大期間 (秒)。 これはステートフル サービスにのみ適用されます。|
| --replica-restart-wait| レプリカがダウンし、新しいレプリカが作成されるまでの期間 (秒)。 これはステートフル サービスにのみ適用されます。|
| --singleton-scheme    | サービスに 1 つのパーティションを持つ必要があるか、サービスを非分割にする必要があるかを示します。|
| --stand-by-replica-keep  | スタンバイ レプリカが削除されるまでに保持される最大期間 (秒)。 これはステートフル サービスにのみ適用されます。|
| --stateful            | サービスがステートフル サービスであることを示します。|
| --stateless           | サービスがステートレス サービスであることを示します。|
| --target-replica-set-size| ターゲット レプリカ セットのサイズ (数)。 これはステートフル サービスにのみ適用されます。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-delete"></a>sfctl service delete
既存の Service Fabric サービスを削除します。

既存の Service Fabric サービスを削除します。 サービスを削除するには、その前にサービスが作成されている必要があります。 既定では Service Fabric は、安全な方法でサービスのレプリカを閉じてからサービスを削除しようとします。 ただし、サービスにレプリカを正常に終了できない問題がある場合は、削除操作に時間がかかったり、スタックしたりする場合があります。 正常に閉じるシーケンスをスキップし、サービスを強制的に削除するには、省略可能な ForceRemove フラグを使用します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --service-id [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならサービス ID は "myapp~app1~svc1" になり、前のバージョンでは "myapp/app1/svc1" になります。|
| --force-remove      | 正常なシャットダウン シーケンスを経由せずに、Service Fabric アプリケーションまたはサービスを強制的に削除します。 このパラメーターを使用して、レプリカの正常な終了を妨げるサービス コードでの問題により、削除がタイムアウトしたアプリケーションまたはサービスを強制的に削除することができます。|
| --timeout -t        | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug             | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h           | このヘルプ メッセージを表示して終了します。|
| --output -o         | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query             | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose           | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-description"></a>sfctl service description
既存の Service Fabric サービスの説明を取得します。

既存の Service Fabric サービスの説明を取得します。 サービスの説明を取得するには、サービスが作成されている必要があります。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --service-id [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならサービス ID は "myapp~app1~svc1" になり、前のバージョンでは "myapp/app1/svc1" になります。|
| --timeout -t        | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug             | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h           | このヘルプ メッセージを表示して終了します。|
| --output -o         | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query             | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose           | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-health"></a>sfctl service health
指定した Service Fabric サービスの正常性を取得します。

指定したサービスの正常性情報を取得します。 正常性状態に基づいてサービスで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 返されるパーティションのコレクションをフィルター処理するには、PartitionsHealthStateFilter を使用します。 正常性ストアに存在しないサービスを指定した場合、このコマンドレットがエラーを返します。 が必要です。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --service-id          [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならサービス ID は "myapp~app1~svc1" になり、前のバージョンでは "myapp/app1/svc1" になります。|
| --events-health-state-filter | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルターできます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。 - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。|
|--exclude-health-statistics     | 正常性の統計情報がクエリ結果の一部として返されるかどうかを示します。 既定では false です。 統計情報は正常性の状態が Ok、Warning、および Error の子エンティティの数を示します。|
| --partitions-health-state-filter| 正常性状態に基づくサービス正常性クエリの結果で返される、パーティションの正常性状態のオブジェクトのフィルター処理ができます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するパーティションのみが返されます。 すべてのパーティションが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のパーティションの正常性の状態が返されます。 - Default - 既定値。 任意の HealthState と一致します。                  値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。 - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。|
| --timeout -t                 | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                      | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                    | このヘルプ メッセージを表示して終了します。|
| --output -o                  | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                  既定値は json です。|
| --query                      | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                    | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-info"></a>sfctl service info
Service Fabric アプリケーションに属する特定のサービスに関する情報を取得します。

指定した Service Fabric アプリケーションに属する指定したサービスに関する情報を返します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-id [必須]| アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、アプリケーション名が 'fabric://myapp/app1' の場合、6.0 以降ではアプリケーション ID は 'myapp~app1' になり、前のバージョンでは 'myapp/app1' になります。|
| --service-id     [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならサービス ID は "myapp~app1~svc1" になり、前のバージョンでは "myapp/app1/svc1" になります。|
| --timeout -t            | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h               | このヘルプ メッセージを表示して終了します。|
| --output -o             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-list"></a>sfctl service list
アプリケーション ID で指定されたアプリケーションに属するすべてのサービスに関する情報を取得します。

アプリケーション ID で指定されたアプリケーションに属するすべてのサービスに関する情報を返します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-id [必須]| アプリケーションの ID。 これは通常、'fabric:' URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、アプリケーション名が 'fabric://myapp/app1' の場合、6.0 以降ではアプリケーション ID は 'myapp~app1' になり、前のバージョンでは 'myapp/app1' になります。|
| --continuation-token    | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらないときに、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。|
| --service-type-name     | クエリを実行するサービスをフィルター処理するために使用されるサービスの種類名。|
| --timeout -t            | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                 | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h               | このヘルプ メッセージを表示して終了します。|
| --output -o             | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query                 | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose               | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-manifest"></a>sfctl service manifest
サービスの種類を記述するマニフェストを取得します。

サービスの種類を記述するマニフェストを取得します。 応答には、サービス マニフェスト XML が文字列として含まれています。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --application-type-name    [必須]| アプリケーションの種類名。|
| --application-type-version [必須]| アプリケーションの種類のバージョン。|
| --service-manifest-name    [必須]| Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストの名前。|
| --timeout -t                      | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                           | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                         | このヘルプ メッセージを表示して終了します。|
| --output -o                       | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                       既定値は json です。|
| --query                           | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                         | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-recover"></a>sfctl service recover
クォーラム損失で現在スタックしている指定されたサービスの復旧を試みる必要があることを Service Fabric クラスターに示します。

クォーラム損失で現在スタックしている指定されたサービスの復旧を試みる必要があることを Service Fabric クラスターに示します。 この操作は、停止しているレプリカを復旧できない場合にのみ実行してください。 この API を不適切に使用すると、データ損失が発生する可能性があります。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --service-id [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は '~' 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならサービス ID は "myapp~app1~svc1" になり、前のバージョンでは "myapp/app1/svc1" になります。|
| --timeout -t        | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug             | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h           | このヘルプ メッセージを表示して終了します。|
| --output -o         | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query             | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose           | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Service Fabric パーティションを解決します。

サービス レプリカのエンドポイントを取得するため、Service Fabric サービス パーティションを解決します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --service-id [必須]| サービスの ID。 これは通常、'fabric:' URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "~" 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならサービス ID は "myapp~app1~svc1" になり、前のバージョンでは "myapp/app1/svc1" になります。|
| --partition-key-type| パーティションのキーの種類。 サービスのパーティション構成が Int64Range または Named の場合、このパラメーターが必要です。 指定できる値は次のとおりです。 - None (1) - PartitionKeyValue パラメーターが指定されていないことを示します。 これは、Singleton のパーティション構成のパーティションに有効です。 これが既定値です。 値は 1 です。 Int64Range (2) - PartitionKeyValue パラメーターが int64 パーティション キーであることを示します。 これは Int64Range のパーティション構成のパーティションに有効です。 値は 2 です。 - Named (3) - PartitionKeyValue パラメーターがパーティションの名前であることを示します。 これは、Named のパーティション構成のパーティションに有効です。 値は 3 です。|
| --partition-key-value  | パーティション キー。 これはサービスのパーティション構成が Int64Range または Named の場合に必要です。|
| --previous-rsp-version | 以前に受信した応答のバージョン フィールドの値。 これは、以前に取得した結果が古くなっていることをユーザーが知っている場合に必要です。|
| --timeout -t        | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug             | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h           | このヘルプ メッセージを表示して終了します。|
| --output -o         | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query             | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose           | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-service-update"></a>sfctl service update
特定の更新プログラムの説明を使用して、指定したサービスを更新します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --service-id   [必須]| 更新対象のサービス。 これは通常、'fabric:' URI スキームのないサービスの完全な ID です。 バージョン 6.0 以降では、階層名は "~" 文字で区切られます。 たとえば、サービス名が "fabric://myapp/app1/svc1" の場合、6.0 以上ならサービス ID は "myapp~app1~svc1" になり、前のバージョンでは "myapp/app1/svc1" になります。|
| --constraints         | 文字列としての配置の制約。 配置の制約はノード プロパティ上のブール式で、サービスの要件に基づいて特定のノードにサービスを制限することができます。 たとえば、NodeType が blue のノードでサービスを配置するには、"NodeColor == blue" を指定します。|
| --correlated-service  | 関連付けるターゲット サービスの名前。|
| --correlation         | 配置アフィニティを使用して既存のサービスにサービスを関連付けます。|
| --instance-count      | インスタンス数。 これはステートレス サービスにのみ適用されます。|
| --load-metrics        | ノード間で負荷分散するときに使用されるメトリックの JSON でエンコードされた一覧。|
| --min-replica-set-size| レプリカ セットの最小サイズ (数)。 これはステートフル サービスにのみ適用されます。|
| --move-cost           | サービスの移動コストを指定します。 使用可能な値は、 'Zero'、'Low'、'Medium'、'High' です。|
| --placement-policy-list  | サービスの配置ポリシーと関連するすべてのドメイン名の JSON でエンコードされた一覧。 ポリシーには `NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution` の 1 つ以上を指定できます。|
| --quorum-loss-wait    | パーティションがクォーラム損失の状態であることを許可する最大期間 (秒)。 これはステートフル サービスにのみ適用されます。|
| --replica-restart-wait| レプリカがダウンし、新しいレプリカが作成されるまでの期間 (秒)。 これはステートフル サービスにのみ適用されます。|
| --stand-by-replica-keep  | スタンバイ レプリカが削除されるまでに保持される最大期間 (秒)。 これはステートフル サービスにのみ適用されます。|
| --stateful            | ターゲット サービスがステートフル サービスであることを示します。|
| --stateless           | ターゲット サービスがステートレス サービスであることを示します。|
| --target-replica-set-size| ターゲット レプリカ セットのサイズ (数)。 これはステートフル サービスにのみ適用されます。|
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