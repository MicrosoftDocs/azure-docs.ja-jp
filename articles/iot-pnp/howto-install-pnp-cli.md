---
title: Azure CLI 用 Azure IoT 拡張機能を使用して、IoT プラグ アンド プレイ プレビュー デバイスと対話する | Microsoft Docs
description: Azure CLI 用 Azure IoT 拡張機能をインストールし、IoT ハブに接続された IoT プラグ アンド プレイ デバイスとの対話に使用します。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 41a626ba602ad33f22c3ea4acc39dd4f3438cbd0
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935516"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能をインストールして使用する

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT ハブなどの Azure リソースを管理するためのオープンソースのクロス プラットフォーム コマンドライン ツールです。 Azure CLI は、Windows、Linux、および MacOS で使用できます。 Azure CLI は [Azure Cloud Shell](https://shell.azure.com) にもプレインストールされています。 Azure CLI を使用すると、拡張機能をインストールしなくても、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを管理できます。

Azure CLI の Azure IoT 拡張機能は、IoT プラグ アンド プレイ プレビュー デバイスとの対話とテストに使用されるコマンドライン ツールです。 拡張機能は、次のことに使用できます。

- デバイスに接続する。
- デバイスが送信するテレメトリを表示する。
- デバイスのプロパティを操作する。
- デバイスのコマンドを呼び出す。

この記事では、その方法について説明します。

- Azure CLI 用 Azure IoT 拡張機能をインストールして構成する。
- 拡張機能を使用して、デバイスとの対話とテストを行う。
- 拡張機能を使用して、モデル リポジトリ内のインターフェイスを管理する。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能をインストールする

### <a name="step-1---install-the-azure-cli"></a>手順 1 - Azure CLI をインストールする

[インストール手順](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に従って、環境内の Azure CLI を設定します。 以下のすべてのコマンドを使用するには、Azure CLI バージョンが 2.0.73 以降である必要があります。 検証するには、`az -–version` を使用します。

### <a name="step-2---install-iot-extension"></a>手順 2 - IoT 拡張機能をインストールする

[IoT 拡張機能の readme](https://github.com/Azure/azure-iot-cli-extension) に、拡張機能をインストールするためのいくつかの方法が説明されています。 最も簡単な方法は、`az extension add --name azure-cli-iot-ext` を実行することです。 インストール後、`az extension list` を使用して、現在インストールされている拡張機能を確認することができます。また、`az extension show --name azure-cli-iot-ext` を使用して、IoT 拡張機能の詳細を表示することもできます。 拡張機能を削除するには、`az extension remove --name azure-cli-iot-ext` を使用します。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能を使用する

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションにサインインするには、次のコマンドを実行します。

```cmd/sh
az login
```

> [!NOTE]
> Azure クラウド シェルを使用している場合は、自動的にサインインされるので、前のコマンドを実行する必要はありません。

Azure CLI 用 Azure IoT 拡張機能を使用するには、以下が必要です。

- Azure IoT Hub。 お使いの Azure サブスクリプションに IoT ハブを追加する方法にはさまざまなものがあります。一例を挙げると、[Azure CLI を使用して IoT ハブを作成する](../iot-hub/iot-hub-create-using-cli.md)という方法があります。 Azure IoT 拡張機能コマンドを実行するには、IoT ハブの接続文字列が必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

    > [!NOTE]
    > パブリック プレビュー中、IoT プラグ アンド プレイ機能は、**米国中部**、**北ヨーロッパ**、および**東日本**の各リージョンで作成された IoT ハブでのみご利用いただけます。

- お使いの IoT ハブに登録されているデバイス。 次の Azure CLI コマンドを使用してデバイスを登録することができます。`{YourIoTHubName}` と `{YourDeviceID}` のプレースホルダーは必ず実際の値に置き換えてください。

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 一部のコマンドには、会社モデル リポジトリの接続文字列が必要です。 初めて [Azure Certified for IoT ポータルにオンボードする](howto-onboard-portal.md)ときに会社のモデル リポジトリが作成されます。 サード パーティが、インターフェイスとモデルへのアクセス権をユーザーに付与するために、モデル リポジトリの接続文字列をユーザーと共有することがあります。

### <a name="interact-with-a-device"></a>デバイスと対話する

この拡張機能を使用して、IoT ハブに接続されている IoT プラグ アンド プレイ デバイスを表示し、操作できます。 この拡張機能は、IoT プラグ アンド プレイ デバイスを表すデジタル ツインで動作します。

#### <a name="list-devices-and-interfaces"></a>デバイスとインターフェイスを一覧表示する

IoT ハブ上のすべてのデバイスの一覧を表示します。

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

IoT プラグ アンド プレイ デバイスによって登録されたすべてのインターフェイスを一覧表示します。

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>properties

デバイス上のインターフェイスのすべてのプロパティとプロパティ値を一覧表示します。

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

読み取りおよび書き込みプロパティの値を設定します。

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

デバイスの **sensor** インターフェイスの **name** プロパティを **Contoso** に設定するペイロード ファイルの例は、次のようになります。

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>command

デバイス上のインターフェイスのすべてのコマンドを一覧表示します。

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

`--repo-login` パラメーターを指定しない場合、このコマンドにはパブリック モデル リポジトリが使用されます。

コマンドを呼び出します。

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --command-name {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="telemetry"></a>テレメトリ

**$Default** イベント ハブ エンドポイントにアクセスする特定のデバイスとインターフェイスからのすべての IoT プラグ アンド プレイ テレメトリを監視します。

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

特定のコンシューマー グループにアクセスする特定のデバイスとインターフェイスからのすべての IoT プラグ アンド プレイ テレメトリを監視します。

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>モデル リポジトリ内のインターフェイスを管理する

次のコマンドでは、パブリック IoT プラグ アンド プレイ モデル リポジトリを使用します。 会社モデル リポジトリを使用するには、モデル リポジトリの接続文字列を指定した `--login` 引数を追加します。

パブリック IoT プラグ アンド プレイ モデル リポジトリ内のインターフェイスを一覧表示します。

```cmd/sh
az iot pnp interface list
```

パブリック IoT プラグ アンド プレイ モデル リポジトリ内のインターフェイスを表示します。

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

IoT プラグ アンド プレイの会社モデル リポジトリ内にインターフェイスを作成します。

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

パブリック モデル リポジトリ内にインターフェイスを直接作成することはできません。

IoT プラグ アンド プレイの会社モデル リポジトリ内のインターフェイスを更新します。

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

パブリック モデル リポジトリ内にインターフェイスを直接更新することはできません。

IoT プラグ アンド プレイの会社モデル リポジトリからパブリック モデル リポジトリにインターフェイスを発行します。 この操作により、インターフェイスは変更できなくなります。

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

パブリック モデル リポジトリにインターフェイスを発行できるのは Microsoft パートナーのみです。

### <a name="manage-device-capability-models-in-a-model-repository"></a>モデル リポジトリ内のデバイス機能モデルを管理する

次のコマンドでは、パブリック IoT プラグ アンド プレイ モデル リポジトリを使用します。 会社モデル リポジトリを使用するには、モデル リポジトリの接続文字列を指定した `--login` 引数を追加します。

IoT プラグ アンド プレイ パブリック モデル リポジトリ内のデバイス機能モデルを一覧表示します。

```cmd/sh
az iot pnp capability-model list
```

IoT プラグ アンド プレイ パブリック モデル リポジトリ内のデバイス機能モデルを表示します。

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

IoT プラグ アンド プレイの会社モデル リポジトリ内にデバイス機能モデルを作成します。

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

パブリック モデル リポジトリ内にモデルを直接作成することはできません。

IoT プラグ アンド プレイの会社モデル リポジトリ内のデバイス機能モデルを更新します。

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

パブリック モデル リポジトリ内にモデルを直接更新することはできません。

IoT プラグ アンド プレイの会社モデル リポジトリからパブリック モデル リポジトリにデバイス機能モデルを発行します。 この操作により、モデルは変更できなくなります。

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

パブリック モデル リポジトリにモデルを発行できるのは Microsoft パートナーのみです。

## <a name="next-steps"></a>次の手順

このハウツー記事では、Azure CLI 用 Azure IoT 拡張機能をインストールして使用し、プラグ アンド プレイ デバイスと対話する方法を学習しました。 次は、[モデルを管理する](./howto-manage-models.md)方法について学習することをお勧めします。
