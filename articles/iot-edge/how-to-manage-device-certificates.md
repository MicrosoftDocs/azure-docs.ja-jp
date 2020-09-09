---
title: デバイス証明書を作成する - Azure IoT Edge | Microsoft Docs
description: テスト証明書を作成し、それらを Azure IoT Edge デバイスにインストールして運用環境のデプロイの準備をします。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4c49345f7036dfee7d1f37c15a4647202b3e5670
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257835"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>IoT Edge デバイスで証明書を管理する

すべての IoT Edge デバイスは、証明書を使用して、デバイス上で実行されるランタイムと任意のモジュール間の安全な接続を作成します。 ゲートウェイとして機能している IoT Edge デバイスは、これらの同じ証明書を使用してダウンストリームのデバイスに接続します。

## <a name="install-production-certificates"></a>運用環境の証明書をインストールする

初めて IoT Edge をインストールしてデバイスをプロビジョニングする場合、サービスをテストできるように、デバイスは一時的な証明書を使用して設定されます。
これらの一時的な証明書は 90 日で有効期限が切れます。もしくは、マシンを再起動するとリセットされます。
運用環境に移行した後、またはゲートウェイデバイスを作成する場合は、独自の証明書を指定する必要があります。
この記事では、証明書を IoT Edge デバイスにインストールするステップを説明します。

さまざまな種類の証明書や、それらの役割についての詳細は、「[Azure IoT Edge 証明書の使用方法の詳細](iot-edge-certs.md)」を参照してください。

>[!NOTE]
>この記事全体で使用される「ルート CA」という用語は、自分の IoT ソリューションの証明書チェーンのうち、最上位の公開証明機関を示しています。 シンジケート証明機関の証明書ルートや、組織の証明機関のルートを使用する必要はありません。 多くの場合、実際は中間 CA の公開証明書です。

### <a name="prerequisites"></a>前提条件

* [Windows](how-to-install-iot-edge-windows.md) または [Linux](how-to-install-iot-edge-linux.md) 上で実行されている IoT Edge デバイス。
* 自己署名したか、Baltimore、Verisign、DigiCert、GlobalSign などの信頼されている商用証明機関から購入したルート証明機関 (CA) の証明書がある。

ルート証明機関がまだないものの、運用証明書を必要とする IoT Edge 機能を試してみたい (ゲートウェイ シナリオなど) 場合、「[デモ証明書を作成して IoT Edge デバイスの機能をテストする](how-to-create-test-certificates.md)」を参照してください。

### <a name="create-production-certificates"></a>運用証明書を作成する

独自の証明機関を使用して次のファイルを作成する必要があります。

* ルート CA
* デバイス CA 証明書
* デバイス CA 秘密キー

この記事で*ルート CA* と呼ばれているものは、組織の最上位の証明機関ではありません。 これは IoT Edge シナリオの最上位の証明機関であり、IoT Edge ハブ モジュール、ユーザー モジュール、他のダウンストリームのデバイスはこれを利用してお互いの間で信頼関係を確立します。

> [!NOTE]
> 現時点では、libiothsm の制限により、2050 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

これらの証明書の例を確認するには、「[サンプルとチュートリアルのためのテスト CA 証明書を管理する](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)」にある、デモ証明書を作成するスクリプトをご確認ください。

### <a name="install-certificates-on-the-device"></a>証明書をデバイスにインストールする

証明書チェーンを IoT Edge デバイスにインストールし、新しい証明書を参照するように IoT Edge ランタイムを構成します。

たとえば、サンプル スクリプトを使用して[デモ証明書を作成](how-to-create-test-certificates.md)した場合、次のファイルを IoT Edge デバイスにコピーします。

* デバイス CA 証明書: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* デバイス CA 秘密キー: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* ルート CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 3 つの証明書とキー ファイルを IoT Edge デバイスにコピーします。

   [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) のようなサービスや、[Secure copy protocol](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。  IoT Edge デバイス自体で証明書を生成した場合は、この手順をスキップして、作業ディレクトリへのパスを使用することができます。

1. IoT Edge セキュリティ デーモン構成ファイルを開きます。

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. config.yaml 内の **certificate** プロパティを、IoT Edge デバイス上の証明書ファイルとキー ファイルへのファイル URI パスに設定します。 証明書プロパティの前の `#` 文字を削除して、4 行をコメント解除します。 **certificates:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。 次に例を示します。

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. Linux デバイスでは、証明書を保持しているディレクトリの読み取り権限をユーザー **iotedge**が必ず保持しているようにします。

1. 以前にこのデバイスの IoT Edge に他の証明書を使用していた場合は、IoT Edge を起動または再起動する前に、次の 2 つのディレクトリにあるファイルを削除します。

   * Windows: `C:\ProgramData\iotedge\hsm\certs` および `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` および `/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>証明書の有効期間をカスタマイズする

IoT Edge では、次のような複数の場合に、デバイスに証明書を自動的に生成します。

* IoT Edge のインストールとプロビジョニングを行うときに自身の運用証明書を提供しない場合は、IoT Edge セキュリティ マネージャーによって、**デバイス CA 証明書**が自動的に生成されます。 この自己署名証明書は、運用環境ではなく、開発およびテスト シナリオのみを対象としています。 この証明書は 90 日後に有効期限が切れます。
* IoT Edge セキュリティ マネージャーは、デバイス CA 証明書によって署名された**ワークロード CA 証明書**も生成します

IoT Edge デバイスでのさまざまな証明書の機能の詳細については、「[Azure IoT Edge での証明書の使用方法について理解する](iot-edge-certs.md)」を参照してください。

自動的に生成されたこれらの 2 つの証明書については、証明書の有効期間の日数を構成する **auto_generated_ca_lifetime_days** フラグを config.yaml で設定するオプションが用意されています。

>[!NOTE]
>IoT Edge セキュリティ マネージャーによって自動生成される 3 つ目の証明書として、**IoT Edge ハブ サーバー証明書**があります。 この証明書の有効期間は常に 90 日ですが、有効期限が切れる前に自動的に更新されます。 **auto_generated_ca_lifetime_days** 値は、この証明書には影響しません。

証明書の有効期限を既定値の 90 日以外に構成するには、config. yaml ファイルの **certificates** セクションに日数の値を追加します。

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

> [!NOTE]
> 現時点では、libiothsm の制限により、2050 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

自身のデバイス CA 証明書を指定した場合でも、設定した有効期間の値がデバイス CA 証明書の有効期間より短ければ、この値がワークロード CA 証明書に適用されます。

config.yaml ファイルでフラグを指定した後に、次の手順を実行します。

1. `hsm` フォルダーの内容を削除します。

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux: `/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. IoT Edge サービスを再起動します。

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. 有効期間の設定を確認します。

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   自動的に生成されたデバイス CA 証明書の有効期限が切れるまでの日数が表示された **production readiness: certificates** チェックの出力を確認します。

## <a name="next-steps"></a>次のステップ

証明書を IoT Edge デバイス上にインストールするステップは、ソリューションを運用環境にデプロイする前に必ず実行する必要があります。 詳細については、「[IoT Edge ソリューションを運用環境にデプロイするための準備を行う](production-checklist.md)」を参照してください。
