---
title: Azure IoT Hub Device Provisioning Service - TPM の構成証明
description: この記事では、IoT Device Provisioning Service (DPS) を使用する TPM 構成証明フローの概念について概説します。
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 12860629d78391ed271306daba29a51aeb326c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531595"
---
# <a name="tpm-attestation"></a>TPM の構成証明

Azure IoT Hub Device Provisioning サービスは IoT Hub のヘルパー サービスです。このサービスは、指定された IoT Hub にプロビジョニングするゼロタッチ デバイスの構成に使用されます。 Device Provisioning Service を利用すると、セキュリティで保護された方法で多数のデバイスをプロビジョニングできます。

この記事では、トラステッド プラットフォーム モジュール (TPM) を使用した場合の ID 構成証明プロセスについて説明します。 TPM は、ハードウェア セキュリティ モジュール (HSM) の一種です。 この記事では、ディスクリート TPM、ファームウェア TPM、または統合された TPM を使用していると仮定します。 ソフトウェアでエミュレートされた TPM はプロトタイプの作成やテストには適していますが、ディスクリート TPM、ファームウェア TPM、または統合された TPM と同じレベルのセキュリティを提供することはできません。 実稼働環境でソフトウェア TPM を使用することはお勧めしません。 TPM の種類について詳しくは、[TPM の概要](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf)に関するページをご覧ください。

この記事は、HMAC キーをサポートする TPM 2.0 を使用したデバイスおよびその保証キーにのみ該当します。 認証に X.509 証明書を使用するデバイスは対象外です。 TPM は、業界で広く採用されている Trusted Computing Group の ISO 標準です。TPM の詳細については、[完全な TPM 2.0 仕様](https://trustedcomputinggroup.org/tpm-library-specification/)または [ISO/IEC 11889 仕様](https://www.iso.org/standard/66510.html)を参照してください。また、この記事は、公開キーおよび秘密キーのペアと、これらを使用して暗号化を行う方法について熟知していることを前提としています。

Device Provisioning Service のデバイス SDK は、この記事で説明されているすべてのことを自動的に処理します。 お使いのデバイスでこの SDK を使用している場合は、何も追加で実装する必要はありません。 この記事は、デバイスのプロビジョニング中に TPM セキュリティ チップで起こること、また、それが安全である理由を概念的に理解するうえで役立ちます。

## <a name="overview"></a>概要

TPM は、セキュリティで保護された信頼のルートとして保証キー (EK) と呼ばれるものを使用します。 EK は、TPM に対して一意であり、EK を変更するとは、実質的にデバイスを新しいものに変更することを意味します。

TPM には、ストレージ ルート キー (SRK) と呼ばれる別の種類のキーもあります。 SRK は、TPM の所有者が、TPM の所有権を取得した後に生成できます。 TPM の所有権を取得するとは、"だれかが HSM にパスワードを設定する" という TPM 固有の方法を意味します。 TPM デバイスが新しい所有者に販売されると、新しい所有者は TPM の所有権を取得して新しい SRK を生成できます。 新しい SRK を生成すると、以前の所有者は TPM を使用できなくなります。 SRK は TPM の所有者に一意であるため、SRK を使用してその所有者のデータを TPM 自体に封印できます。 SRK は、所有者がキーを格納するためのサンドボックスを提供し、デバイスまたは TPM が販売された場合にアクセス権を取り消すことができるようにします。 つまり、新居に引っ越すようなものです。所有権を取得するとドアの鍵が変更され、前の所有者 (SRK) によって残されたすべて家具が破壊されます、ただし、家 (EK) の住所を変更することはできません。

デバイスの設定が終了し、使用する準備が整うと、EK と SRK の両方が使用可能になります。

![TPM の所有権の取得](./media/concepts-tpm-attestation/tpm-ownership.png)

TPM の所有権の取得に関する注意事項: TPM の所有権の取得方法は、TPM の製造元、使用する一連の TPM ツール、デバイスの OS など、さまざまな要素によって異なります。 お使いのシステムに該当する手順に従って、所有権を取得してください。

Device Provisioning Service では、EK (EK_pub) の公開部分を使用して、デバイスの識別と登録を行います。 デバイスの製造元では、製造または最終テスト中に EK_pub を読み取ることができ、EK_pub をプロビジョニング サービスにアップロードして、プロビジョニングのために接続するときにデバイスが認識されるようにすることができます。 Device Provisioning Service は SRK や所有者をチェックしないため、TPM を "クリーニング" すると顧客データが消去されますが、EK (およびその他の製造元データ) は保持され、プロビジョニングのために接続するときに、Device Provisioning Service によって認識されます。

## <a name="detailed-attestation-process"></a>詳細な構成証明プロセス

TPM を装備したデバイスを Device Provisioning Service に初めて接続すると、このサービスは最初に、提供された EK_pub を、登録リストに格納されている EK_pub と照合します。 EK_pub が一致しない場合、デバイスのプロビジョニングは許可されません。 EK_pub が一致した場合、サービスは nonce チャレンジを介して EK の秘密部分の所有権を証明することをデバイスに要求します (nonce チャレンジ とは、ID を証明するために使用されるセキュリティで保護されたチャレンジのことです)。 Device Provisioning Service は nonce を生成し、SRK とその後に EK_pub を使用して暗号化します。これらは両方とも、初期登録呼び出し中に、デバイスから提供されます。 TPM は常に EK の秘密部分をセキュリティで保護します。 これによって偽造が防止され、SAS トークンが承認されたデバイスに安全にプロビジョニングされます。

構成証明プロセスを詳しく確認しましょう。

### <a name="device-requests-an-iot-hub-assignment"></a>デバイスが IoT Hub の割り当てを要求する

まず、デバイスから Device Provisioning Service に接続して、プロビジョニングを要求します。 デバイスはこのとき、サービスに対して登録 ID、ID スコープ、TPM の EK_pub および SRK_pub を提供します。 サービスは暗号化された nonce をデバイスに返します。また、nonce を復号化し、これを使用して SAS トークンに署名して再接続し、プロビジョニングを完了するようデバイスに要求します。

![デバイスがプロビジョニングを要求する](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>nonce チャレンジ

デバイスは nonce を受け取り、EK と SRK の秘密部分を使用して nonce を TPM に復号化します。nonce の暗号化の順序によって、EK (変更不可能) から SRK (新しい所有者が TPM の所有権を取得した場合に変更可能) へと信頼が委任されます。

![nonce の復号化](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>nonce を検証して資格情報を受け取る

デバイスは、復号化された nonce を使用して SAS トークンに署名し、この署名付きの SAS トークンを使用して Device Provisioning Service への接続を再確立できます。 Nonce チャレンジを完了すると、このサービスは、デバイスのプロビジョニングを許可します。

![デバイスが Device Provisioning Service との接続を再確立して EK の所有権を検証する](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>次のステップ

これで、デバイスが IoT Hub に接続され、デバイスのキーが安全に格納されていることがわかり安心できます。 Device Provisioning Service で TPM を使用してデバイスの ID を安全に検証する方法を確認しました。詳細について次の記事を参照してください。

* [プロビジョニングの概念を確認する](about-iot-dps.md#provisioning-process)
* SDK を使用して[自動プロビジョニングの作業を開始](./quick-setup-auto-provision.md)し、フローを処理する。
