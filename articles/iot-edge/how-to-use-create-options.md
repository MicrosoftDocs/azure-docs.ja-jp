---
title: モジュールの createOptions を記述する - Azure IoT Edge | Microsoft Docs
description: 配置マニフェストで createOptions を使用して実行時にモジュールを構成する方法
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ee5536562eb3f2008908a36ff296ef2cfa337ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200613"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>IoT Edge モジュールのコンテナー作成オプションを構成する方法

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

配置マニフェストの **createOptions** パラメーターを使用すると、実行時にモジュール コンテナーを構成できます。 このパラメーターは、モジュールに対する制御を拡張し、ホスト デバイスのリソースに対するモジュールのアクセスを許可または制限したり、ネットワークを構成したりするなどのタスクを可能にします。

IoT Edge モジュールは、Docker と互換性のあるコンテナーとして IoT Edge デバイスに実装されます。 Docker にはコンテナーを作成するための多くのオプションが用意されており、これらのオプションは IoT Edge モジュールにも適用されます。 詳細については、[Docker コンテナーの作成オプション](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)に関するページを参照してください。

## <a name="format-create-options"></a>作成オプションの書式設定

IoT Edge 配置マニフェストは、JSON として書式設定された作成オプションを受け入れます。 たとえば、すべての edgeHub モジュールに自動的に含まれる作成オプションを見てみましょう。

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

この edgeHub の例では、**HostConfig.PortBindings** パラメーターを使用して、コンテナーの公開されているポートをホスト デバイスのポートにマップしています。

Visual Studio または Visual Studio Code の Azure IoT Tools 拡張機能を使用する場合は、JSON 形式の作成オプションを **deployment.template.json** ファイルに書き込むことができます。 その後、拡張機能を使用して IoT Edge ソリューションをビルドしたり、配置マニフェストを生成したりすると、IoT Edge ランタイムが想定する形式で JSON が文字列化されます。 次に例を示します。

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

作成オプションを記述するためのヒントの 1 つは、`docker inspect` コマンドを使用することです。 開発プロセスの一部として、`docker run <container name>` を使用してモジュールをローカルで実行します。 モジュールが想定どおりに動作するようになったら、`docker inspect <container name>` を実行します。 このコマンドは、モジュールの詳細を JSON 形式で出力します。 構成したパラメーターを探し、JSON をコピーします。 次に例を示します。

[![docker inspect edgeHub の結果](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>一般的なシナリオ

コンテナーの作成オプションを使用すると、多くのシナリオに対応できますが、IoT Edge ソリューションを構築するときに最も頻繁に発生するものがいくつかあります。

* [モジュールにホスト記憶域へのアクセスを許可する](how-to-access-host-storage-from-module.md)
* [ホスト ポートをモジュール ポートにマップする](#map-host-port-to-module-port)
* [モジュールのメモリと CPU の使用量を制限する](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>ホスト ポートをモジュール ポートにマップする

モジュールが IoT Edge ソリューションの外部のサービスと通信する必要があり、そのためにメッセージ ルーティングを使用していない場合は、ホスト ポートをモジュール ポートにマップする必要があります。

>[!TIP]
>このポート マッピングは、同じデバイス上のモジュール間通信には必要ありません。 モジュール A がモジュール B でホストされている API に対してクエリを実行する必要がある場合は、ポート マッピングなしで実行できます。 モジュール B は、dockerfile 内でポートを公開する必要があります (例: `EXPOSE 8080`)。 モジュール A は、モジュール B の名前を使用して、API に対してクエリを実行できます (例: `http://ModuleB:8080/api`)。

まず、モジュール内のポートが接続をリッスンするように公開されていることを確認します。 これを行うには、dockerfile で [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 命令を使用します。 たとえば、「 `EXPOSE 8080` 」のように入力します。 公開命令では、指定しない場合は既定で TCP プロトコルに設定されます。または、UDP を指定することもできます。

次に、[Docker コンテナー作成オプション](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)の **HostConfig** グループの **PortBindings** 設定を使用して、モジュール内の公開されているポートをホスト デバイスのポートにマップします。 たとえば、モジュール内でポート 8080 を公開し、それをホスト デバイスのポート 80 にマップする場合、template.json ファイルの作成オプションは次の例のようになります。

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

配置マニフェスト用に文字列化されると、同じ構成は次の例のようになります。

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>モジュールのメモリと CPU の使用量を制限する

モジュールが使用できるホスト リソースの量を宣言できます。 この制御は、1 つのモジュールが大量のメモリまたは CPU を消費しないようにし、他のプロセスがデバイス上で実行されないようにするために役立ちます。 これらの設定を管理するには、次のような **HostConfig** グループの [Docker コンテナー作成オプション](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)を使用します。

* **メモリ**:メモリの制限 (バイト単位)。 たとえば、268435456バイト = 256 MB になります。
* **MemorySwap**:合計メモリ制限 (メモリ + スワップ) たとえば、536870912 バイト = 512 MB になります。
* **CpuPeriod**:CPU 期間の長さ (マイクロ秒単位)。 既定値は 100000 です。たとえば、25000 の値を指定すると、コンテナーが CPU リソースの 25% に制限されます。

テンプレートの JSON 形式では、これらの値は次の例のようになります。

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

最終的な配置マニフェスト用に文字列化されると、これらの値は次の例のようになります。

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>次のステップ

実際の作成オプションのその他の例については、次の IoT Edge のサンプルを参照してください。

* [Raspberry Pi 3 での Custom Vision と Azure IoT Edge](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge の BLOB ストレージ サンプル](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
