---
title: EdgeAgent および EdgeHub の推奨されるプロパティのリファレンス - Azure IoT Edge | Microsoft Docs
description: edgeAgent および edgeHub モジュール ツインの特定のプロパティとその値を確認します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e8a8170023c8f529894522e27a4c6231325089af
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190994"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge エージェントと IoT Edge ハブのモジュール ツインのプロパティ

IoT Edge エージェントと IoT Edge ハブは、IoT Edge ランタイムを構成する 2 つのモジュールです。 各モジュールが実行する役割について詳しくは、「[Azure IoT Edge ランタイムとそのアーキテクチャについて](iot-edge-runtime.md)」をご覧ください。 

この記事では、ランタイム モジュール ツインの必要なプロパティと報告されるプロパティを示します。 IoT Edge デバイスにモジュールをデプロイする方法の詳細については、[IoT Edge にモジュールをデプロイしてルートを確立する方法](module-composition.md)に関する記事をご覧ください。

モジュール ツインには以下が含まれます。 

* **必要なプロパティ**。 ソリューション バックエンドにより必要なプロパティを設定でき、モジュールによりそれらを読み取れます。 モジュールでは、必要なプロパティに対する変更を知らせる通知を受け取ることもできます。 モジュールの構成や状態を同期するために、必要なプロパティを報告されるプロパティと共に使用します。

* **報告されるプロパティ**。 モジュールにより、報告されたプロパティを設定でき、ソリューション バックエンドによりそれらを読み取って、クエリを実行できます。 モジュールの構成や状態を同期するために、報告されたプロパティが必要なプロパティと共に使用されます。 

## <a name="edgeagent-desired-properties"></a>edgeAgent の必要なプロパティ

IoT Edge エージェントのモジュール ツインは `$edgeAgent` と呼ばれ、デバイス上で実行されている IoT Edge エージェントと IoT Hub の間の通信を調整します。 必要なプロパティは、単一デバイスまたは大規模デプロイの一部として特定のデバイスにデプロイ マニフェストを適用するときに設定されます。 

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" である必要がある | はい |
| runtime.type | "docker" である必要がある | はい |
| runtime.settings.minDockerVersion | このデプロイ マニフェストに必要な最小の Docker バージョンに設定される | はい |
| runtime.settings.loggingOptions | IoT Edge エージェント コンテナーのログ オプションを含む文字列化された JSON。 [Docker のログ オプション](https://docs.docker.com/engine/admin/logging/overview/) | いいえ |
| runtime.settings.registryCredentials<br>.{registryId}.username | コンテナー レジストリのユーザー名です。 Azure Container Registry では、ユーザー名は通常、レジストリ名です。<br><br> 公開されていないモジュール イメージでは、レジストリの資格情報が必要です。 | いいえ |
| runtime.settings.registryCredentials<br>.{registryId}.password | コンテナー レジストリのパスワード。 | いいえ |
| runtime.settings.registryCredentials<br>.{registryId}.address | コンテナー レジストリのアドレス。 Azure Container Registry の場合、アドレスは通常 *{registry name}.azurecr.io* です。 | いいえ |  
| systemModules.edgeAgent.type | "docker" である必要がある | はい |
| systemModules.edgeAgent.settings.image | IoT Edge エージェントのイメージの URI。 現在、IoT Edge エージェントは自身を更新できません。 | はい |
| systemModules.edgeAgent.settings<br>.createOptions | IoT Edge エージェント コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | いいえ |
| systemModules.edgeAgent.configuration.id | このモジュールをデプロイしたデプロイの ID。 | このプロパティは、マニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |
| systemModules.edgeHub.type | "docker" である必要がある | はい |
| systemModules.edgeHub.status | "running" である必要がある | はい |
| systemModules.edgeHub.restartPolicy | "always" である必要がある | はい |
| systemModules.edgeHub.settings.image | IoT Edge ハブのイメージの URI。 | はい |
| systemModules.edgeHub.settings<br>.createOptions | IoT Edge ハブ コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | いいえ |
| systemModules.edgeHub.configuration.id | このモジュールをデプロイしたデプロイの ID。 | このプロパティは、マニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |
| modules.{moduleId}.version | このモジュールのバージョンを表すユーザー定義の文字列。 | はい |
| modules.{moduleId}.type | "docker" である必要がある | はい |
| modules.{moduleId}.status | {"running" \| "stopped"} | はい |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | はい |
| modules.{moduleId}.settings.image | モジュール イメージへの URI。 | はい |
| modules.{moduleId}.settings.createOptions | モジュール コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | いいえ |
| modules.{moduleId}.configuration.id | このモジュールをデプロイしたデプロイの ID。 | このプロパティは、マニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |

## <a name="edgeagent-reported-properties"></a>edgeAgent の報告されるプロパティ

IoT Edge エージェントの報告されるプロパティには、次の 3 つの主な情報が含まれます。

1. 最後に表示された必要なプロパティのアプリケーションの状態
2. IoT Edge エージェントによって報告された、現在デバイス上で実行されているモジュールの状態
3. 現在デバイス上で実行されている必要なプロパティのコピー。

この最後の情報、つまり現在の必要なプロパティのコピーは、デバイスに最新の必要なプロパティが適用されているか、または以前のデプロイ マニフェストが引き続き実行されているかを判断するのに役立ちます。

> [!NOTE]
> IoT Edge エージェントの報告されるプロパティは、デプロイの状態を大規模に調査するために [IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-query-language.md)でクエリできるため有効です。 状態のために IoT Edge エージェント プロパティを使用する方法の詳細については、[1 台または多数のデバイスを対象とした IoT Edge デプロイ](module-deployment-monitoring.md)に関する記事を参照してください。

次の表には、必要なプロパティからコピーされる情報は含まれません。

| プロパティ | 説明 |
| -------- | ----------- |
| lastDesiredVersion | この整数は、IoT Edge エージェントによって処理された必要なプロパティの最後のバージョンを参照します。 |
| lastDesiredStatus.code | これは、IoT Edge エージェントによって表示された最後の必要なプロパティを参照する状態コードです。 使用できる値は以下の通りです。`200` 成功、`400` 無効な構成、`412` 無効なスキーマ バージョン、`417` 必要なプロパティが空、`500` 失敗 |
| lastDesiredStatus.description | 状態のテキストでの説明 |
| deviceHealth | `healthy` すべてのモジュールのランタイムの状態が `running` または `stopped` のどちらかである場合、`unhealthy` それ以外の場合 |
| configurationHealth.{deploymentId}.health | `healthy` デプロイ {deploymentId} によって設定されたすべてのモジュールのランタイムの状態が `running` または `stopped` のどちらかである場合、`unhealthy` それ以外の場合 |
| runtime.platform.OS | デバイス上で実行されている OS の報告 |
| runtime.platform.architecture | デバイス上の CPU のアーキテクチャの報告 |
| systemModules.edgeAgent.runtimeStatus | IoT Edge エージェントの報告された状態: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | IoT Edge エージェントの報告された状態のテキストでの説明。 |
| systemModules.edgeHub.runtimeStatus | IoT Edge ハブの状態: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 異常な場合の IoT Edge ハブの状態のテキストでの説明。 |
| systemModules.edgeHub.exitCode | コンテナーが終了した場合に、IoT Edge ハブ コンテナーによって報告された終了コード。 |
| systemModules.edgeHub.startTimeUtc | IoT Edge ハブが最後に起動された時間 |
| systemModules.edgeHub.lastExitTimeUtc | IoT Edge ハブが最後に終了された時間 |
| systemModules.edgeHub.lastRestartTimeUtc | IoT Edge ハブが最後に再起動された時間 |
| systemModules.edgeHub.restartCount | このモジュールが再起動ポリシーの一部として再起動された回数。 |
| modules.{moduleId}.runtimeStatus | モジュールの状態: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 異常な場合のモジュールの状態のテキストでの説明。 |
| modules.{moduleId}.exitCode | コンテナーが終了した場合に、モジュール コンテナーによって報告された終了コード。 |
| modules.{moduleId}.startTimeUtc | モジュールが最後に起動された時間 |
| modules.{moduleId}.lastExitTimeUtc | モジュールが最後に終了した時間 |
| modules.{moduleId}.lastRestartTimeUtc | モジュールが最後に再起動された時間 |
| modules.{moduleId}.restartCount | このモジュールが再起動ポリシーの一部として再起動された回数。 |

## <a name="edgehub-desired-properties"></a>edgeHub の必要なプロパティ

IoT Edge ハブのモジュール ツインは `$edgeHub` と呼ばれ、デバイス上で実行されている IoT Edge ハブと IoT Hub の間の通信を調整します。 必要なプロパティは、単一デバイスまたは大規模デプロイの一部として特定のデバイスにデプロイ マニフェストを適用するときに設定されます。 

| プロパティ | 説明 | デプロイ マニフェストに必要 |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" である必要がある | はい |
| routes.{routeName} | IoT Edge ハブのルートを表す文字列。 詳細については、「[ルートの宣言](module-composition.md#declare-routes)」を参照してください。 | `routes` 要素は存在できますが、空です。 |
| storeAndForwardConfiguration.timeToLiveSecs | ルーティング エンドポイント (IoT Hub またはローカル モジュール) から切断された場合に、IoT Edge ハブがメッセージを保持する秒数。 値には正の整数を指定できます。 | はい |

## <a name="edgehub-reported-properties"></a>edgeHub の報告されるプロパティ

| プロパティ | 説明 |
| -------- | ----------- |
| lastDesiredVersion | この整数は、IoT Edge ハブによって処理された必要なプロパティの最後のバージョンを参照します。 |
| lastDesiredStatus.code | IoT Edge ハブによって表示された最後の必要なプロパティを参照する状態コード。 使用できる値は以下の通りです。`200` 成功、`400` 無効な構成、`500` 失敗 |
| lastDesiredStatus.description | 状態を説明するテキスト。 |
| clients.{device or moduleId}.status | このデバイスまたはモジュールの接続状態。 可能性のある値 {"connected" \| "disconnected"}。 切断された状態になることができるのはモジュール ID だけです。 IoT Edge ハブに接続されるダウンストリーム デバイスは、接続されている場合にのみ表示されます。 |
| clients.{device or moduleId}.lastConnectTime | デバイスまたはモジュールが接続された最後の時間。 |
| clients.{device or moduleId}.lastDisconnectTime | デバイスまたはモジュールが切断された最後の時間。 |

## <a name="next-steps"></a>次の手順

これらのプロパティを使って配置マニフェストを作成する方法については、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。
