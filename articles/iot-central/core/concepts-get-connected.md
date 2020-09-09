---
title: Azure IoT Central のデバイス接続機能 | Microsoft Docs
description: この記事では、Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 82d797189096994e02c77e9d342c00b13dfa187d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337094"
---
# <a name="get-connected-to-azure-iot-central"></a>Azure IoT Central に接続する

"*この記事は、オペレーターとデバイス開発者を対象としています。* "

この記事では、Azure IoT Central アプリケーションにデバイスを接続する場合のさまざまな選択肢について説明します。

通常、デバイスを接続するためには、あらかじめアプリケーションにデバイスを登録しておく必要があります。 ただし、IoT Central では、[デバイスを事前登録なしで接続する](#connect-without-registering-devices)シナリオがサポートされています。

IoT Central では、[Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) を使用して接続プロセスが管理されます。 デバイスは、アプリケーションに接続するうえで必要な情報を取得するために、まず DPS エンドポイントに接続します。 IoT Central アプリケーションは、内部的に IoT ハブを使用してデバイスの接続を処理します。 DPS を使用すると、次のことが可能になります。

- IoT Central で、大量のデバイスのオンボーディングと接続をサポートできます。
- デバイスの資格情報を生成し、デバイスをオフラインで構成できます。IoT Central UI を通じてデバイスを登録する必要はありません。
- 独自のデバイス ID を使用して、IoT Central にデバイスを登録できます。 独自のデバイス ID を使用して、既存のバックオフィス システムとの統合を簡略化できます。
- 1 つの一貫した方法で IoT Central にデバイスを接続できます。

デバイスとアプリケーションの間の通信をセキュリティで保護するために、IoT Central では、Shared Access Signature (SAS) と X.509 証明書の両方がサポートされています。 運用環境では、X.509 証明書はお勧めしません。

この記事では、次のユース ケースについて説明します。

- [SAS を使用して 1 台のデバイスを接続する](#connect-a-single-device)
- [SAS を使用して大量のデバイスを接続する](#connect-devices-at-scale-using-sas)
- [X.509 証明書を使用して大量のデバイスを接続する](#connect-devices-using-x509-certificates) - これは運用環境に対して推奨されるアプローチです。
- [デバイスを事前登録なしで接続する](#connect-without-registering-devices)
- [DPS 個別登録を使用するデバイスを接続する](#individual-enrollment-based-device-connectivity)
- [デバイスをデバイス テンプレートに自動的に関連付ける](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>1 つのデバイスを接続する

このアプローチは、IoT Central を使用した実験やデバイスのテストを行う場合に便利です。 IoT Central アプリケーションからのデバイス接続 SAS キーを使用して、IoT Central アプリケーションにデバイスを接続できます。 登録済みデバイスの接続情報から "_デバイス SAS キー_" をコピーします。

![個々のデバイスの SAS キー](./media/concepts-get-connected/single-device-sas.png)

詳細については、チュートリアル「[Node.js クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device-nodejs.md)」を参照してください。

## <a name="connect-devices-at-scale-using-sas"></a>SAS を使用して大量のデバイスを接続する

SAS キーを使用して大量のデバイスを IoT Central に接続するには、デバイスを登録してから設定する必要があります。

### <a name="register-devices-in-bulk"></a>デバイスの一括登録

IoT Central アプリケーションに大量のデバイスを登録するには、CSV ファイルを使用して[デバイス ID とデバイス名をインポート](howto-manage-devices.md#import-devices)します。

インポートしたデバイスの接続情報を取得するには、[IoT Central アプリケーションから CSV ファイルをエクスポート](howto-manage-devices.md#export-devices)します。 エクスポートされた CSV ファイルに、デバイス ID と SAS キーが含まれています。

### <a name="set-up-your-devices"></a>デバイスの設定

デバイス コード内のエクスポート ファイルからの接続情報を使用して、デバイスが IoT 経由で IoT Central アプリケーションに接続してデータを送信できるようにします。 また、アプリケーションに使用する DPS の **ID スコープ**も必要となります。 この値は、 **[管理] > [デバイス接続]** で確認できます。

> [!NOTE]
> 最初に IoT Central に登録しないでデバイスを接続する方法については、「[デバイスを登録しないで接続する](#connect-without-registering-devices)」をご覧ください。

## <a name="connect-devices-using-x509-certificates"></a>X.509 証明書を使用してデバイスを接続する

運用環境では、X.509 証明書を使用することが、IoT Central に対して推奨されるデバイス認証メカニズムです。 詳細については、「[X.509 CA 証明書を使用したデバイス認証](../../iot-hub/iot-hub-x509ca-overview.md)」をご覧ください。

X.509 証明書を使用してデバイスをアプリケーションに接続するには、次のようにします。

1. **証明書 (X.509)** 構成証明の種類を使用する*登録グループ*を作成します。
2. 登録グループで、中間またはルート X.509 証明書を追加して検証します。
3. 登録グループのルートまたは中間証明書から生成されたリーフ X.509 証明書を使用するデバイスを登録して接続します。

### <a name="create-an-enrollment-group"></a>登録グループを作成する

[登録グループ](../../iot-dps/concepts-service.md#enrollment)は、同じ構成証明の種類を共有するデバイスのグループです。 サポートされている構成証明の種類は、X.509 証明書と SAS の 2 つです。

- X.509 登録グループでは、IoT Central に接続するすべてのデバイスによって、登録グループのルートまたは中間証明書から生成されたリーフ X.509 証明書が使用されます。
- SAS 登録グループでは、IoT Central に接続するすべてのデバイスによって、登録グループの SAS トークンから生成された SAS トークンが使用されます。

すべての IoT Central アプリケーションの 2 つの既定の登録グループは、1 つは IoT デバイス用、もう 1 つは Azure IoT Edge デバイス用の、SAS 登録グループです。 X.509 登録グループを作成するには、 **[デバイスの接続]** ページに移動し、 **[+ Add enrollment group]\(+ 登録グループの追加\)** を選択します。

:::image type="content" source="media/concepts-get-connected/add-enrollment-group.png" alt-text="X.509 登録グループの追加のスクリーンショット":::

### <a name="add-and-verify-a-root-or-intermediate-x509-certificate"></a>ルートまたは中間 X.509 証明書を追加して検証する

登録グループにルートまたは中間証明書を追加して検証するには、次のようにします。

1. 先ほど作成した X.509 登録グループに移動します。 プライマリとセカンダリの両方の X.509 証明書を追加することもできます。 **[+ Manage primary]\(プライマリの管理\)** を選択します。

1. **[プライマリ証明書] ページ**で、プライマリの X.509 証明書をアップロードします。 これは、ルートまたは中間証明書です。

    :::image type="content" source="media/concepts-get-connected/upload-primary-certificate.png" alt-text="プライマリ証明書のスクリーンショット":::

1. **確認コード**を使用して、使用しているツールで確認コードを生成します。 次に、 **[検証]** を選択して検証証明書をアップロードします。

1. 検証が成功すると、次の確認メッセージが表示されます。

    :::image type="content" source="media/concepts-get-connected/verified-primary-certificate.png" alt-text="検証済みのプライマリ証明書のスクリーンショット":::

証明書の所有権を検証して、証明書のアップロード担当者が証明書の秘密キーを持っていることを確認します。

セキュリティ侵害があるか、プライマリ証明書の期限が設定されている場合は、セカンダリ証明書を使用してダウンタイムを短縮します。 プライマリ証明書の更新中に、セカンダリ証明書を使用してデバイスのプロビジョニングに進むことができます。

### <a name="register-and-connect-devices"></a>デバイスを登録して接続する

X.509 証明書を使用してデバイスを一括接続するには、まず CSV ファイルを使用してデバイスをアプリケーションに登録し、[デバイス ID とデバイス名をインポート](howto-manage-devices.md#import-devices)します。 デバイス ID はすべて小文字である必要があります。

X.509 登録グループにアップロードしたルートまたは中間証明書を使用して、デバイスの X.509 リーフ証明書を生成します。 **デバイス ID** をリーフ証明書の `CNAME` 値として使用します。 デバイス コードには、アプリケーションの **ID スコープ**値と**デバイス ID**、および対応するデバイス証明書が必要です。

#### <a name="sample-device-code"></a>サンプル デバイス コード

[Azure IoT NODE.JS SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) の次のサンプルは、Node.js デバイス クライアントで x.509 リーフ証明書と DPS を使用して IoT Central アプリケーションに登録する方法を示しています。

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

同等の C サンプルについては、[Azure IoT C Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) の [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) を参照してください。

### <a name="for-testing-purposes-only"></a>テスト目的のみ

テストの場合にのみ、次のユーティリティを使用して、ルート証明書、中間証明書、およびデバイス証明書を生成できます。

- [Azure IoT Device Provisioning Device SDK 用ツール](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): X.509 の証明書とキーの生成および検証に使用できる一連の Node.js ツールです。
- DevKit デバイスを使用している場合、この[コマンドライン ツール](https://aka.ms/iotcentral-docs-dicetool)では、証明書を検証するために IoT Central アプリケーションに追加できる CA 証明書が生成されます。
- [サンプルとチュートリアルに使用するテスト CA 証明書の管理](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): 次の作業を目的とした、一連の PowerShell スクリプトと Bash スクリプトです。
  - 証明書チェーンを作成します。
  - IoT Central アプリケーションにアップロードする .cer ファイルとして証明書を保存します。
  - IoT Central アプリケーションからの確認コードを使用して検証証明書を生成します。
  - デバイス ID をツールへのパラメーターとして使用して、デバイスのリーフ証明書を作成します。

## <a name="connect-without-registering-devices"></a>デバイスを登録しないで接続する

これまでに説明したどのシナリオでも、接続するデバイスをあらかじめアプリケーションに登録しておく必要があります。 しかし IoT Central には、事前登録なしで接続する手段も用意されており、そのようなデバイスを OEM は大量に製造することができます。 OEM が適切なデバイス資格情報を生成し、デバイスを工場で構成することになります。 顧客が初めてデバイスの電源を投入したとき、デバイスはまず DPS に接続し、その後 DPS によって自動的に適切な IoT Central アプリケーションに接続されます。 デバイスからアプリケーションへのデータの送信が開始される前に、IoT Central のオペレーターがデバイスを承認しておく必要があります。

そのフローは、デバイスで使用されているのが SAS トークンであるか X.509 証明書であるかによって若干異なります。

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>SAS トークンを使用するデバイスを登録なしで接続する

1. **SAS-IoT-Devices** 登録グループからグループ プライマリ キーをコピーします。

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="SAS-IoT-Devices 登録グループのグループ プライマリ キー":::

1. [dps-keygen](https://www.npmjs.com/package/dps-keygen) ツールを使用してデバイスの SAS キーを生成します。 前の手順で得たグループ プライマリ キーを使用します。 デバイス ID は小文字にする必要があります。

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. デバイス ID、生成されたデバイス SAS キー、アプリケーションの **ID スコープ**の値を使用して、OEM が各デバイスをフラッシュします。

1. デバイスの電源をオンにすると、そのデバイスはまず DPS に接続して、IoT Central の登録情報を取得します。

    デバイスは、その初期状態が **[関連付けなし]** として **[デバイス]** ページに表示され、デバイス テンプレートには割り当てられていません。 **[デバイス]** ページで、適切なデバイス テンプレートにデバイスを**移行**してください。 デバイスのプロビジョニングはこれで完了です。デバイスは **[プロビジョニング済み]** 状態となり、データの送信を開始することができます。

    デバイスからデータの送信が開始される前にデバイスを手動で承認しておく必要があるかどうかは、 **[管理] > [デバイス接続]** ページの **[自動承認]** オプションで制御されます。

    > [!NOTE]
    > デバイス テンプレートにデバイスを自動的に関連付ける方法については、「[デバイスをデバイス テンプレートに自動的に関連付ける](#automatically-associate-with-a-device-template)」を参照してください。

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>X.509 証明書を使用するデバイスを登録なしで接続する

1. [登録グループを作成](#create-an-enrollment-group)し、IoT Central アプリケーションに[ルートまたは中間 X.509 証明書を追加して検証](#add-and-verify-a-root-or-intermediate-x509-certificate)します。

1. IoT Central アプリケーションに追加したルートまたは中間証明書を使用して、デバイスのリーフ証明書を生成します。 小文字のデバイス ID をリーフ証明書の `CNAME` として使用してください。

1. OEM では、デバイス ID、生成されたリーフ X.509 証明書、アプリケーションの **ID スコープ**の値を使用して、各デバイスがフラッシュされます。

1. デバイスの電源をオンにすると、そのデバイスはまず DPS に接続して、IoT Central の登録情報を取得します。

    デバイスは、その初期状態が **[関連付けなし]** として **[デバイス]** ページに表示され、デバイス テンプレートには割り当てられていません。 **[デバイス]** ページで、適切なデバイス テンプレートにデバイスを**移行**してください。 デバイスのプロビジョニングはこれで完了です。デバイスは **[プロビジョニング済み]** 状態となり、データの送信を開始することができます。

    デバイスからデータの送信が開始される前にデバイスを手動で承認しておく必要があるかどうかは、 **[管理] > [デバイス接続]** ページの **[自動承認]** オプションで制御されます。

    > [!NOTE]
    > デバイス テンプレートにデバイスを自動的に関連付ける方法については、「[デバイスをデバイス テンプレートに自動的に関連付ける](#automatically-associate-with-a-device-template)」を参照してください。

## <a name="individual-enrollment-based-device-connectivity"></a>個別加入ベースのデバイス接続

それぞれ独自の認証資格情報を持つデバイスを接続する場合は、個別登録を使用します。 個別登録は、接続を許可された 1 台のデバイスを対象とする登録方法です。 個別登録では、構成証明メカニズムとして X.509 リーフ証明書または (実際のトラステッド プラットフォーム モジュールまたは仮想トラステッド プラットフォーム モジュールの) SAS トークンを使用できます。 個別加入のデバイス ID (登録 ID とも呼ばれる) は、小文字の英数字で、ハイフンを含めることができます。 詳細については、[DPS の個別登録](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)に関するセクションを参照してください。

> [!NOTE]
> デバイスの個別登録を作成すると、既定のグループ登録オプションよりもそちらが IoT Central アプリケーションで優先されます。

### <a name="create-individual-enrollments"></a>個々の登録を作成する

IoT Central では、個別登録に関して次の構成証明メカニズムがサポートされます。

- **対称キーの構成証明:** 対称キーの構成証明は、DPS インスタンスを使用してデバイスを認証するシンプルなアプローチです。 対称キーを使用する個別登録を作成するには、 **[デバイス接続]** ページを開き、接続方法として **[個別の登録]** を、メカニズムとして **[Shared Access Signature (SAS)]** を選択します。 Base64 エンコードされたプライマリ キーとセカンダリ キーを入力し、変更を保存します。 **ID スコープ**、**デバイス ID**、キー (プライマリまたはセカンダリ) を使用してデバイスを接続します。

    > [!TIP]
    > テストでは、**OpenSSL** を使用して Base64 エンコードされたキーを生成できます (`openssl rand -base64 64`)。

- **X.509 証明書:** X.509 証明書を使用する個別登録を作成するには、 **[デバイス接続]** ページを開き、接続方法として **[個別の登録]** を、メカニズムとして **[証明書 (X.509)]** を選択します。 個別登録エントリで使用するデバイス証明書には、発行者とサブジェクト CN をデバイス ID に設定しなければならないという要件があります。

    > [!TIP]
    > テストでは、[Azure IoT Device Provisioning Device SDK for Node.js 用ツール](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)を使用して自己署名証明書を生成できます (`node create_test_cert.js device "mytestdevice"`)。

- **トラステッド プラットフォーム モジュール (TPM) の構成証明:** [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) は、ハードウェア セキュリティ モジュールの一種です。 TPM を使用することが、デバイスを接続する最も安全な方法の 1 つとなっています。 この記事では、ディスクリート TPM、ファームウェア TPM、または統合された TPM を使用していると仮定します。 ソフトウェアでエミュレートされた TPM はプロトタイプの作成やテストには適していますが、ディスクリート TPM、ファームウェア TPM、または統合された TPM と同じレベルのセキュリティを提供することはできません。 運用環境ではソフトウェア TPM を使用しないでください。 TPM を使用する個別登録を作成するには、 **[デバイス接続]** ページを開き、接続方法として **[個別の登録]** を、メカニズムとして **[TPM]** を選択します。 TPM 保証キーを入力し、デバイスの接続情報を保存します。

## <a name="automatically-associate-with-a-device-template"></a>デバイス テンプレートに自動的に関連付ける

IoT Central の主な機能の 1 つとして、デバイスの接続時にデバイス テンプレートを自動的に関連付ける機能があります。 デバイスでは、デバイスの資格情報と共に、デバイス登録呼び出しの一部として **CapabilityModelId** を送信できます。 **CapabilityModelID** は、デバイスで実装する機能モデルを識別する URN です。 IoT Central アプリケーションでは、**CapabilityModelID** を使用して、使用するデバイス テンプレートを特定し、デバイスをデバイス テンプレートに自動的に関連付けることができます。 検出プロセスは次のように実行されます。

1. IoT Central アプリケーションでデバイス テンプレートが既に発行されている場合、デバイスはデバイス テンプレートに関連付けられます。
1. 事前認定された IoT プラグ アンド プレイ デバイスでは、IoT Central アプリケーションでデバイス テンプレートがまだ発行されていない場合、デバイス テンプレートはパブリック リポジトリからフェッチされます。

次のスニペットは、自動的な関連付けが機能するために、DPS 登録呼び出し中にデバイスで送信する必要がある追加のペイロードの形式を示しています。

一般提供デバイス SDK を使用するデバイスの形式は、次のとおりです。

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

パブリック プレビュー デバイス SDK を使用するデバイスの形式は、次のとおりです。

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> デバイスで自動的に接続し、デバイス テンプレートを検出して、データの送信を開始するには、 **[管理] > [デバイス接続]** の **[自動承認]** オプションが有効になっている必要があります。

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

## <a name="best-practices"></a>ベスト プラクティス

デバイスの初回接続時に DPS から返されるデバイスの接続文字列を永続化したりキャッシュしたりすることは避けてください。 デバイスを再接続する際は、標準的なデバイス登録フローを通じて、適切なデバイス接続文字列を取得するようにします。 接続文字列をキャッシュすると、IoT Central で使用されている基になる Azure IoT Hub が更新された場合に、デバイスのソフトウェアに古い接続文字列が残ってしまうおそれがあります。

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
- [デバイス ツイン](../../iot-hub/iot-hub-devguide-device-twins.md)

次の表では、Azure IoT Central デバイスの機能が IoT Hub の機能にどのように対応しているかをまとめたものです。

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| テレメトリ | デバイスからクラウドへのメッセージ |
| プロパティ | デバイス ツインの報告されるプロパティ |
| プロパティ (書き込み可能) | デバイス ツインの目的および報告されるプロパティ |
| コマンド | ダイレクト メソッド |

### <a name="protocols"></a>プロトコル

デバイス SDK は、次のネットワーク プロトコルを使った IoT Hub への接続をサポートしています。

- MQTT
- AMQP
- HTTPS

これらプロトコルの相違点や、プロトコルを選択する上でのガイダンスについては、「[通信プロトコルの選択](../../iot-hub/iot-hub-devguide-protocols.md)」を参照してください。

デバイスでサポートされているプロトコルのいずれも使用できない場合は、Azure IoT Edge を使用して、プロトコルを変換します。 IoT Edge は、Azure IoT Central アプリケーションの処理負荷を軽減するための他のエッジ上のインテリジェンス シナリオをサポートします。

## <a name="security"></a>セキュリティ

デバイスと、Azure IoT Central 間で交換されるすべてのデータは暗号化されます。 IoT Hub は IoT Hub エンドポイントに接続するデバイスからのすべての要求を認証します。 有線上で資格情報を交換することを避けるため、デバイスでは、認証に署名入りトークンが使用されます。 詳細については、「[IoT Hub へのアクセスの制御](../../iot-hub/iot-hub-devguide-security.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

デバイス開発者にお勧めする次のステップは次のとおりです。

- [Azure CLI を使用してデバイスの接続性を監視する](./howto-monitor-devices-azure-cli.md)方法を確認する
- [Azure IoT Central アプリケーションで新しい IoT デバイスの種類を定義する](./howto-set-up-template.md)方法を確認する
- [Azure IoT Edge デバイスと Azure IoT Central](./concepts-iot-edge.md) について確認する
