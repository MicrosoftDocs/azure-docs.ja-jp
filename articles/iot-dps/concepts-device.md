---
title: Azure デバイス プロビジョニングにおけるデバイスの概念 | Microsoft Docs
description: Device Provisioning Service（DPS）および IoT Hub を備えたデバイスに固有のデバイス プロビジョニングの概念を説明します
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975314"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub デバイス プロビジョニング サービスのデバイスの概念

Azure IoT Hub Device Provisioning サービスは IoT Hub のヘルパー サービスです。このサービスは、指定された IoT Hub にプロビジョニングするゼロタッチ デバイスの構成に使用されます。 デバイス プロビジョニング サービスを利用すると、セキュリティで保護されたスケーラブルな方法でいくつものデバイスをプロビジョニングできます。

この記事では、デバイス プロビジョニングに関連する*デバイス*の概念の概要を説明します。 この記事は、デプロイのために準備されたデバイスを取得する[製造手順](about-iot-dps.md#manufacturing-step)に関わるユーザーに最も役立ちます。

## <a name="attestation-mechanism"></a>構成証明メカニズム

構成証明書メカニズムは、デバイスの ID の確認に使用される方法です。 また、構成証明メカニズムは、指定されたデバイスで使用する構成証明の方法をプロビジョニング サービスに伝えるための加入契約リストと関係があります。

> [!NOTE]
> IoT Hub は、そのサービスでの同様の概念として "認証スキーム" を使用します。

デバイス プロビジョニング サービスは、以下の形式の構成証明をサポートします。
* 標準の X.509 証明書の認証フローに基づく**X.509 証明書**。
* nonce チャレンジに基づく**トラステッド プラットフォーム モジュール (TPM)** 。キーの TPM 標準を使用し、署名された Shared Access Signature (SAS) トークンを提示します。 このトークンでは、デバイス上の物理 TPM は必須ではありませんが、[TPM 仕様](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)ごとに保証キーを使用して証明するために、サービスからは物理 TPM が期待されます。
* 共有アクセス署名 (SAS) **セキュリティ トークン**に基づく[対称キー](../iot-hub/iot-hub-devguide-security.md#security-tokens)。これにはハッシュの署名と埋め込みの有効期限が含まれています。 詳細については、「[対称キーの構成証明](concepts-symmetric-key-attestation.md)」を参照してください。

## <a name="hardware-security-module"></a>ハードウェア セキュリティ モジュール

ハードウェア セキュリティ モジュール (HSM) は、デバイス シークレットの安全なハードウェア ベースのストレージに使用されます。これは、最も安全な形式のシークレット ストレージです。 X.509 証明書と SAS トークンの両方を HSM に格納できます。 HSM は、プロビジョニング サービスがサポートする両方の構成証明メカニズムで使用できます。

> [!TIP]
> デバイス上に安全にシークレットを保管するために、デバイスに HSM を使用することを強くお勧めします。

デバイス シークレットもソフトウェア (メモリ) に保存できますが、HSM よりも安全性の低い形式のストレージです。

## <a name="registration-id"></a>登録 ID

登録 ID は、デバイス プロビジョニング サービスでデバイスを一意に識別するために使用されます。 デバイス ID は、プロビジョニング サービスの[ ID スコープ](#id-scope)内で一意である必要があります。 各デバイスに登録 ID が必要です。 登録 ID は大文字と小文字が区別されない英数字であり、コロン、ピリオド、アンダースコア、ハイフンなどの特殊文字を含めることができます。

* TPM の場合、登録 ID は TPM 自身によって提供されます。
* X.509 ベースの構成証明の場合、登録 ID は、証明書のサブジェクト名として提供されます。

## <a name="device-id"></a>Device ID

デバイス ID は、IoT Hub に表示される ID です。 加入契約エントリに目的のデバイス ID を設定できますが、設定は必須ではありません。 希望するデバイス ID の設定は、個別の登録でのみサポートされています。 加入契約リストに目的のデバイス ID が指定されていない場合、デバイスの登録時の登録 ID がデバイス ID として使用されます。 詳細については、[IoT Hub のデバイス ID](../iot-hub/iot-hub-devguide-identity-registry.md) に関する記事をご覧ください。

## <a name="id-scope"></a>ID スコープ

ID スコープは、ユーザーによって作成されたときに、デバイス プロビジョニング サービスに割り当てられます。デバイスの登録に利用される特定のプロビジョニング サービスを一意に識別するために使用されます。 サービスによって ID スコープが生成され、この ID スコープが不変であるため、一意性が保証されます。

> [!NOTE]
> 実行時間の長いデプロイ操作や統合と取得のシナリオでは、一意性が重要です。
