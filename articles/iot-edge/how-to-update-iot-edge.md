---
title: デバイス上の IoT Edge バージョンの更新 - Azure IoT Edge | Microsoft Docs
description: セキュリティ デーモンと IoT Edge ランタイムの最新バージョンを実行するように IoT Edge デバイスを更新する方法
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3dd7f78362b5f5c99dc4a74fe0a32c4d26be5b7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311919"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>IoT Edge セキュリティ デーモンおよびランタイムの更新

IoT Edge サービスの新しいバージョンがリリースされたら、最新の機能およびセキュリティの強化のために、IoT Edge デバイスを更新する必要があります。 この記事では、新しいバージョンが使用可能になったときに、IoT Edge デバイスを更新する方法について説明します。 

新しいバージョンに移行する場合、IoT Edge デバイスの 2 つのコンポーネントを更新する必要があります。 1 つ目は、デバイスで実行され、デバイスの起動時にランタイム モジュールを起動するセキュリティ デーモンです。 現在、セキュリティ デーモンは、デバイス自体からのみ更新できます。 2 つ目のコンポーネントはランタイムで、IoT Edge ハブと IoT Edge エージェント モジュールから構成されます。 デプロイの構成方法に応じて、ランタイムはデバイスから、またはリモートで更新できます。 

Azure IoT Edge の最新バージョンを見つけるには、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に関する記事を参照してください。

>[!IMPORTANT]
>Windows デバイスで Azure IoT Edge を実行していて、次のいずれかがご利用のデバイスに当てはまる場合は、バージョン 1.0.5 に更新しないでください。 
>* デバイスを Windows ビルド 17763 にアップグレードしていない。 IoT Edge バージョン 1.0.5 は、17763 よりも前の Windows ビルドをサポートしていません。
>* Windows デバイス上で Java または Node.js モジュールを実行している。 Windows デバイスを最新のビルドに更新している場合でも、バージョン 1.0.5 はスキップしてください。 
>
>IoT Edge バージョン 1.0.5 の詳細については、[1.0.5 のリリース ノート](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5)を参照してください。 開発ツールが最新バージョンに更新されないようにする方法の詳細については、[IoT 開発者ブログ](https://devblogs.microsoft.com/iotdev/)を参照してください。


## <a name="update-the-security-daemon"></a>セキュリティ デーモンの更新

IoT Edge セキュリティ デーモンは、IoT Edge デバイス上のパッケージ マネージャーを使用して更新する必要があるネイティブ コンポーネントです。 

デバイスで実行されているセキュリティ デーモンのバージョンを確認するには、コマンド `iotedge version` を使用します。 

### <a name="linux-devices"></a>Linux デバイス

Linux デバイスでは、apt-get または適切なパッケージ マネージャーを使用して、セキュリティ デーモンを更新します。 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Windows デバイス

Windows デバイスでは、PowerShell スクリプトを使用して、セキュリティ デーモンをアンインストールしてから、再インストールします。 インストール スクリプトにより、セキュリティ デーモンの最新バージョンが自動的に取得されます。 

管理者 PowerShell セッションで、セキュリティ デーモンをアンインストールします。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

パラメーターなしで `Uninstall-SecurityDaemon` コマンドを実行すると、デバイスから 2 つのランタイム コンテナー イメージと共にセキュリティ デーモンが削除されるだけです。 config.yaml ファイルは、Moby コンテナー エンジンのデータと同様に、デバイス上にも保存されています。 構成情報を保存すると、インストール プロセス中に、接続文字列または Device Provisioning Service 情報をデバイスに再び提供する必要がなくなります。 

IoT Edge デバイスで Windows コンテナーまたは Linux コンテナーのいずれを使用しているかに応じて、セキュリティ デーモンを再インストールします。 **\<Windows または Linux\>** のフレーズを適切なコンテナー オペレーティング システムに置き換えます。 デバイス上の既存の config.yaml ファイルをポイントするには、**-ExistingConfig** フラグを使用します。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

セキュリティ デーモンの特定のバージョンをインストールする場合は、[IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)から適切な iotedged-windows.zip ファイルをダウンロードします。 次に、`-OfflineInstallationPath` パラメーターを使用してファイルの場所を指定します。 詳細については、「[オフライン インストール](how-to-install-iot-edge-windows.md#offline-installation)」を参照してください。

## <a name="update-the-runtime-containers"></a>ランタイム コンテナーの更新

IoT Edge エージェントおよび IoT Edge ハブ コンテナーを更新する方法は、デプロイにローリング タグ (1.0 など) を使用しているか、または特定のタグ (1.0.2 など) を使用しているかによって異なります。 

デバイス上の IoT Edge エージェントおよび IoT Edge ハブ モジュールの現在のバージョンを確認するには、コマンド `iotedge logs edgeAgent` または `iotedge logs edgeHub` を使用します。 

  ![ログ内でコンテナー バージョンを検索する](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge のタグについて

IoT Edge エージェントおよび IoT Edge ハブ イメージには、関連付けられている IoT Edge のバージョンでタグ付けされます。 ランタイム イメージでタグを使用する方法は 2 つあります。 

* **ローリング タグ** - バージョン番号の先頭の 2 つの値のみを使用して、これらの数字に一致する最新のイメージを取得します。 たとえば、最新の 1.0.x バージョンを指す新しいリリースが存在するたびに、1.0 が更新されます。 IoT Edge デバイス上のコンテナー ランタイムによって、再度イメージが取得されると、ランタイム モジュールが最新バージョンに更新されます。 開発目的では、このアプローチが推奨されます。 Azure portal からのデプロイでは、既定でローリング タグに設定されます。 
* **特定のタグ** - バージョン番号の 3 つすべての値を使用して、イメージのバージョンを明示的に設定します。 たとえば、1.0.2 はその最初のリリース後に変更されることはありません。 更新する準備ができたら、配置マニフェストに新しいバージョン番号を宣言できます。 運用環境目的では、このアプローチが推奨されます。

### <a name="update-a-rolling-tag-image"></a>ローリング タグ イメージの更新

デプロイでローリング タグを使用している (mcr.microsoft.com/azureiotedge-hub:**1.0** など) 場合、デバイス上のコンテナー ランタイムに強制的にイメージの最新バージョンを取得させる必要があります。 

IoT Edge デバイスからイメージのローカル バージョンを削除します。 Windows マシンでは、セキュリティ デーモンをアンインストールするとランタイム イメージも削除されるため、この手順をもう一度実行する必要はありません。 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

強制の `-f` フラグを使用して、イメージを削除する必要がある場合があります。 

IoT Edge サービスによって、ランタイム イメージの最新バージョンが取得され、自動的にデバイス上でそれらが再度起動されます。 

### <a name="update-a-specific-tag-image"></a>特定のタグ イメージの更新

デプロイで特定のタグを使用している (mcr.microsoft.com/azureiotedge-hub:**1.0.2** など) 場合、行う必要がある作業は、配置マニフェスト内のタグを更新し、デバイスに変更を適用することだけです。 

Azure portal で、ランタイム デプロイ イメージは、**[Edge ランタイムの詳細設定を構成する]** セクションで宣言されています。 

![Edge ランタイムの詳細設定を構成する](./media/how-to-update-iot-edge/configure-runtime.png)

JSON の配置マニフェストでは、**systemModules** セクションのモジュール イメージを更新します。 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>次の手順

最新の [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を確認する

[モノのインターネットのブログ](https://azure.microsoft.com/blog/topics/internet-of-things/)の最新の更新とお知らせによって最新情報を得る 