---
title: Linux 上への Azure IoT Edge のインストール | Microsoft Docs
description: Ubuntu または Raspbian を実行している Linux AMD64 デバイス上に Azure IoT Edge をインストールする手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: kgremban
ms.openlocfilehash: d73f3a37bb084533733b27b49ac171747cee814c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85321875"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Debian ベースの Linux システムに Azure IoT Edge ランタイムをインストールする

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、X64、ARM32、または ARM64 Linux デバイス上に Azure IoT Edge ランタイムをインストールする手順について説明します。 Ubuntu Server 16.04、Ubuntu Server 18.04、および Raspbian Stretch 用のインストール パッケージが提供されています。 サポートされている Linux オペレーティング システムおよびアーキテクチャの一覧については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください。

> [!NOTE]
> Linux ソフトウェア リポジトリ内のパッケージは、各パッケージ (/usr/share/doc/*パッケージ名*) 内にあるライセンス条項の対象となります。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

## <a name="install-iot-edge-and-container-runtimes"></a>IoT Edge とコンテナーのランタイムをインストールする

以下のセクションを使用して、最新バージョンの Azure IoT Edge ランタイムをデバイスにインストールします。

>[!NOTE]
>ARM64 デバイスのサポートは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft キーとソフトウェア リポジトリ フィードを登録する

IoT Edge ランタイムをインストールできるようにデバイスを準備します。

リポジトリ構成をインストールします。 デバイスのオペレーティング システムに対応する **16.04** または **18.04** コマンドを選択します。

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

生成された一覧をコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>コンテナー ランタイムをインストールする

Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー ランタイムに依存します。 実稼働環境シナリオでは、以下の [Moby ベース](https://mobyproject.org/) エンジンを使用することをお勧めします。 Moby エンジンは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

デバイスのパッケージ リストを更新します。

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

Moby コンテナー ランタイムをインストールするときにエラーが発生した場合は、この記事の後半にある「[Linux カーネルを確認して Moby の互換性を確保する](#verify-your-linux-kernel-for-moby-compatibility)」の手順に従ってください。

### <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンのインストール

**IoT Edge セキュリティ デーモン**によって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

使用できる IoT Edge のバージョンを確認します。

   ```bash
   apt list -a iotedge
   ```

最新バージョンのセキュリティ デーモンをインストールするには、次のコマンドを使用します。これにより、**libiothsm-std** パッケージの最新バージョンもインストールされます。

   ```bash
   sudo apt-get install iotedge
   ```

特定のバージョンのセキュリティ デーモンをインストールする場合は、apt リスト出力から該当するバージョンを指定します。 また、**libiothsm-std** パッケージにも同じバージョンを指定します。そうしないと、最新バージョンがインストールされます。 たとえば、次のコマンドでは、1.0.8 リリースの最新バージョンがインストールされます。

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

インストールしたいバージョンがリストに表示されていない場合は、「[リリース資産を使用してランタイムをインストールする](#install-runtime-using-release-assets)」の手順に従ってください。 そのセクションでは、IoT Edge セキュリティ デーモンの以前の任意のバージョン、またはリリース候補のバージョンをターゲットとする方法が説明されています。

`/etc/iotedge/` に IoT Edge が正常にインストールされると、その出力に、構成ファイルを更新するよう求めるメッセージが表示されます。 次のセクションに進み、デバイス プロビジョニングを完了します。

## <a name="configure-the-security-daemon"></a>セキュリティ デーモンを構成する

IoT Edge ランタイムを構成して、物理デバイスを Azure IoT ハブに存在するデバイス ID にリンクします。

デーモンは、`/etc/iotedge/config.yaml` にある構成ファイルを使用して構成できます。 このファイルは既定で書き込み禁止になっています。編集するには管理者特権が必要な場合があります。

IoT Hub によって提供されるデバイス接続文字列を使用して、1 つの IoT Edge デバイスを手動でプロビジョニングすることもできますし、 Device Provisioning Service を使用して、複数のデバイスを自動的にプロビジョニングすることもできます。これは、プロビジョニングするデバイスが多数ある場合に便利です。 目的にプロビジョニング方法に応じて、適切なインストール スクリプトを選択してください。

### <a name="option-1-manual-provisioning"></a>オプション 1: 手動プロビジョニング

デバイスを手動でプロビジョニングするには、[デバイス接続文字列](how-to-register-device.md#register-in-the-azure-portal)をデバイスに提供する必要があります。この文字列は、新しいデバイスを IoT ハブに登録することで作成できます。

構成ファイルを開きます。

```bash
sudo nano /etc/iotedge/config.yaml
```

ファイルのプロビジョニング構成を見つけ、**Manual provisioning configuration** (手動プロビジョニングの構成) セクションをコメント解除します。 **device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。**provisioning:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

クリップボードの内容を Nano に貼り付けるには、`Shift+Right Click` キーまたは `Shift+Insert` キーを押します。

ファイルを保存して閉じます。

   `CTRL + X`、`Y`、`Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>オプション 2:自動プロビジョニング

IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml) を使用して自動的にプロビジョニングできます。 現在 IoT Edge では、自動プロビジョニングを使用するときに 3 つの構成証明メカニズムがサポートされていますが、ご利用のハードウェアの要件によってはご自分の選択が制限される場合があります。 たとえば、Raspberry Pi デバイスには、既定でトラステッド プラットフォーム モジュール (TPM) チップが搭載されていません。 詳細については、次の記事を参照してください。

* [Linux 仮想マシン上で、仮想 TPM を使用する IoT Edge デバイスを作成し、プロビジョニングする](how-to-auto-provision-simulated-device-linux.md)
* [X.509 証明書を使用して IoT Edge デバイスを作成およびプロビジョニングする](how-to-auto-provision-x509-certs.md)
* [対称キーの構成証明を使用して IoT Edge デバイスを作成およびプロビジョニングする](how-to-auto-provision-symmetric-keys.md)

これらの記事では、DPS で登録を設定し、構成証明用の適切な証明書またはキーを生成する手順について説明しています。 プロビジョニング情報は、選択した構成証明メカニズムに関係なく、お使いの IoT Edge デバイスの IoT Edge 構成ファイルに追加されます。

構成ファイルを開きます。

```bash
sudo nano /etc/iotedge/config.yaml
```

ファイルのプロビジョニング構成を見つけ、構成証明メカニズムに適したセクションをコメント解除します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。**provisioning:** の行の先頭には空白文字を入れず、入れ子の項目には 2 つの空白でインデントする必要があります。 **scope_id** の値は、お使いの IoT Hub Device Provisioning Service インスタンスの値で更新し、構成証明のフィールドには適切な値を指定します。

TPM の構成証明:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

X.509 の構成証明:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

対称キーの構成証明:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

クリップボードの内容を Nano に貼り付けるには、`Shift+Right Click` キーまたは `Shift+Insert` キーを押します。

ファイルを保存して閉じます。 `CTRL + X`、`Y`、`Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

前のセクションで**手動構成**手順を使用した場合、IoT Edge ランタイムがデバイス上で正常にプロビジョニングおよび実行されている必要があります。 **自動構成**手順を使用した場合は、ランタイムが IoT ハブにデバイスを登録できるように、追加の手順を完了する必要があります。 次の手順については、「[Linux 仮想マシンでのシミュレートされた TPM IoT Edge デバイスの作成とプロビジョニング](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)」をご覧ください。

以下により、IoT Edge デーモンの状態を確認できます。

```bash
systemctl status iotedge
```

デーモンのログを調べます:

```bash
journalctl -u iotedge --no-pager --no-full
```

[トラブルシューティング ツール](troubleshoot.md#run-the-check-command)を実行して、最も一般的な構成およびネットワーク エラーを確認します。

```bash
sudo iotedge check
```

最初のモジュールをデバイス上の IoT Edge にデプロイするまで、 **$edgeHub** システム モジュールはデバイスに展開されません。 その結果、自動チェックからは `Edge Hub can bind to ports on host` 接続チェックのエラーが返されます。 このエラーは、モジュールをデバイスに展開した後に発生した場合でなければ、無視できます。

最後に、実行中のモジュールを一覧表示します。

```bash
sudo iotedge list
```

デバイスに IoT Edge をインストールした後、実行されているモジュールは **edgeAgent** だけです。 最初のデプロイを作成した後は、その他のシステム モジュール **$edgeHub** もデバイスで起動します。 詳細については、[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に関する記事を参照してください。

## <a name="tips-and-troubleshooting"></a>ヒントとトラブルシューティング

`iotedge` コマンドの実行には、昇格された特権が必要です。 ランタイムをインストールしたら、マシンからサインアウトした後サインインし直して、自動的にアクセス許可を更新します。 それまでは、すべての `iotedge` コマンドの前に **sudo** を使用します。

リソースに制約のあるデバイスでは、[トラブルシューティング ガイド](troubleshoot.md)に示されているように、*OptimizeForPerformance* 環境変数を *false* に設定することを強くお勧めします。

ネットワークにプロキシ サーバーがある場合は、「[Configure an IoT Edge device to communicate through a proxy server](how-to-configure-proxy-support.md)」(プロキシ サーバー経由で通信するように IoT Edge デバイスを構成する) の手順に従ってください。

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Linux カーネルを確認して Moby の互換性を確保する

組み込みデバイスの多くの製造元が、カスタム Linux カーネルを含むデバイス イメージを提供しています。これらのカーネルには、コンテナー ランタイムの互換性を確保するために必要な機能がありません。 推奨される Moby コンテナー ランタイムのインストール中に問題が発生した場合は、デバイスで次のコマンドを実行し、公式の [Moby GitHub リポジトリ](https://github.com/moby/moby)にある [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) スクリプトを使って Linux カーネル構成のトラブルシューティングを行うことができます。 デバイスで次のコマンドを実行して、カーネル構成を確認します。

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

このコマンドにより、Moby ランタイムで使用されるカーネルの機能の状態が含まれた詳細な出力が提供されます。 カーネルと Moby ランタイムの完全な互換性を確保するには、`Generally Necessary` と `Network Drivers` の下のすべての項目が有効になっていることを確認します。  欠けている機能を特定したら、カーネルをソースから再構築し、カーネルの適切な .config に含める関連モジュールを選択することで、それらを有効にします。同様に、`defconfig` や `menuconfig` などのカーネル構成ジェネレーターを使用している場合は、それぞれの機能を見つけて有効にし、ご自分のカーネルを適宜再構築します。  新たに変更されたカーネルを展開したら、check-config スクリプトをもう一度実行して、必要なすべての機能が正常に有効になっていることを確認します。

## <a name="install-runtime-using-release-assets"></a>リリース資産を使用してランタイムをインストールする

特定のバージョンの Moby と、`apt-get install` を介して使用できない Azure IoT Edge ランタイムをインストールする場合は、このセクションの手順を使用します。 Microsoft パッケージのリストには、最近のバージョンとそのサブバージョンの限られたセットしか含まれていないので、これらの手順は、古いバージョンまたはリリース候補バージョンをインストールするユーザーが対象となります。

curl コマンドを使用すると、IoT Edge GitHub リポジトリから直接、コンポーネント ファイルをターゲットにすることができます。 次の手順に従って、すべての IoT Edge コンポーネントをデバイスに取得します。Moby エンジンと CLI、libiothsm、IoT Edge セキュリティ デーモンの順に取得します。

1. [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。

2. そのバージョンの **[Assets]** セクションを展開します。

3. どのリリースでも、Moby エンジンの更新プログラムがある場合とない場合があります。 **moby-engine** および **moby-cli** で始まるファイルが表示されている場合は、以下のコマンドを使用して、それらのコンポーネントを更新します。 Moby ファイルが表示されない場合は、最新バージョンが見つかるまで古いリリース資産を順番に調べます。

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

`/etc/iotedge` に IoT Edge が正常にインストールされると、その出力に、構成ファイルを更新するよう求めるメッセージが表示されます。 「[セキュリティ デーモンを構成する](#configure-the-security-daemon)」セクションの手順に従って、デバイスのプロビジョニングを完了します。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

IoT Edge のインストールを Linux デバイスから削除する場合は、コマンド ラインから次のコマンドを使用します。

IoT Edge ランタイムを削除します。

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには残っています。 すべてのコンテナーを表示して、どのコンテナーが残っているかを確認します。

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

## <a name="next-steps"></a>次のステップ

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
