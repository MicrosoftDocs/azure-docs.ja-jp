---
title: Azure CLI 用 Azure IoT 拡張機能を使用して、IoT プラグ アンド プレイ プレビュー デバイスと対話する | Microsoft Docs
description: Azure CLI 用 Azure IoT 拡張機能をインストールし、IoT ハブに接続された IoT プラグ アンド プレイ デバイスとの対話に使用します。
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: dadb1f044547acd6e5f0d274143123e89d7dae46
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475483"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能をインストールして使用する

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT ハブなどの Azure リソースを管理するためのオープンソースのクロス プラットフォーム コマンドライン ツールです。 Azure CLI は、Windows、Linux、macOS で使用できます。 Azure CLI を使用すると、拡張機能をインストールしなくても、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを管理できます。

Azure CLI の Azure IoT 拡張機能は、IoT プラグ アンド プレイ プレビュー デバイスとの対話とテストに使用されるコマンドライン ツールです。 拡張機能は、次のことに使用できます。

- デバイスに接続する。
- デバイスが送信するテレメトリを表示する。
- デバイスのプロパティを操作する。
- デバイスのコマンドを呼び出す。

この記事で取り上げるテクニック:

- Azure CLI 用 Azure IoT 拡張機能をインストールして構成する。
- 拡張機能を使用して、デバイスとの対話とテストを行う。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能をインストールする

### <a name="step-1---install-the-azure-cli"></a>手順 1 - Azure CLI をインストールする

[インストール手順](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に従って、環境内の Azure CLI を設定します。 最適なエクスペリエンスになるようにするには、Azure CLI のバージョンがバージョン 2.9.1 以上である必要があります。 検証するには、`az -–version` を使用します。

### <a name="step-2---install-iot-extension"></a>手順 2 - IoT 拡張機能をインストールする

[IoT 拡張機能の readme](https://github.com/Azure/azure-iot-cli-extension) に、拡張機能をインストールするためのいくつかの方法が説明されています。 最も簡単な方法は、`az extension add --name azure-iot` を実行することです。 インストール後、`az extension list` を使用して、現在インストールされている拡張機能を確認することができます。また、`az extension show --name azure-iot` を使用して、IoT 拡張機能の詳細を表示することもできます。 この記事の執筆時点での拡張機能のバージョン番号は `0.9.7` です。

拡張機能を削除するには、`az extension remove --name azure-iot` を使用します。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能を使用する

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションにサインインするには、次のコマンドを実行します。

```azurecli
az login
```

Azure CLI 用 Azure IoT 拡張機能を使用するには、以下が必要です。

- Azure IoT Hub。 お使いの Azure サブスクリプションに IoT ハブを追加する方法にはさまざまなものがあります。一例を挙げると、[Azure CLI を使用して IoT ハブを作成する](../iot-hub/iot-hub-create-using-cli.md)という方法があります。 Azure IoT 拡張機能コマンドを実行するには、IoT ハブの接続文字列が必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- お使いの IoT ハブに登録されているデバイス。 次の Azure CLI コマンドを使用してデバイスを登録することができます。`{YourIoTHubName}` と `{YourDeviceID}` のプレースホルダーは必ず実際の値に置き換えてください。

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>デバイスと対話する

この拡張機能を使用して、IoT ハブに接続されている IoT プラグ アンド プレイ デバイスを表示し、操作できます。 この拡張機能は、IoT プラグ アンド プレイ デバイスを表すデジタル ツインで動作します。

#### <a name="list-devices"></a>デバイスを一覧表示する

IoT ハブ上のすべてのデバイスの一覧を表示します。

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>デジタル ツインを表示する

デバイスのデジタル ツインを表示します。

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Properties

読み取りおよび書き込みプロパティの値を設定します。

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>コマンド

コマンドを呼び出します。

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>デジタル ツイン イベント

**$Default** イベント ハブ コンシューマー グループにアクセスする特定のデバイスとインターフェイスからのすべての IoT プラグ アンド プレイ デジタル ツイン イベントを監視します。

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>モデル リポジトリ内のモデルを管理する

Azure CLI のモデル リポジトリ コマンドを使用して、リポジトリ内のモデルを管理できます。

#### <a name="create-model-repository"></a>モデル リポジトリを作成する

テナントの最初のユーザーである場合、テナント用の新しい IoT プラグ アンド プレイ会社リポジトリを作成します。

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>モデル リポジトリのテナント ロールを管理する

ユーザーまたはサービス プリンシパルに対して、特定のリソースへのロール割り当てを作成します。

たとえば、テナントに対する **ModelsCreator** のロールを user@consoso.com に付与します。

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

または、特定のモデルに対する **ModelAdministrator** のロールを user@consoso.com に付与します。

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>モデルを作成する

会社のリポジトリに新しいモデルを作成します。

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>モデルを検索する

特定のキーワードに一致するモデルの一覧を表示します。

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>モデルを公開する

会社のリポジトリに存在するデバイス モデルをパブリック リポジトリに公開します。

たとえば、`dtmi:com:example:ClimateSensor;1` という ID のモデルを公開します。

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

モデルを公開するには、次の要件を満たしている必要があります。

- 会社または組織のテナントは、Microsoft パートナーである必要があります。 
- ユーザーまたはサービス プリンシパルは、リポジトリ テナントの **Publisher** ロールのメンバーである必要があります。

## <a name="next-steps"></a>次のステップ

このハウツー記事では、Azure CLI 用の Azure IoT 拡張機能をインストールして使用し、IoT プラグ アンド プレイ デバイスと対話する方法を学習しました。 次は、[デバイスで Azure IoT Explorer](./howto-use-iot-explorer.md) を使用する方法について学習することをお勧めします。
