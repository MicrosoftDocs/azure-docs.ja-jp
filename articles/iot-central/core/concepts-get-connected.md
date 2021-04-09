---
title: Azure IoT Central のデバイス接続機能 | Microsoft Docs
description: この記事では、Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: dc0655aba424d29a4055f0d50a20057f22d084ed
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015457"
---
# <a name="get-connected-to-azure-iot-central"></a>Azure IoT Central に接続する

"*この記事は、オペレーターとデバイス開発者を対象としています。* "

この記事では、Azure IoT Central アプリケーションにデバイスを接続する方法について説明します。 デバイスが IoT Central とデータを交換できるようにするには、デバイスで次を行う必要があります。

- *認証*。 IoT Central アプリケーションに対する認証では、_Shared Access Signature (SAS) トークン_ または _X.509 証明書_ が使用されます。 運用環境では、X.509 証明書はお勧めしません。
- *登録*。 デバイスは IoT Central アプリケーションに登録されている必要があります。 登録されているデバイスは、アプリケーションの **[デバイス]** ページで確認できます。
- *デバイス テンプレートに関連付ける*。 IoT Central アプリケーションでは、デバイス テンプレートによって、接続されているデバイスを表示、管理するためにオペレーターが使用する UI が定義されます。

IoT Central では、次の 2 つのデバイス登録シナリオがサポートされています。

- *自動登録*。 デバイスは、初めて接続するときに自動的に登録されます。 このシナリオでは、事前登録なしで接続する手段も用意されており、そのようなデバイスを OEM は大量に製造することができます。 OEM が適切なデバイス資格情報を生成し、デバイスを工場で構成することになります。 必要に応じて、データの送信を開始する前にオペレーターによるデバイスの承認を必須とすることもできます。 このシナリオでは、アプリケーションで X.509 または SAS _グループ登録_ を構成する必要があります。
- *手動登録*。 オペレーターは、 **[デバイス]** ページで個々のデバイスを登録するか、[CSV ファイルをインポート](howto-manage-devices.md#import-devices)してデバイスを一括登録します。 このシナリオでは、X.509 か SAS _グループ登録_、または X.509 か SAS _個別登録_ を使用できます。

IoT Central に接続するデバイスは、*IoT プラグ アンド プレイの規則* に従う必要があります。 この規則の 1 つは、デバイスが実装するデバイス モデルの _モデル ID_ を接続時に送信する必要があることです。 モデル ID により、IoT Central アプリケーションはデバイスを適切なデバイス テンプレートに関連付けることができます。

IoT Central では、[Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) を使用して接続プロセスが管理されます。 デバイスは、アプリケーションに接続するうえで必要な情報を取得するために、まず DPS エンドポイントに接続します。 IoT Central アプリケーションは、内部的に IoT ハブを使用してデバイスの接続を処理します。 DPS を使用すると、次のことが可能になります。

- IoT Central で、大量のデバイスのオンボーディングと接続をサポートできます。
- デバイスの資格情報を生成し、デバイスをオフラインで構成できます。IoT Central UI を通じてデバイスを登録する必要はありません。
- 独自のデバイス ID を使用して、IoT Central にデバイスを登録できます。 独自のデバイス ID を使用して、既存のバックオフィス システムとの統合を簡略化できます。
- 1 つの一貫した方法で IoT Central にデバイスを接続できます。

この記事では、次のデバイス接続手順について説明します。

- [X.509 グループ登録](#x509-group-enrollment)
- [SAS グループ登録](#sas-group-enrollment)
- [個別加入](#individual-enrollment)
- [デバイスの登録](#device-registration)
- [デバイス テンプレートへのデバイスの関連付け](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X.509 グループ登録

運用環境では、X.509 証明書を使用することが、IoT Central に対して推奨されるデバイス認証メカニズムです。 詳細については、「[X.509 CA 証明書を使用したデバイス認証](../../iot-hub/iot-hub-x509ca-overview.md)」をご覧ください。

X.509 証明書を使用してデバイスをアプリケーションに接続するには、次のようにします。

1. **証明書 (X.509)** 構成証明の種類を使用する *登録グループ* を作成します。
1. 登録グループで、中間またはルート X.509 証明書を追加して検証します。
1. 登録グループのルート証明書または中間証明書からリーフ証明書を生成します。 アプリケーションに接続するときに、デバイスからリーフ証明書を送信します。

詳細については、[X.509 証明書を使用してデバイスを接続する方法](how-to-connect-devices-x509.md)に関するページを参照してください。

### <a name="for-testing-purposes-only"></a>テスト目的のみ

テストの場合にのみ、次のユーティリティを使用して、ルート証明書、中間証明書、およびデバイス証明書を生成できます。

- [Azure IoT Device Provisioning Device SDK 用ツール](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): X.509 の証明書とキーの生成および検証に使用できる一連の Node.js ツールです。
- DevKit デバイスを使用している場合、この[コマンドライン ツール](https://aka.ms/iotcentral-docs-dicetool)では、証明書を検証するために IoT Central アプリケーションに追加できる CA 証明書が生成されます。
- [サンプルとチュートリアルに使用するテスト CA 証明書の管理](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): 次の作業を目的とした、一連の PowerShell スクリプトと Bash スクリプトです。
  - 証明書チェーンを作成します。
  - IoT Central アプリケーションにアップロードする .cer ファイルとして証明書を保存します。
  - IoT Central アプリケーションからの確認コードを使用して検証証明書を生成します。
  - デバイス ID をツールへのパラメーターとして使用して、デバイスのリーフ証明書を作成します。

## <a name="sas-group-enrollment"></a>SAS グループ登録

SAS キーを使用してデバイスをアプリケーションに接続するには、次のようにします。

1. **Shared Access Signature (SAS)** 構成証明の種類を使用する *登録グループ* を作成します。
1. 登録グループからグループのプライマリ キーまたはセカンダリ キーをコピーします。
1. Azure CLI を使用してグループ キーからデバイス キーを生成します。

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. デバイスが IoT Central アプリケーションに接続するときに、生成されたデバイス キーを使用します。

## <a name="individual-enrollment"></a>個別加入

それぞれ独自の認証資格情報を持つデバイスを接続する場合は、個別登録を使用します。 個別登録は、接続を許可された 1 台のデバイスを対象とする登録方法です。 個別登録では、構成証明メカニズムとして X.509 リーフ証明書または (実際のトラステッド プラットフォーム モジュールまたは仮想トラステッド プラットフォーム モジュールの) SAS トークンを使用できます。 デバイス ID には、文字、数字、および `-` 文字を含めることができます。 詳細については、[DPS の個別登録](../../iot-dps/concepts-service.md#individual-enrollment)に関するセクションを参照してください。

> [!NOTE]
> デバイスの個別登録を作成すると、既定のグループ登録オプションよりもそちらが IoT Central アプリケーションで優先されます。

### <a name="create-individual-enrollments"></a>個々の登録を作成する

IoT Central では、個別登録に関して次の構成証明メカニズムがサポートされます。

- **対称キーの構成証明:** 対称キーの構成証明は、DPS インスタンスを使用してデバイスを認証するシンプルなアプローチです。 対称キーを使用する個別登録を作成するには、デバイスの **[デバイス接続]** ページを開き、接続方法として **[個別の登録]** を、メカニズムとして **[Shared Access Signature (SAS)]** を選択します。 Base64 エンコードされたプライマリ キーとセカンダリ キーを入力し、変更を保存します。 **ID スコープ**、**デバイス ID**、キー (プライマリまたはセカンダリ) を使用してデバイスを接続します。

    > [!TIP]
    > テストでは、**OpenSSL** を使用して Base64 エンコードされたキーを生成できます (`openssl rand -base64 64`)。

- **X.509 証明書:** X.509 証明書を使用する個別登録を作成するには、 **[デバイス接続]** ページを開き、接続方法として **[個別の登録]** を、メカニズムとして **[証明書 (X.509)]** を選択します。 個別登録エントリで使用するデバイス証明書には、発行者とサブジェクト CN をデバイス ID に設定しなければならないという要件があります。

    > [!TIP]
    > テストでは、[Azure IoT Device Provisioning Device SDK for Node.js 用ツール](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)を使用して自己署名証明書を生成できます (`node create_test_cert.js device "mytestdevice"`)。

- **トラステッド プラットフォーム モジュール (TPM) の構成証明:** [TPM](../../iot-dps/concepts-tpm-attestation.md) は、ハードウェア セキュリティ モジュールの一種です。 TPM を使用することが、デバイスを接続する最も安全な方法の 1 つとなっています。 この記事では、ディスクリート TPM、ファームウェア TPM、または統合された TPM を使用していると仮定します。 ソフトウェアでエミュレートされた TPM はプロトタイプの作成やテストには適していますが、ディスクリート TPM、ファームウェア TPM、または統合された TPM と同じレベルのセキュリティを提供することはできません。 運用環境ではソフトウェア TPM を使用しないでください。 TPM を使用する個別登録を作成するには、 **[デバイス接続]** ページを開き、接続方法として **[個別の登録]** を、メカニズムとして **[TPM]** を選択します。 TPM 保証キーを入力し、デバイスの接続情報を保存します。

## <a name="device-registration"></a>デバイス登録

デバイスを IoT Central アプリケーションに接続するには、そのデバイスを事前にアプリケーションに登録する必要があります。

- デバイスは、初めて接続するときに自動的に登録できます。 このオプションを使用するには、[X.509 グループ登録](#x509-group-enrollment)または [SAS グループ登録](#sas-group-enrollment)を使用する必要があります。
- オペレーターは、CSV ファイルをインポートして、アプリケーション内のデバイスの一覧を一括登録できます。
- オペレーターは、アプリケーションの **[デバイス]** ページで個々のデバイスを手動で登録できます。

IoT Central では、OEM が、自動的に自己登録するデバイスを大量に製造できます。 OEM が適切なデバイス資格情報を生成し、デバイスを工場で構成することになります。 顧客が初めてデバイスの電源を投入したとき、デバイスはまず DPS に接続し、その後 DPS によって自動的に適切な IoT Central アプリケーションに接続されます。 必要に応じて、アプリケーションへのデータの送信を開始する前に、オペレーターによるデバイスの承認を必須とすることもできます。

> [!TIP]
> デバイスからデータの送信が開始される前にオペレーターがデバイスを手動で承認しておく必要があるかどうかは、 **[管理] > [デバイス接続]** ページの **[自動承認]** オプションで制御されます。

### <a name="automatically-register-devices-that-use-x509-certificates"></a>X.509 証明書を使用するデバイスを自動的に登録する

1. [X.509 登録グループ](#x509-group-enrollment)に追加したルート証明書または中間証明書を使用して、デバイスのリーフ証明書を生成します。 デバイス ID をリーフ証明書の `CNAME` として使用してください。 デバイス ID には、文字、数字、および `-` 文字を含めることができます。

1. OEM は、デバイス ID、生成された X.509 リーフ証明書、アプリケーションの **ID スコープ** の値を使用して、各デバイスをフラッシュします。 デバイスコードも、実装するデバイス モデルのモデル ID を送信する必要があります。

1. デバイスの電源をオンにすると、そのデバイスはまず DPS に接続して、IoT Central の接続情報を取得します。

1. デバイスは、DPS からの情報を使用して、IoT Central アプリケーションに接続し、登録します。

IoT Central アプリケーションは、デバイスから送られたモデル ID を使って、[登録されているデバイスをデバイス テンプレートに関連付け](#associate-a-device-with-a-device-template)ます。

### <a name="automatically-register-devices-that-use-sas-tokens"></a>SAS トークンを使用するデバイスを自動的に登録する

1. **SAS-IoT-Devices** 登録グループからグループ プライマリ キーをコピーします。

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="SAS-IoT-Devices 登録グループのグループ プライマリ キー":::

1. `az iot central device compute-device-key` コマンドを使用してデバイスの SAS キーを生成します。 前の手順で得たグループ プライマリ キーを使用します。 デバイス ID には、文字、数字、および `-` 文字を含めることができます。

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. デバイス ID、生成されたデバイス SAS キー、アプリケーションの **ID スコープ** の値を使用して、OEM が各デバイスをフラッシュします。 デバイスコードも、実装するデバイス モデルのモデル ID を送信する必要があります。

1. デバイスの電源をオンにすると、そのデバイスはまず DPS に接続して、IoT Central の登録情報を取得します。

1. デバイスは、DPS からの情報を使用して、IoT Central アプリケーションに接続し、登録します。

IoT Central アプリケーションは、デバイスから送られたモデル ID を使って、[登録されているデバイスをデバイス テンプレートに関連付け](#associate-a-device-with-a-device-template)ます。

### <a name="bulk-register-devices-in-advance"></a>事前にデバイスを一括登録する

IoT Central アプリケーションに大量のデバイスを登録するには、CSV ファイルを使用して[デバイス ID とデバイス名をインポート](howto-manage-devices.md#import-devices)します。

デバイスで認証に SAS トークンを使用している場合は、[IoT Central アプリケーションから CSV ファイルをエクスポート](howto-manage-devices.md#export-devices)します。 エクスポートされた CSV ファイルに、デバイス ID と SAS キーが含まれています。

使用するデバイスが X.509 を使って認証を行う場合は、X.509 登録グループにアップロードしたルート証明書または中間証明書を使用して、デバイスの X.509 リーフ証明書を生成します。 インポートしたデバイス ID をリーフ証明書の `CNAME` 値として使用します。

デバイスは、アプリケーションの **ID スコープ** 値を使用して、接続時にモデル ID を送信する必要があります。

> [!TIP]
> **ID スコープ** 値は、 **[管理] > [デバイス接続]** で確認できます。

### <a name="register-a-single-device-in-advance"></a>事前に 1 つのデバイスを登録する

このアプローチは、IoT Central を使用した実験やデバイスのテストを行う場合に便利です。 **[デバイス]** ページの **[新規]** を選択して、個々のデバイスを登録します。 デバイス接続 SAS キーを使用して、IoT Central アプリケーションにデバイスを接続できます。 登録済みデバイスの接続情報から "_デバイス SAS キー_" をコピーします。

![個々のデバイスの SAS キー](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>デバイス テンプレートへのデバイスの関連付け

デバイスが接続されると、IoT Central によってデバイスがデバイス テンプレートに自動的に関連付けられます。 デバイスは、接続時に[モデル ID](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) を送信します。 IoT Central は、モデル ID を使用して、その特定のデバイス モデルのデバイス テンプレートを識別します。 検出プロセスは次のように実行されます。

1. IoT Central アプリケーションでデバイス テンプレートが既に発行されている場合、デバイスはデバイス テンプレートに関連付けられます。
1. デバイス テンプレートが IoT Central アプリケーション内でまだ公開されていない場合は、IoT Centralにおいて[パブリック モデル リポジトリ](https://github.com/Azure/iot-plugandplay-models)でデバイス モデルが検索されます。 モデルが見つかると、それを使用して基本のデバイス テンプレートが生成されます。
1. パブリック モデル リポジトリでモデルが見つからない場合、デバイスは **関連付けなし** としてマークされます。 オペレーターは、デバイスのデバイス テンプレートを作成し、関連付けられていないデバイスを新しいデバイス テンプレートに移行できます。

次のスクリーンショットは、IoT Central でデバイス テンプレートのモデル ID を表示する方法を示しています。 デバイス テンプレートでコンポーネントを選択し、 **[ID の表示]** を選択します。

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="サーモスタット デバイス テンプレート内のモデル ID を表示するスクリーンショット。":::

パブリック モデル リポジトリ内の[サーモスタット モデル](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json)は表示できます。 モデル ID の定義は次のようになります。

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>デバイスの状態の値

実際のデバイスが IoT Central アプリケーションに接続すると、そのデバイスの状態は次のように変わります。

1. デバイスの状態は、最初は **[登録済み]** です。 この状態は、デバイスが IoT Central 内で作成され、デバイス ID を持つことを意味します。 デバイスは、以下の場合に登録されます。
    - 1 台の新しい実際のデバイスが **[Devices]\(デバイス\)** ページで追加された。
    - 一連のデバイスが **[Devices]\(デバイス\)** ページで **[インポート]** を使用して追加された。

1. 有効な資格情報を使用して IoT Central アプリケーションに接続されているデバイスのプロビジョニング手順が完了すると、デバイスの状態が **[プロビジョニング済み]** に変わります。 この手順では、IoT Central アプリケーションで使用されている IoT ハブから、デバイスが DPS を使用して自動的に接続文字列を取得します。 これでデバイスは、IoT Central に接続し、データの送信を開始することができます。

1. オペレーターはデバイスをブロックできます。 デバイスがブロックされると、そのデバイスは IoT Central アプリケーションにデータを送信できません。 ブロックされたデバイスの状態は **[ブロック]** です。 オペレーターは、データの送信を再開する前にデバイスをリセットする必要があります。 オペレーターがデバイスのブロックを解除すると、状態は前の値 ( **[登録済み]** または **[プロビジョニング済み]** ) に戻ります。

1. デバイスの状態が **[承認を待っています]** の場合は、 **[自動承認]** オプションが無効になっていることを意味します。 デバイスがデータの送信を開始する前に、オペレーターが明示的にデバイスを承認する必要があります。 **[Devices]\(デバイス\)** ページで手動で登録されていないが、有効な資格情報を使って接続されているデバイスの場合、デバイスの状態が **[Waiting for Approval]\(承認待ち\)** になります。 オペレーターは、 **[Devices]\(デバイス\)** ページから **[承認]** ボタンを使用してこれらのデバイスを承認できます。

1. デバイスの状態が **[関連付けなし]** の場合は、IoT Central に接続しているデバイスにデバイス テンプレートが関連付けられていないことを意味します。 この状況は通常、次のシナリオで発生します。

    - デバイス テンプレートを指定せずに、 **[デバイス]** ページで **[インポート]** を使用して一連のデバイスが追加された。
    - デバイス テンプレートを指定せずに **[デバイス]** ページから手動でデバイスが登録された。 その後、有効な資格情報を使用してデバイスが接続された。  

    オペレーターは、 **[デバイス]** ページから **[移行]** ボタンを使用して、デバイス テンプレートにデバイスを関連付けることができます。

## <a name="sdk-support"></a>SDK のサポート

Azure Device SDK では、デバイス コードを最も簡単に実装する方法が提供されます。 次のデバイス SDK が使用できます。

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK の機能と IoT Hub の接続機能

IoT Hub を使用するすべてのデバイス通信では、次の IoT Hub 接続オプションを使用します。

- [デバイスからクラウドへのメッセージ](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [cloud-to-device メッセージング](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [デバイス ツイン](../../iot-hub/iot-hub-devguide-device-twins.md)

次の表では、Azure IoT Central デバイスの機能が IoT Hub の機能にどのように対応しているかをまとめたものです。

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| テレメトリ | デバイスからクラウドへのメッセージ |
| オフライン コマンド | クラウドからデバイスへのメッセージ |
| プロパティ | デバイス ツインの報告されるプロパティ |
| プロパティ (書き込み可能) | デバイス ツインの目的および報告されるプロパティ |
| コマンド | ダイレクト メソッド |

### <a name="protocols"></a>プロトコル

デバイス SDK は、次のネットワーク プロトコルを使った IoT Hub への接続をサポートしています。

- MQTT
- AMQP
- HTTPS

これらプロトコルの相違点や、プロトコルを選択する上でのガイダンスについては、「[通信プロトコルの選択](../../iot-hub/iot-hub-devguide-protocols.md)」を参照してください。

サポートされているプロトコルがどれもデバイスで使用できない場合は、Azure IoT Edge を使用して、プロトコルを変換します。 IoT Edge は、Azure IoT Central アプリケーションの処理負荷を軽減するための他のエッジ上のインテリジェンス シナリオをサポートします。

## <a name="security"></a>セキュリティ

デバイスと、Azure IoT Central 間で交換されるすべてのデータは暗号化されます。 IoT Hub は IoT Hub エンドポイントに接続するデバイスからのすべての要求を認証します。 有線上で資格情報を交換することを避けるため、デバイスでは、認証に署名入りトークンが使用されます。 詳細については、「[IoT Hub へのアクセスの制御](../../iot-hub/iot-hub-devguide-security.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

デバイス開発者にお勧めする次のステップは次のとおりです。

- デバイス開発の[ベスト プラクティス](concepts-best-practices.md)を確認します。
- 「[チュートリアル:クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」で、SAS トークンの使い方を示すサンプル コードを確認する
- [IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する](how-to-connect-devices-x509.md)方法を確認する
- [Azure CLI を使用してデバイスの接続性を監視する](./howto-monitor-devices-azure-cli.md)方法を確認する
- [Azure IoT Edge デバイスと Azure IoT Central](./concepts-iot-edge.md) について確認する
