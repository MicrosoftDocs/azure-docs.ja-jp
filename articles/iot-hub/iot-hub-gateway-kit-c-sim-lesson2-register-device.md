---
title: "シミュレートされたデバイスと Azure IoT Gateway - レッスン 2: デバイスの登録 | Microsoft Docs"
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure IoT ハブ, モノのインターネット クラウド, Azure IoT ハブでのデバイスの作成, TI SensorTag, TI BLE"
ms.assetid: 23cfbe21-22c6-4fe1-ae41-63714a897f12
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 759085dcb4993d38f79cb19efe58e1a968ddb682
ms.lasthandoff: 01/25/2017


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>Azure IoT ハブを作成してデバイスを登録する

## <a name="what-you-will-do"></a>学習内容

- リソース グループを作成する
- 最初の IoT ハブを作成する
- Azure CLI を使用してデバイスを IoT ハブに登録する 

デバイスを IoT ハブに追加すると、Azure IoT Hub サービスによって、サービスで認証するために使用するデバイスのキーが生成されます。 

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-sim-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

このレッスンでは、次の内容を学習します。

- Azure CLI を使用して IoT ハブを作成する方法。
- IoT ハブにデバイスを登録する方法。

## <a name="what-you-need"></a>必要なもの

- 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](http://azure.microsoft.com/pricing/free-trial/)を数分で作成できます。
- Azure CLI のインストール。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

IoT ハブを作成するには、次の手順を実行します。

1. 次のコマンドを実行して Azure アカウントにサインインします。

   ```bash
   az login
   ```

   サインインの成功後に、使用できるサブスクリプションがすべて一覧表示されます。

2. 次のコマンドを実行して、既定で使用する Azure サブスクリプションを設定します。

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name` は、`az login` または `az account list` コマンドの出力内で確認できます。

3. 次のコマンドを実行して、プロバイダーを登録します。 リソース プロバイダーは、アプリケーションにリソースを提供するサービスです。 プロバイダーによって提供される Azure リソースをデプロイする前に、プロバイダーを登録しておく必要があります。

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. 次のコマンドを実行して、米国西部リージョンに `iot-gateway` という名前のリソース グループを作成します。

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus` は、リソース グループを作成する場所です。 別の場所を使用する場合は、`az account list-locations -o table` を実行して、Azure でサポートされているすべての場所を確認します。

5. 次のコマンドを実行して、`iot-gateway` リソース グループに IoT ハブを作成します。

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

既定では、IoT ハブは Free 価格レベルで作成されます。 詳細については、「[Azure IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub/)」を参照してください。

> [!NOTE]
> IoT ハブの名前は、グローバルに一意である必要があります。 Azure サブスクリプションで作成できる F1 エディションの Azure IoT Hub は&1; つのみです。

## <a name="register-your-device-in-your-iot-hub"></a>IoT ハブへのデバイスの登録

IoT ハブとの間でメッセージの交換を行う各デバイスは、一意の ID を使用して登録する必要があります。
次のコマンドを実行して、デバイスを IoT ハブに登録します。

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>まとめ

IoT ハブを作成し、デバイス ID を使用して IoT ハブに論理デバイスを登録しました。 物理デバイスからクラウド内の IoT ハブにデータを送信するゲートウェイ サンプル アプリケーションを構成して実行する方法を学習する準備ができました。

## <a name="next-steps"></a>次のステップ
[シミュレート デバイスからクラウドへのアップロードを実行するサンプル アプリケーションの構成と実行](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)
