---
title: Azure IoT Central へのデバイス接続のトラブルシューティング | Microsoft Docs
description: デバイスからのデータが IoT Central で表示されない原因を解決します。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: d1a7c94152b611ea0dbea249156add617178d7ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673236"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>デバイスからのデータが Azure IoT Central で表示されない原因を解決する

このドキュメントは、デバイス開発者が、デバイスから IoT Central に送信されるデータがアプリケーションで表示されない原因を解明するのに役立ちます。

調査する主な領域としては、次の 2 つがあります。

- デバイス接続の問題
  - 認証の問題 (デバイスの資格情報が無効など)
  - ネットワーク接続の問題
  - デバイスが承認されていない、またはブロックされている
- デバイス ペイロードの形態の問題

このトラブルシューティング ガイドでは、デバイス接続の問題とデバイス ペイロードの形態の問題について重点的に説明します。

## <a name="device-connectivity-issues"></a>デバイス接続の問題

このセクションは、データが IoT Central に到達しているかどうかを確認するのに役立ちます。

`az cli` ツールと `azure-iot` 拡張機能をまだインストールしていない場合はインストールします。

`az cli` のインストール方法については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

`azure-iot` 拡張機能を[インストール](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation)するには、次のコマンドを実行します。

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> 拡張コマンドを初めて実行するときに、`uamqp` ライブラリをインストールするように求められる場合があります。

`azure-iot` 拡張機能をインストールしたら、デバイスを起動して、送信しているメッセージが、IoT Central に送信されているかどうかを確認します。

次のコマンドを使用して、IoT Central アプリケーションを含むサブスクリプションにサインインします。

```azurecli
az login
az set account --subscription <your-subscription-id>
```

デバイスから送信されているテレメトリを監視するには、次のコマンドを使用します。

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

デバイスが IoT Central に正常に接続されている場合、次のような出力が表示されます。

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

デバイスと IoT Central の間で交換されているプロパティの更新を監視するには、次のプレビュー コマンドを使用します。

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

デバイスからプロパティの更新が正常に送信されている場合、次のような出力が表示されます。

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

ターミナルにデータが表示される場合、データは IoT Central アプリケーションまで到達しています。

数分経ってもデータが表示されない場合、出力がスタックしている可能性があるので、キーボードの `Enter` キーまたは `return` キーを押します。

それでもターミナルにデータが表示されない場合は、デバイスでネットワーク接続の問題が発生しているか、データが IoT Central に正しく送信されていない可能性があります。

### <a name="check-the-provisioning-status-of-your-device"></a>デバイスのプロビジョニング状態を確認する

データがモニターに表示されていない場合は、次のコマンドを実行して、デバイスのプロビジョニング状態を確認します。

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

次の出力は、接続がブロックされているデバイスの例を示しています。

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| デバイスのプロビジョニング状態 | 説明 | 考えられる軽減策 |
| - | - | - |
| プロビジョニング済み | すぐに認識できる問題はありません。 | 該当なし |
| 登録済み | デバイスがまだ IoT Central に接続されていません。 | デバイス ログを調べて、接続の問題の有無を確認します。 |
| Blocked | デバイスは、IoT Central への接続をブロックされています。 | デバイスは、IoT Central アプリケーションへの接続をブロックされています。 IoT Central でデバイスのブロックを解除して、再試行します。 詳細については、[デバイスのブロック](concepts-get-connected.md#device-status-values)に関する記事を参照してください。 |
| 未承認 | デバイスは承認されていません。 | デバイスは、IoT Central アプリケーションへの接続を承認されていません。 IoT Central でデバイスを承認し、再試行します。 詳細については、[デバイスの承認](concepts-get-connected.md#device-registration)に関する記事を参照してください |
| 関連付けなし | デバイスは、デバイス テンプレートに関連付けられていません。 | IoT Central でデータの解析方法を認識できるように、デバイスをデバイス テンプレートに関連付けます。 |

デバイスの状態コードの詳細については、[こちら](concepts-get-connected.md#device-status-values)を参照してください。

### <a name="error-codes"></a>エラー コード

データが `monitor-events`に表示されない原因をまだ突き止められない場合は、次の手順として、デバイスによって報告されるエラー コードを調べます。

デバイスでデバッグ セッションを開始するか、デバイスからログを収集します。 デバイスによって報告されるエラー コードがあるかどうかを確認します。

次の表は、一般的なエラー コードと考えられる軽減措置を示しています。

認証フローに関連する問題が発生している場合:

| エラー コード | 説明 | 考えられる軽減策 |
| - | - | - |
| 400 | 要求本文が有効ではありません。 たとえば、要求を解釈できない場合や、オブジェクトを検証できない場合などです。 | 確実に構成証明フローの過程で正しい要求本文を送信するか、デバイス SDK を使用します。 |
| 401 | 認証トークンを検証できません。 たとえば、有効期限切れの場合や、要求の URI に適用されない場合などです。 また、このエラー コードは、TPM 構成証明フローの過程でデバイスに返されます。 | 確実に正しい資格情報がデバイスに含まれるようにします。 |
| 404 | デバイス プロビジョニング サービスのインスタンス、または登録などのリソースが存在しません。 | [カスタマー サポートにチケットを提出](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)してください。 |
| 412 | 要求の `ETag` が、RFC7232 に従って、既存のリソースの `ETag` と一致しません。 | [カスタマー サポートにチケットを提出](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)してください。 |
| 429 | サービスによって操作がスロットルされています。 サービスの具体的な制限については、「[IoT Hub Device Provisioning Service の制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)」を参照してください。 | メッセージの頻度を削減し、より多くのデバイス間で責任を分割します。 |
| 500 | 内部エラーが発生しました。 | [カスタマー サポートにチケットを提出](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)して、さらにサポートを受けられるかどうかを確認します。 |

## <a name="payload-shape-issues"></a>ペイロードの形態の問題

デバイスから IoT Central にデータが送信されていることを確認したら、次のステップとして、確実に有効な形式でデバイスからデータが送信されるようにします。

デバイス データが IoT Central で表示されない原因となる一般的な問題には、主に、次の 2 つのカテゴリがあります。

- デバイス テンプレートとデバイス データの不一致:
  - 名前の不一致 (入力ミスやケース マッチングの問題など)。
  - モデル化されていないプロパティ (スキーマがデバイス テンプレートで定義されていない場合)。
  - スキーマの不一致 (型はテンプレートで `boolean` として定義されているが、データは文字列である場合など)。
  - 複数のインターフェイスで同じテレメトリ名が定義されているが、デバイスは IoT プラグ アンド プレイに準拠していない。
- データの形態が、無効な JSON である。 詳細については、「[テレメトリ、プロパティ、およびコマンドのペイロード](concepts-telemetry-properties-commands.md)」を参照してください。

問題が含まれるカテゴリを見つけるには、シナリオに最も適したコマンドを実行します。

- テレメトリを検証するには、次のプレビュー コマンドを使用します。

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- プロパティの更新を検証するには、次のプレビュー コマンドを使用します

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

`validate` コマンドを初めて実行するときに、`uamqp` ライブラリをインストールするように求められる場合があります。

次の出力は、検証コマンドからのエラーおよび警告メッセージの例を示しています。

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

GUI を使用する場合は、IoT Central の **生データ** ビューを使用して、モデル化されていないものがないかどうかを確認します。 **生データ** ビューによって、デバイスから誤った形式の JSON が送信されているかどうかは検出されません。

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="生データ ビューのスクリーンショット":::

問題が検出された場合、デバイスのファームウェアを更新するか、以前にモデル化されていないデータをモデル化する新しいデバイス テンプレートを作成することが必要になる場合があります。

データを正しくモデル化する新しいテンプレートの作成を選択した場合は、デバイスを古いテンプレートから新しいテンプレートに移行します。 詳細については、「[Azure IoT Central アプリケーションでデバイスを管理する](howto-manage-devices.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

さらにサポートが必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートにお問い合わせください。 または、[Azure サポート チケット](https://portal.azure.com/#create/Microsoft.Support)を送信することもできます。

詳細については、「[Azure IoT のサポートとヘルプのオプション](../../iot-fundamentals/iot-support-help.md)」を参照してください。
