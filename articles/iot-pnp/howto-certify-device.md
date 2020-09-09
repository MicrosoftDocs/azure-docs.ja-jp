---
title: IoT プラグ アンド プレイ デバイスを認定する方法 | Microsoft Docs
description: デバイス ビルダーとして、テストを実行し、認定のためにデバイスを送信する方法について学習します
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: bdb6bf166e84bb9134bbd14454899bcefbf0a887
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949900"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>IoT プラグ アンド プレイ デバイスを認定する方法

IoT プラグ アンド プレイ デバイス認定プログラムには、IoT プラグ アンド プレイの認定要件をデバイスが満たしていることを確認するためのツールが含まれています。 また、このツールは、IoT プラグ アンド プレイ デバイスの可用性について、組織の認識を高めるのに役立ちます。 これらの認定デバイスは、IoT ソリューション向けに調整されており、市場投入までの時間を短縮するのに役立ちます。

この記事で取り上げるテクニック:

- Azure CLI 用の Azure IoT コマンド ライン ツール拡張機能をインストールする
- 開発途中の段階で、IoT プラグ アンド プレイ テストを実行してデバイス アプリケーションを確認する  
- Azure Certified Device ポータルを使用してデバイス アプリケーションを確認する

## <a name="prepare-your-device"></a>デバイスを準備する

IoT プラグ アンド プレイで実行されるアプリケーション コードで、次のことを行う必要があります。

- [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md) を使用して、Azure IoT Hub に接続する。
- [IoT プラグ アンド プレイ規則](concepts-convention.md)に従って、テレメトリ、プロパティ、およびコマンドを実装する。

アプリケーションは、オペレーティング システムとは別にインストールされているか、デバイスにフラッシュされるファームウェア イメージのオペレーティング システムにバンドルされているソフトウェアです。

認定プロセスでは、デバイスの実装が、[Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) デバイス モデルで定義されているテレメトリ、プロパティ、およびコマンドに一致し、モデルが [Azure IoT パブリック モデル リポジトリ](concepts-model-repository.md)で利用可能であることを確認することで、デバイスが IoT プラグ アンド プレイ互換であることを検証します。

認定要件を満たすために、デバイスで次のことを行う必要があります。

- [DPS](../iot-dps/about-iot-dps.md) を使用して、Azure IoT Hub に接続する。
- IoT プラグ アンド プレイ規則に従ったテレメトリ、プロパティ、またはコマンドを実装する。
- デバイスと [DTDL v2](https://aka.ms/dtdl) モデルとのやりとりについて説明する。
- モデル、および必要なすべてのインターフェイスを [Azure IoT パブリック モデル リポジトリに発行する](https://devicemodels.azureiotsolutions.com/)
- DPS プロビジョニング ペイロードで [DPS 登録](concepts-developer-guide.md#dps-payload)中にモデル ID を送信する。
- [MQTT 接続](concepts-developer-guide.md#model-id-announcement)中にモデル ID を知らせる。

## <a name="test-with-the-azure-iot-extension-cli"></a>Azure IoT 拡張機能 CLI でテストする

Azure Certified Device ポータルを通じて認定を行うためにデバイスを送信する前に、[Azure IoT CLI 拡張機能](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/product?view=azure-cli-latest)を使用して、デバイスの実装がモデルと一致することを確認できます。

次の手順は、CLI を使用して認定テストを準備し、実行する方法を示しています。

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能をインストールする

インストール手順を参照し、ご利用の環境に [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を設定します。

Azure IoT 拡張機能をインストールするには、次のコマンドを実行します。

```azurecli
az extension add --name azure-iot
```

詳細については、「[Azure IoT の Azure CLI](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot?view=azure-cli-latest)」を参照してください。

### <a name="create-a-new-product-test"></a>新しい製品のテストを作成する

次のコマンドでは、DPS を使用し、対称キーの構成証明方法でテストを作成します。

- テストする新しい製品を作成し、テスト構成を生成します。 出力には、デバイスでプロビジョニングに使用する必要がある DPS 情報 (主キー、デバイス ID、ID スコープ) が表示されます。
- モデルを記述する DTDL ファイルを含むフォルダーを指定します。

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> CLI を使用する場合は、サブスクリプションに[サインインする](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)必要があります。

コマンドからの JSON 出力には、デバイスを接続するときに使用する `primaryKey`、`registrationId`、`scopeID` が含まれています。

予想される出力:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>ご利用のデバイスを接続する

前のコマンドの DPS 情報の出力を使用して、デバイスをテスト IoT Hub インスタンスに接続します。

### <a name="manage-and-configure-the-product-tests"></a>製品テストを管理および構成する

デバイスが接続され、IoT ハブとやりとりする準備ができたら、製品テストの構成ファイルを生成します。 ファイルを作成するには、次のようにします。

- 前のコマンドの出力からのテスト `id` を使用します。
- `--wait` パラメーターを使用して、テスト ケースを取得します。

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

予想される出力:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

`az iot product test case update` コマンドを使用して、テスト構成ファイルを変更することができます。

### <a name="run-the-tests"></a>テストを実行する

テスト構成を生成した後、次の手順でテストを実行します。 前のコマンドと同じ `devicetestId` をパラメーターとして使用して、テストを実行します。 テスト結果を確認し、テストの状態がすべて `Passed` になっていることを確かめます。

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

テストの実行の出力例

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Azure Certified Device ポータルを使用してテストする

次の手順では、[Azure Certified Device ポータル](https://aka.ms/acdp)を使用して、オンボード、製品の詳細の登録、ファースト ステップ ガイドの送信、および認定テストを行う方法を示します。

> [!NOTE]
> このドキュメントの執筆時点では、ポータルで [Azure IoT 認定デバイス カタログ](https://aka.ms/devicecatalog)への発行はサポートされていません。

### <a name="onboarding"></a>オンボード

[認定ポータル](https://aka.ms/acdp)を使用するには、職場または学校のテナントからの Azure Active Directory を使用する必要があります。

Azure IoT パブリック モデル リポジトリにモデルを発行するには、アカウントが [Microsoft Partner Network](https://partner.microsoft.com) のメンバーである必要があります。 デバイス カタログに発行する前に、システムによって Microsoft Partner Network ID が存在し、アカウントが十分調査されていることが確認されます。

### <a name="company-profile"></a>会社のプロファイル

会社のプロファイルは、左側のナビゲーション メニューから管理できます。 会社のプロファイルには、会社の URL、電子メール アドレス、および会社のロゴが含まれています。 認定操作を行う前に、このページでプログラム契約に同意する必要があります。

会社のプロファイル情報は、デバイス カタログで紹介されているデバイスの説明に使用されます。

### <a name="create-new-project"></a>新しいプロジェクトの作成

デバイスを認定するには、最初に新しいプロジェクトを作成する必要があります。

[認定ポータル](https://aka.ms/acdp)に移動します。 **[プロジェクト]** ページで、 *[+ 新しいプロジェクトの作成]* を選択します。 その後、プロジェクトの名前とデバイス名を入力し、デバイス クラスを選択します。

認定プロセス中に提供する製品情報は、次の 4 つのカテゴリに分類されます。

- デバイス情報。 デバイスに関する情報 (その名前、説明、認定、オペレーティング システムなど) を収集します。
- **ファースト ステップ** ガイド。 デバイスを発行する前に、システム管理者によって承認されるように、このガイドを PDF ドキュメントとして送信する必要があります。
- マーケティングの詳細。 デバイスについて、顧客向けのマーケティング情報を提供します。 マーケティング情報には、説明、写真、およびディストリビューターが含まれます。
- 追加の業界認定。 この省略可能なセクションでは、デバイスによって取得されたその他の認定に関する追加情報を提供できます。

### <a name="connect-and-test"></a>接続してテストする

接続とテストの手順では、デバイスが IoT プラグ アンド プレイの認定要件を満たしていることを確認します。

次の 3 つの手順を行います。

1. 接続してインターフェイスを検出します。 デバイスは、DPS 経由で Azure IoT 認定サービスに接続する必要があります。 使用する認証方法 (x.509 証明書、対称キー、またはトラステッド プラットフォーム モジュール) を選び、デバイス アプリケーションを DPS 情報で更新します。
1. インターフェイスを確認します。 インターフェイスを確認し、それぞれにテストに適したペイロード入力があることを確かめます。
1. テストします。 システムにより、各デバイス モデルがテストされ、モデルに記述されているテレメトリ、プロパティ、およびコマンドが IoT プラグ アンド プレイ規則に従っていることが確認されます。 テストが完了したら、 **[ログの表示]** リンクを選択して、デバイスからのテレメトリと、IoT Hub デバイス ツインのプロパティに送信された生データを確認します。

## <a name="next-steps"></a>次のステップ

これでデバイスの送信が完了したので、[iotcert@microsoft.com](mailto:iotcert@microsoft.com) でデバイス認定チームに連絡して、次の手順に進むことができます。これには、Microsoft Partner Network メンバーシップの確認や、ファースト ステップ ガイドの確認が含まれます。 すべての要件が満たされたら、デバイスを [Azure IoT 認定デバイス カタログ](https://aka.ms/devicecatalog)に含めることを選択できます。
