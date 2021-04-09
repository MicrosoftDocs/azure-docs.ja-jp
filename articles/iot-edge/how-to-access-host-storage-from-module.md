---
title: モジュールから IoT Edge デバイスのローカル ストレージを使用する - Azure IoT Edge | Microsoft Docs
description: 環境変数と作成オプションを使用して、モジュールが IoT Edge デバイスのローカル ストレージにアクセスできるようにします。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d88d35eece698c7d0079221ae3c76058d1877948
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200472"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>モジュールにデバイスのローカル ストレージへのアクセスを許可する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Storage サービスを使用して、またはデバイスのコンテナー ストレージにデータを格納することに加えて、特にオフラインで操作しているときは、信頼性を向上させるために、ホスト IoT Edge デバイス自体のストレージを専用で使用することもできます。

## <a name="link-module-storage-to-device-storage"></a>モジュール ストレージをデバイス ストレージにリンクする

モジュール ストレージからホスト システム上のストレージへのリンクを有効にするには、コンテナー内のストレージ フォルダーを指す、モジュール用の環境変数を作成します。 その後、作成オプションを使用して、ホスト コンピューター上のフォルダーにそのストレージ フォルダーをバインドします。

たとえば、IoT Edge ハブがデバイスのローカル ストレージにメッセージを格納し、後で取得できるようにする場合は、Azure portal の **[ランタイムの設定]** セクションで環境変数と作成オプションを構成できます。

1. IoT Edge ハブと IoT Edge エージェントの両方について、モジュール内のディレクトリを指す **storageFolder** という名前の環境変数を追加します。
1. IoT Edge ハブと IoT Edge エージェントの両方について、ホスト コンピューター上のローカル ディレクトリをモジュール内のディレクトリに接続するバインドを追加します。 次に例を示します。

   ![ローカル ストレージの作成オプションと環境変数を追加する](./media/how-to-access-host-storage-from-module/offline-storage.png)

または、配置マニフェストでローカル ストレージを直接構成することもできます。 次に例を示します。

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

`<HostStoragePath>` と `<ModuleStoragePath>` を実際のホストとモジュールのストレージ パスに置き換えます。どちらの値も絶対パスにする必要があります。

たとえば、Linux システムでは、`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` は、ご利用のホスト システム上のディレクトリ **/etc/iotedge/storage** がコンテナー内のディレクトリ **/iotedge/storage/** にマップされていることを意味します。 Windows システムでは、別の例として、`"Binds":["C:\\temp:C:\\contemp"]` は、ご利用のホスト システム上のディレクトリ **C:\\temp** がコンテナー内のディレクトリ **C:\\contemp** にマップされていることを意味します。

さらに、Linux デバイスでは、モジュールのユーザー プロファイルが、ホスト システム ディレクトリに対して必要な読み取り、書き込み、および実行のアクセス許可を持っていることを確認します。 IoT Edge ハブがデバイスのローカル ストレージにメッセージを格納できるようにする前述の例に戻ると、ユーザー プロファイル UID 1000 にアクセス許可を付与する必要があります。 (IoT Edge エージェントはルートとして動作するため、追加のアクセス許可は必要ありません。)Linux システム上でディレクトリのアクセス許可を管理するには、`chown` を使用してディレクトリの所有者を変更してから `chmod` を使用してアクセス許可を変更するなど、いくつかの方法があります。たとえば、次のようにします。

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

作成オプションの詳細については、[Docker ドキュメント](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)を参照してください。

## <a name="encrypted-data-in-module-storage"></a>モジュール ストレージのデータを暗号化する

モジュールで IoT Edge デーモンのワークロード API が呼び出され、データが暗号化されるとき、その暗号化キーはモジュール ID とモジュールの生成 ID から派生されます。 生成 ID は、モジュールがデプロイから削除された後に、別のモジュールが同じモジュール ID で同じデバイスにデプロイされたとき、シークレットを保護するために使用されます。 モジュールの生成 ID は、[az iot hub module-identity show](/cli/azure/ext/azure-iot/iot/hub/module-identity) の Azure CLI コマンドで参照できます。

世代にわたってモジュールでファイルを共有したい場合は、暗号化の解除に失敗するので、シークレットが含まれていないようにする必要があります。

## <a name="next-steps"></a>次のステップ

モジュールからホスト ストレージにアクセスするその他の例については、「[IoT Edge 上の Azure Blob Storage を使用してエッジにデータを格納する](how-to-store-data-blob.md)」を参照してください。
