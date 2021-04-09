---
title: Azure Service Bus に関する Resource Manager の例外 | Microsoft Docs
description: Azure Resource Manager によって表示される Service Bus の例外と推奨されるアクションの一覧。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a0b0338da0f002c7b667748ffd2bf5a40c91c580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85336975"
---
# <a name="service-bus-resource-manager-exceptions"></a>Service Bus に関する Resource Manager での例外

この記事では、Azure Resource Manager を使用して、テンプレートまたは直接呼び出しにより Azure Service Bus とやり取りするときに生成される例外を示します。

> [!IMPORTANT]
> このドキュメントは頻繁に更新されます。 更新プログラムがないかどうか確認してください。

次に示すのは、Azure Resource Manager によって表示されるさまざまな例外/エラーです。

## <a name="error-bad-request"></a>エラー: 正しくない要求

"正しくない要求" は、Resource Manager によって受信された要求の検証が失敗したことを意味します。

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  The property *'property name'* cannot be set when creating a Queue because the namespace *'namespace name'* is using the 'Basic' Tier. (名前空間 '<名前空間名>' では 'Basic' レベルが使用されているため、キューの作成時にプロパティ '<プロパティ名>' を設定することはできません。)  This operation is only supported in 'Standard' or 'Premium' tier. (この操作は、'Standard' または 'Premium' レベルでのみサポートされています。) | Azure Service Bus の Basic レベルでは、以下のプロパティを設定または更新することはできません。 <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> トピック </li> </ul> | この機能を使用するには、Basic レベルから Standard レベルまたは Premium レベルにアップグレードすることを検討してください。 |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  The value for the 'requiresDuplicateDetection' property of an existing Queue(or Topic) cannot be changed. (既存のキュー (またはトピック) の 'requiresDuplicateDetection' プロパティの値を変更することはできません。) | 重複の検出は、エンティティの作成時に有効/無効にする必要があります。 作成後に、重複検出の構成パラメーターを変更することはできません。 | 以前に作成したキュー/トピックで重複の検出を有効にするには、重複検出を使用して新しいキュー/トピックを作成し、元のキューから新しいキュー/トピックに転送します。 |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  The specified value 16384 is invalid. (指定された値 16384 は無効です。)  The property 'MaxSizeInMegabytes', must be one of the following values: 1024;2048;3072;4096;5120. (プロパティ 'MaxSizeInMegabytes' は、次の値のいずれかである必要があります: 1024、2048、3072、4096、5120。) | MaxSizeInMegabytes の値が無効です。 | MaxSizeInMegabytes が次のいずれかであることを確認します - 1024、2048、3072、4096、5120。 |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  Partitioning cannot be changed for Queue/Topic. (キュー/トピックのパーティション分割は変更できません。) | エンティティのパーティション分割は変更できません。 | 新しいエンティティ (キューまたはトピック) を作成し、パーティションを有効にします。 | 
| 正しくない要求 | なし | The namespace *'namespace name'* does not exist. (名前空間 '<名前空間名>' は存在しません。) | 名前空間が Azure サブスクリプション内に存在しません。 | このエラーを解決するには、以下を試してください <ul> <li> Azure サブスクリプションが正しいことを確認します。 </li> <li> 名前空間が存在することを確認します。 </li> <li> 名前空間の名前が正しいことを確認します (スペルミスまたは null 文字列がないこと)。 </li> </ul> | 
| 正しくない要求 | 40400 | SubCode=40400. (サブコード = 40400。)  The auto forwarding destination entity does not exist. (自動転送先エンティティが存在しません。) | 自動転送先エンティティの宛先が存在しません。 | 転送先エンティティ (キューまたはトピック) は、ソースが作成される前に存在している必要があります。 転送先エンティティの作成後に再試行してください。 |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  The supplied lock time exceeds the allowed maximum of '5' minutes. (指定されたロック時間が、許可されている最大値の '5' 分を超えています。) | メッセージをロックできる時間は、1 分 (最小) から 5 分 (最大) までの範囲で指定する必要があります。 | 指定したロック時間が 1 分から 5 分の範囲内であることを確認します。 |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  Both DelayedPersistence and RequiresDuplicateDetection property cannot be enabled together. (DelayedPersistence と RequiresDuplicateDetection プロパティの両方を同時に有効にすることはできません。) | 重複検出が有効になっているエンティティは永続的である必要があるため、永続化を遅延させることはできません。 | 詳細については「[重複検出](duplicate-detection.md)」を参照してください |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  The value for RequiresSession property of an existing Queue cannot be changed. (既存のキューの RequiresSession プロパティの値を変更することはできません。) | セッションのサポートは、エンティティの作成時に有効にする必要があります。 作成した後で、既存のエンティティ (キューまたはサブスクリプション) のセッションを有効または無効にすることはできません。 | 既存のものを削除し、"RequiresSession" プロパティを有効にして新しいキュー (またはサブスクリプション) を再作成します。 |
| 正しくない要求 | 40000 | SubCode=40000. (サブコード = 40000。)  'URI_PATH' contains character(s) that is not allowed by Service Bus. ('URI_PATH' に、Service Bus で許可されない文字が含まれます。)  エンティティ セグメントに使用できるのは、アルファベット、数字、ピリオド (.)、ハイフン (-)、アンダースコア (_) だけです。 | エンティティ セグメントに使用できるのは、アルファベット、数字、ピリオド (.)、ハイフン (-)、アンダースコア (_) だけです。 他の文字を使用すると、要求は失敗します。 | URI パスに無効な文字がないことを確認します。 |


## <a name="error-code-429"></a>エラー コード:429

HTTP の場合と同様に、"エラー コード 429" は "要求が多すぎる" ことを示します。 特定のリソース (名前空間) が、そのリソースに対する要求が多すぎるため (または操作が競合しているため)、調整されていることを意味します。

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | SubCode=50004. (サブコード = 50004。)  The request was terminated because the namespace *your namespace* is being throttled. (名前空間 <名前空間> が調整されているため、要求は終了されました。) | このエラー状態は、受信要求の数がリソースの制限を超えた場合に発生します。 | 数秒待ってから、もう一度やり直してください。 <br/> <br/> [クォータ](service-bus-quotas.md)および [Azure Resource Manager の要求制限](../azure-resource-manager/management/request-limits-and-throttling.md)の詳細を確認してください。|
| 429 | 40901 | SubCode=40901. (サブコード = 40901。)  Another conflicting operation is in progress. (別の競合する操作が実行中です。) | 同じリソース/エンティティで、競合する別の操作が進行中です | 現在の処理中の操作が完了するのを待ってから、再試行してください。 |
| 429 | 40900 | SubCode=40900. (サブコード = 40900。) 競合しています。 リソースの現在の状態では許可されていない操作を要求しています。 | この状態は、同じエンティティ (キュー、トピック、サブスクリプション、またはルール) に対して複数の要求により操作が同時に実行された場合に発生する可能性があります。 | 数秒待ってから、もう一度やり直してください |
| 429 | 40901 | Request on entity *'entity name'* conflicted with another request (エンティティ '<エンティティ名>' の要求が別の要求と競合しました) | 同じリソース/エンティティで、競合する別の操作が進行中です | 前の操作が完了するのを待ってから、再試行してください |
| 429 | 40901 | Another update request is in progress for the entity *'entity name'*. (エンティティ '<エンティティ名>' の別の更新要求が進行中です。) | 同じリソース/エンティティで、競合する別の操作が進行中です | 前の操作が完了するのを待ってから、再試行してください |
| 429 | なし | Resource Conflict Occurred. (リソースの競合が発生しました。)  Another conflicting operation may be in progress. (別の競合する操作が実行中の可能性があります。) If this is a retry for failed operation, background cleanup is still pending. (失敗した操作の再試行の場合は、バックグラウンドのクリーンアップがまだ保留中になっています。) 後で再試行してください。 | この状態は、同じエンティティに対して保留中の操作がある場合に発生する可能性があります。 | 前の操作が完了するのを待ってから、再試行してください。 |


## <a name="error-code-not-found"></a>エラー コード: 見つかりません

このクラスのエラーは、リソースが見つからなかったことを示します。

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 見つかりません | なし | Entity *'entity name'* was not found. (エンティティ '<エンティティ名>' が見つかりませんでした。) | 操作の対象のエンティティが見つかりませんでした。 | エンティティが存在するかどうかを確認してから、操作を再試行してください。 |
| 見つかりません | なし | Not Found. (見つかりませんでした。) The Operation doesn't exist. (操作が存在しません。) | 実行しようとしている操作は存在しません。 | 操作を確認してから、やり直してください。 |
| 見つかりません | なし | The incoming request is not recognized as a namespace policy put request. (受信した要求は名前空間ポリシーの PUT 要求として認識されません。) | 受信した要求の本文が null であるため、PUT 要求として実行できません。 | 要求の本文が null でないことを確認してください。 | 
| 見つかりません | なし | The messaging entity *'entity name'* could not be found. (メッセージング エンティティ '< エンティティ名>' が見つかりませんでした。) | 操作を実行しようとしているエンティティが見つかりませんでした。 | エンティティが存在するかどうかを確認し、操作を再試行してください。 |

## <a name="error-code-internal-server-error"></a>エラー コード: 内部サーバー エラー

このクラスのエラーは、内部サーバー エラーがあったことを示しています

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 内部サーバー エラー | 50000 | サブコード=50000。 内部サーバー エラー| さまざまな理由で発生する可能性があります。 症状の一部は以下のようなものです。 <ul> <li> クライアントの要求/本文が破損していてエラーに至る。 </li> <li> サービスでの処理の問題のため、クライアント要求がタイムアウトする。 </li> </ul> | これを解決するには、次のようにします <ul> <li> 要求のパラメーターが null でないことや、形式が正しくないことを確認します。 </li> <li> 要求をやり直してください。 </li> </ul> |

## <a name="error-code-unauthorized"></a>エラー コード: 権限がありません

このクラスのエラーは、コマンドを実行する権限がないことを示しています。

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 権限がありません | なし | Invalid operation on the Secondary namespace. (セカンダリ名前空間での操作が無効です。) Secondary namespace is read-only. (セカンダリ名前空間は読み取り専用です。) | 操作はセカンダリ名前空間に対して実行されました。この名前空間は読み取り専用の名前空間として設定されています。 | プライマリ名前空間に対してコマンドを再試行してください。 [セカンダリ名前空間](service-bus-geo-dr.md)についての詳細を確認してください |
| 権限がありません | なし | MissingToken: Authorization ヘッダーが見つかりません。 | このエラーは、承認に null または正しくない値が含まれているときに発生します。 | Authorization ヘッダーに記載されているトークン値が正しく、null でないことを確認します。 |