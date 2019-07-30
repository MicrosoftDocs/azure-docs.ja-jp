---
title: Linux 上への Azure IoT Edge のインストール | Microsoft Docs
description: Ubuntu を実行している Linux AMD64 デバイス上に Azure IoT Edge をインストールする手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: bbab0d8d0947c18cf8e6c178d12fdbd7b335d2b6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485894"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Linux に Azure IoT Edge ランタイムをインストールする (x64)

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。

詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、Ubuntu Linux x64 (Intel/AMD) IoT Edge デバイスに Azure IoT Edge ランタイムをインストールする手順を示します。 サポートされている AMD64 オペレーティング システムの一覧については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください。

> [!NOTE]
> Linux ソフトウェア リポジトリ内のパッケージは、各パッケージ (/usr/share/doc/*パッケージ名*) 内にあるライセンス条項の対象となります。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

## <a name="install-the-latest-version"></a>最新バージョンをインストールする

以下のセクションを使用して、最新バージョンの Azure IoT Edge サービスをデバイスにインストールします。 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft キーとソフトウェア リポジトリ フィードを登録する

IoT Edge ランタイムをインストールできるようにデバイスを準備します。


リポジトリ構成をインストールします。 Ubuntu のリリースに適した **16.04** または **18.04** コード スニペットのどちらかを選択します。

> [!NOTE]
> 使用しているバージョンの Ubuntu の正しいコード ボックスからコード スニペットを選択するようにしてください。

* **Ubuntu 16.04** の場合:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu 18.04** の場合:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
生成された一覧をコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG 公開キーをインストールする

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>コンテナー ランタイムをインストールする

Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー ランタイムに依存します。 実稼働環境シナリオでは、以下の [Moby ベース](https://mobyproject.org/) エンジンを使用することをお勧めします。 これは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

apt update を実行します。

   ```bash
   sudo apt-get update
   ```

Moby エンジンをインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

Moby コマンドライン インターフェイス (CLI) をインストールします。 CLI は開発には役立ちますが、実稼働環境には省略可能です。

   ```bash
   sudo apt-get install moby-cli
   ```

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Linux カーネルを確認して Moby の互換性を確保する

組み込みデバイスの多くの製造元が、カスタム Linux カーネルを含むデバイス イメージを提供しています。これらのカーネルには、コンテナー ランタイムの互換性を確保するために必要な機能が欠けている場合があります。 推奨される [Moby](https://github.com/moby/moby) コンテナー ランタイムのインストール時に問題が発生した場合は、デバイスで次のコマンドを実行し、公式の [Moby GitHub リポジトリ](https://github.com/moby/moby)で提供されている [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) スクリプトを使って Linux カーネル構成のトラブルシューティングを行うことができます。

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

これにより、Moby ランタイムで使用されるカーネルの機能の状態が含まれた詳細な出力が提供されます。 カーネルと Moby ランタイムの完全な互換性を確保するには、`Generally Necessary` と `Network Drivers` の下のすべての項目が有効になっていることを確認します。  欠けている機能を特定したら、カーネルをソースから再構築し、カーネルの適切な .config に含める関連モジュールを選択することで、それらを有効にすることができます。同様に、defconfig や menuconfig などのカーネル構成ジェネレーターを使用している場合は、それぞれの機能を見つけて有効にし、カーネルを適宜再構築する必要があります。  新たに変更されたカーネルを展開したら、check-config スクリプトをもう一度実行して、特定された機能が正常に有効になっていることを確認します。

### <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンのインストール

**IoT Edge セキュリティ デーモン**によって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

このインストール コマンドでは、**libiothsm** の標準バージョンもインストールされます (まだ存在しない場合)。

apt update を実行します。

   ```bash
   sudo apt-get update
   ```

セキュリティ デーモンをインストールします。 パッケージは `/etc/iotedge/` にインストールされます。

   ```bash
   sudo apt-get install iotedge
   ```

IoT Edge が正常にインストールされると、構成ファイルを更新するよう求められます。 「[Azure IoT Edge セキュリティ デーモンを構成する](#configure-the-azure-iot-edge-security-daemon)」の手順に従って、デバイスのプロビジョニングを完了します。 

## <a name="install-a-specific-version"></a>特定のバージョンをインストールする

特定のバージョンの Azure IoT Edge をインストールする場合は、IoT Edge GitHub リポジトリから直接コンポーネント ファイルを対象にすることができます。 次の手順に従って、すべての IoT Edge コンポーネントをデバイスに取得します。Moby エンジンと CLI、libiothsm、IoT Edge セキュリティ デーモンの順に取得します。

1. [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。 

2. そのバージョンの **[Assets]** セクションを展開します。

3. どのリリースでも、Moby エンジンの更新プログラムがある場合とない場合があります。 **moby-engine** および **moby-cli** で始まるファイルが表示されている場合は、以下のコマンドを使用して、それらのコンポーネントを更新します。 Moby ファイルが表示されず、Moby がデバイスにまだインストールされていない場合は、それらが見つかるまで古いリリース資産を順番に調べます。 

   1. IoT Edge デバイスのアーキテクチャに対応する **moby-engine** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   2. コピーしたリンクを次のコマンドで使用して、そのバージョンの Moby エンジンをインストールします。 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. IoT Edge デバイスのアーキテクチャに対応する **moby-cli** ファイルを見つけます。 Moby CLI はオプション コンポーネントですが、開発時に役立つことがあります。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。 

   4. コピーしたリンクを次のコマンドで使用して、そのバージョンの Moby CLI をインストールします。 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. 各リリースには、IoT Edge セキュリティ デーモンと hsmlib の新しいファイルが必要です。 以下のコマンドを使用して、これらのコンポーネントを更新します。 

   1. IoT Edge デバイスのアーキテクチャに対応する **libiothsm-std** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。 

   2. コピーしたリンクを次のコマンドで使用して、そのバージョンの hsmlib をインストールします。

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. IoT Edge デバイスのアーキテクチャに対応する **iotedge** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。 

   4. コピーしたリンクを次のコマンドで使用して、そのバージョンの IoT Edge セキュリティ デーモンをインストールします。 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

IoT Edge が正常にインストールされると、構成ファイルを更新するよう求められます。 次のセクションの手順に従って、デバイスのプロビジョニングを完了します。 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンを構成する

IoT Edge ランタイムを構成して、物理デバイスを Azure IoT ハブに存在するデバイス ID にリンクします。

デーモンは、`/etc/iotedge/config.yaml` にある構成ファイルを使用して構成できます。 このファイルは既定で書き込み禁止になっています。編集するには管理者特権が必要な場合があります。

IoT Hub によって提供されるデバイス接続文字列を使用して、1 つの IoT Edge デバイスを手動でプロビジョニングすることもできますし、 Device Provisioning Service を使用して、複数のデバイスを自動的にプロビジョニングすることもできます。これは、プロビジョニングするデバイスが多数ある場合に便利です。 目的にプロビジョニング方法に応じて、適切なインストール スクリプトを選択してください。

### <a name="option-1-manual-provisioning"></a>オプション 1: 手動プロビジョニング

デバイスを手動でプロビジョニングするには、[デバイス接続文字列](how-to-register-device-portal.md)をデバイスに提供する必要があります。この文字列は、新しいデバイスを IoT ハブに登録することで作成できます。

構成ファイルを開きます。

```bash
sudo nano /etc/iotedge/config.yaml
```

ファイルのプロビジョニング セクションを見つけます。 **manual** プロビジョニング モードのコメントを解除し、dps プロビジョニング モードがコメントアウトされていることを確認します。**device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

ファイルを保存して閉じます。

   `CTRL + X`、`Y`、`Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>オプション 2: 自動プロビジョニング

デバイスを自動的にプロビジョニングするには、[Device Provisioning Service を設定し、デバイス登録 ID を取得](how-to-auto-provision-simulated-device-linux.md)します。 自動プロビジョニングは、トラステッド プラットフォーム モジュール (TPM) チップが搭載されているデバイスでのみ機能します。 たとえば、Raspberry Pi デバイスには、既定で TPM が搭載されていません。

構成ファイルを開きます。

```bash
sudo nano /etc/iotedge/config.yaml
```

ファイルのプロビジョニング セクションを見つけます。 **dps** プロビジョニング モードのコメントを解除し、手動セクションをコメントアウトします。 **scope_id** と **registration_id** の値を、IoT Hub Device Provisioning Service と TPM を搭載した IoT Edge デバイスの値で更新します。

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

ファイルを保存して閉じます。

   `CTRL + X`、`Y`、`Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

前のセクションで**手動構成**手順を使用した場合、IoT Edge ランタイムがデバイス上で正常にプロビジョニングおよび実行されている必要があります。 **自動構成**手順を使用した場合は、ランタイムが IoT ハブにデバイスを登録できるように、追加の手順を完了する必要があります。 次の手順については、「[Linux 仮想マシンでのシミュレートされた TPM IoT Edge デバイスの作成とプロビジョニング](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)」をご覧ください。

以下を使用して、IoT Edge デーモンの状態を確認できます。

```bash
systemctl status iotedge
```

以下を使用して、デーモンのログを確認します。

```bash
journalctl -u iotedge --no-pager --no-full
```

また、以下を使用して、実行中のモジュールを一覧表示します。

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>ヒントと検索候補

`iotedge` コマンドの実行には、昇格された特権が必要です。 ランタイムをインストールしたら、マシンからサインアウトした後サインインし直して、自動的にアクセス許可を更新します。 それまでは、すべての `iotedge` コマンドの前に **sudo** を使用します。

リソースに制約のあるデバイスでは、[トラブルシューティング ガイド](troubleshoot.md)に示されているように、*OptimizeForPerformance* 環境変数を *false* に設定することを強くお勧めします。

ネットワークにプロキシ サーバーがある場合は、「[Configure an IoT Edge device to communicate through a proxy server](how-to-configure-proxy-support.md)」(プロキシ サーバー経由で通信するように IoT Edge デバイスを構成する) の手順に従ってください。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

IoT Edge のインストールを Linux デバイスから削除する場合は、コマンド ラインから次のコマンドを使用します。

IoT Edge ランタイムを削除します。

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには依然として残っています。 すべてのコンテナーを表示して、どのコンテナーが残っているかを確認します。

```bash
sudo docker ps -a
```

2 つのランタイム コンテナーを含め、デバイスからコンテナーを削除します。

```bash
sudo docker rm -f <container name>
```

最後に、デバイスからコンテナー ランタイムを削除します。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>次の手順

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
