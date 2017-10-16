---
title: "Azure IoT Hub Device Provisioning サービスにおけるセキュリティの概念 | Microsoft Docs"
description: "デバイス プロビジョニング サービスと IoT Hub を備えたデバイスに固有のセキュリティ プロビジョニングの概念を説明します。"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub デバイス プロビジョニング サービスのセキュリティの概念 

Azure IoT Hub Device Provisioning サービスは IoT Hub のヘルパー サービスです。このサービスは、指定された IoT Hub にプロビジョニングするゼロタッチ デバイスの構成に使用されます。 デバイス プロビジョニング サービスを利用すると、セキュリティで保護されたスケーラブルな方法でいくつものデバイスをプロビジョニングできます。 この記事では、デバイス プロビジョニングに関連付けられた*セキュリティ*の概念の概要を説明します。 この記事は、デプロイのために準備されたデバイスの取得に関わるすべてのユーザーに最も役立ちます。

## <a name="attestation-mechanism"></a>構成証明メカニズム

構成証明書メカニズムは、デバイスの ID の確認に使用される方法です。 また、構成証明メカニズムは、指定されたデバイスで使用する構成証明の方法をプロビジョニング サービスに伝えるための加入契約リストと関係があります。

> [!NOTE]
> IoT Hub は、そのサービスでの同様の概念として "認証スキーム" を使用します。

デバイス プロビジョニング サービスでは、2 つの形式の構成証明がサポートされます。
* 標準の X.509 証明書の認証フローに基づく**X.509 証明書**。
* キーの TPM 標準を使用する nonce チャレンジに基づく **SAS トークン**。 このトークンでは、デバイス上の物理 TPM は必須ではありませんが、[TPM 仕様](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)ごとに保証キーを使用して証明するために、サービスからは物理 TPM が期待されます。

## <a name="hardware-security-module"></a>ハードウェア セキュリティ モジュール

ハードウェア セキュリティ モジュール (HSM) は、デバイス シークレットの安全なハードウェア ベースのストレージに使用されます。これは、最も安全な形式のシークレット ストレージです。 X.509 証明書と SAS トークンの両方を HSM に格納できます。 HSM は、プロビジョニングがサポートする両方の構成証明メカニズムで使用できます。

> [!TIP]
> デバイス上に安全にシークレットを保管するために、デバイスに HSM を使用することを強くお勧めします。

デバイス シークレットもソフトウェア (メモリ) に保存できますが、HSM よりも安全性の低い形式のストレージです。

## <a name="trusted-platform-module-tpm"></a>トラステッド プラットフォーム モジュール (TPM)

TPM は、プラットフォームの認証に使用される安全に保管されたキーの標準を参照できます。または、標準を実装しているモジュールとの対話に使用される I/O インターフェイスを参照できます。 TPM は個別ハードウェア、統合ハードウェア、ファームウェアベース、またはソフトウェアベースとして存在する場合があります。 詳細については、[TPM の構成証明](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation)に関する記事をご覧ください。 デバイス プロビジョニング サービスでは、TPM 2.0 のみをサポートします。

## <a name="endorsement-key"></a>保証キー

保証キーとは、製造時に挿入された TPM 内に含まれる非対称キーであり、TPM ごとに一意です。 保証キーは、変更または削除できません。 保証キーの公開部分が正規 TPM の認識に使用されるのに対して、保証キーの秘密部分が TPM の外部に解放されることはありません。 詳細については、[保証キー](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx)に関する記事をご覧ください。

## <a name="storage-root-key"></a>ストレージ ルート キー

ストレージ ルート キーは TPM に格納され、アプリケーションで作成された TPM キーの保護に使用されます。TPM なしでは、これらのキーを使用できません。 ストレージ ルート キーは、TPM の所有権の取得時に生成されます。TPM をクリアすると、新しいユーザーが所有権を取得できるので、新しいストレージ ルート キーが生成されます。 詳細については、[ストレージ ルート キー](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)に関する記事をご覧ください。

## <a name="root-certificate"></a>ルート証明書

ルート証明書は、証明機関を表す X.509 証明書の種類であり、自己署名です。 証明書チェーンの終端になります。

## <a name="intermediate-certificate"></a>中間証明機関の証明書

中間証明機関の証明書は、ルート証明書 (または、証明書チェーン内のルート証明書を使用する別の証明書) によって署名され、リーフ証明書の署名に使用される X.509 証明書です。

## <a name="leaf-certificate"></a>リーフ証明書

リーフ証明書 (エンド エンティティ証明書) は証明書フォルダーの識別に使用され、証明書チェーンにあるルート証明書を保持します。 リーフ証明書は、他の証明書の署名には使用されません。
