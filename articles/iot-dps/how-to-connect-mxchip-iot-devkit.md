---
title: Azure IoT Hub Device Provisioning Service の自動プロビジョニングを使用して MXChip IoT DevKit を IoT Hub に登録する方法 | Microsoft Docs
description: Azure IoT Hub Device Provisioning Service の自動プロビジョニングを使用して MXChip IoT DevKit を IoT Hub に登録する方法。
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: c809077b0ec14240d231e9b281285080f68596b3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439501"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Azure IoT Hub Device Provisioning Service の自動プロビジョニングを使用して MXChip IoT DevKit を IoT Hub に登録します

この記事では、Azure IoT Hub Device Provisioning Service の[自動プロビジョニング](concepts-auto-provisioning.md)を使用して MXChip IoT DevKit を Azure IoT Hub に登録する方法について説明します。 このチュートリアルでは、以下の内容を学習します。

* デバイスに Device Provisioning Service のグローバル エンドポイントを構成する。
* 一意デバイス シークレット (UDS) を使って X.509 証明書を生成する。
* 個々のデバイスを登録する。
* デバイスが登録されていることを確認する。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 Visual Studio Code で [Azure IoT Device Workbench](https://aka.ms/iot-workbench) または [Azure IoT Tools](https://aka.ms/azure-iot-tools) 拡張機能パックを使用して、これを開発することができます。 また、DevKit には、Azure サービスを活用したモノのインターネット (IoT) ソリューションのプロトタイプを作成する手順を示す[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が付属しています。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、最初に次のタスクを行います。

* 「[IoT DevKit AZ3166 をクラウドの Azure IoT Hub に接続する](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment)」の「開発環境の準備」セクションの手順に従って、DevKit の Wi-Fi を構成し、開発環境を準備します。
* 「[Update DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)」(DevKit のファームウェアを更新する) チュートリアルで、最新のファームウェア (1.3.0 以降) にアップグレードします。
* 「[Azure portal で IoT Hub Device Provisioning Service を設定する](/azure/iot-dps/quick-setup-auto-provision)」の手順に従って、IoT Hub を作成し、Device Provisioning Service インスタンスとリンクします。

## <a name="open-sample-project"></a>サンプル プロジェクトを開く

1. IoT DevKit がお使いのコンピューターに接続されて**いない**ことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。

1. `F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench:Open Examples...]\(Azure IoT Device Workbench: 例を開く...\)** を入力して選択します。次に、 **[IoT DevKit]** をボードとして選択します。

1. [IoT Workbench Examples]\(IoT Workbench の例\) ページで **[Device Registration with DPS]\(DPS によるデバイスの登録\)** を見つけて **[Open Sample]\(サンプルを開く\)** をクリックします。 次に、サンプル コードをダウンロードするための既定のパスを選択します。
    ![サンプルを開く](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>デバイスのセキュリティ ストレージに一意のデバイス シークレットを保存する

自動プロビジョニングは、デバイスの[構成証明メカニズム](concepts-security.md#attestation-mechanism)に基づいてデバイスで構成できます。 MXChip IoT DevKit では、[Trusted Computing Group](https://trustedcomputinggroup.org) の [Device Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) を使います。 DevKit の STSAFE セキュリティ チップ ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) に保存された "**一意のデバイス シークレット**" (UDS) は、デバイス固有の [X.509](concepts-security.md#x509-certificates) 証明書の生成に使われます。 この証明書は後で Device Provisioning Service での登録プロセスで使用され、また、実行時の登録中に使用されます。

一般的な UDS は、次の例のような 64 文字の文字列です。

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

UDS を DevKit に保存するには、次の手順を実行します。

1. VS Code で、ステータス バーをクリックして選択して、DevKit の COM ポートを選択します。
  ![COM ポートを選択する](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. DevKit で**ボタン A** を押しながら、**リセット** ボタンを押して離した後、**ボタン A** を離します。DevKit が構成モードに移行します。

1. `F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench:デバイス設定の構成...] > [Config Unique Device String (UDS)]\(一意のデバイス文字列 (UDS) の構成)\)** を入力して選択します。
  ![UDS を構成する](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. 生成された UDS 文字列をメモします。 X.509 証明書を生成するために必要です。 次に、`Enter` を押します。
  ![UDS をコピーする](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. UDS が STSAFE に正しく構成されたことを示す通知から確認します。
  ![UDS の成功を構成する](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> または、Putty などのユーティリティを使用してシリアル ポートから UDS を構成することもできます。 これを行うには「[Use configuration mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)」 (構成オードを使用する) に従ってください。

## <a name="update-the-global-device-endpoint-and-id-scope"></a>グローバル デバイスのエンドポイントと ID スコープを更新する

デバイス コードでは、[デバイス プロビジョニングのエンドポイント](/azure/iot-dps/concepts-service#device-provisioning-endpoint)と ID スコープを指定してテナントの分離を確実にする必要があります。

1. Azure portal で、Device Provisioning Service の **[概要]** ウィンドウを選び、 **[グローバル デバイス エンドポイント]** と **[ID スコープ]** の値を書き留めます。
  ![Device Provisioning Service のグローバル エンドポイントと ID スコープ](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. **DeKitDPS.ino** を開きます。 `[Global Device Endpoint]` と `[ID Scope]` を探して、書き留めた値に置き換えます。
  ![デバイス プロビジョニング サービスのエンドポイント](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. コードの `registrationId` 変数を入力します。 最大 128 文字で、英数字、小文字、ハイフンの組み合わせが許可されています。 また、値を書き留めます。
  ![登録 ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. `F1` をクリックし、 **[Azure IoT Device Workbench:Upload Device Code]\(Azure IoT Device Workbench: デバイス コードのアップロード\)** を入力して選択します。 コンパイルが開始され、コードが DevKit にアップロードされます。
  ![デバイスのアップロード](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>X.509 証明書を生成する

このサンプルで使用される[構成証明メカニズム](/azure/iot-dps/concepts-device#attestation-mechanism)は、X.509 証明書です。 それを生成するユーティリティを使用する必要があります。

> [!NOTE]
> X.509 証明書ジェネレーターは現在 Windows のみサポートしています。

1. VS Code で、`F1` をクリックし、 **[Open New Terminal ]\(新しいターミナルを開く\)** を入力して選択し、ターミナル ウィンドウを開きます。

1. `tool` フォルダーで `dps_cert_gen.exe` を実行します。

1. `..\.build\DevKitDPS` としてコンパイルされたバイナリ ファイルの場所を指定します。 書き留めた **UDS** と **registrationId** を貼り付けます。 
  ![X.509 を生成する](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. `.pem` X.509 証明書は同じフォルダーに生成されます。
  ![X.509 ファイル](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>デバイス登録エントリを作成する

1. Microsoft Azure portal で、Device Provision Service を開き、[登録を管理します] に移動して、 **[個別登録の追加]** をクリックします。
  ![個別登録の追加](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. **[プライマリ証明書の .pem ファイルまたは .cer ファイル:]** の隣のファイル アイコンをクリックし、生成された `.pem` ファイルをアップロードします。
  ![.pem のアップロード](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>DevKit が Azure IoT Hub に登録されたことを確認する

DevKit の**リセット** ボタンを押します。 DevKit 画面に **"DPS Connected!"\(DPS 接続済み!\)** というメッセージが 表示されます。 デバイスが再起動すると、次のアクションが実行されます。

1. デバイスが Device Provisioning Service に登録要求を送信します。
1. Device Provisioning Service が登録チャレンジを送信し、デバイスがこれに応答します。
1. 登録が成功すると、Device Provisioning Service は、IoT Hub の URI、デバイス ID、暗号化されたキーをデバイスに送信します。
1. デバイス上の IoT Hub クライアント アプリケーションがハブに接続します。
1. ハブに正常に接続されると、IoT Hub の Device Explorer にデバイスが表示されます。
  ![登録されたデバイス](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、IoT Hub の [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を参照するか、以下のチャネルからサポートにお問い合わせください。

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、デバイスが Azure IoT Hub に自動的に登録できるように、Device Identity Composition Engine を使って Device Provisioning Service に安全にデバイスを登録する方法を学習しました。 

まとめると、次の方法を学習しました。

> [!div class="checklist"]
> * デバイスに Device Provisioning Service のグローバル エンドポイントを構成する。
> * 一意デバイス シークレットを使って X.509 証明書を生成する。
> * 個々のデバイスを登録する。
> * デバイスが登録されていることを確認する。

[シミュレートされたデバイスを作成してプロビジョニングする](./quick-create-simulated-device.md)方法を学習してください。

