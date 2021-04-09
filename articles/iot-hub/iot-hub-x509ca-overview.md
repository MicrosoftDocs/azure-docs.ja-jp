---
title: Azure IoT Hub X.509 CA セキュリティの概要 | Microsoft Docs
description: 概要 - X.509 証明機関を使用して IoT Hub に対してデバイスを認証する方法。
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92150698"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>X.509 CA 証明書を使用したデバイス認証

この記事では、X.509 証明機関 (CA) 証明書を使用して、IoT Hub 接続デバイスを認証する方法について説明します。  この記事では、次のことについて説明します。

* X.509 CA 証明書の入手方法
* X.509 CA 証明書を IoT Hub に登録する方法
* X.509 CA 証明書を使用してデバイスに署名する方法
* X.509 CA で署名されたデバイスを認証する方法

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>概要

X.509 CA 機能により、証明機関 (CA) を使用した IoT Hub に対するデバイス認証を行うことができます。 これにより、初回のデバイス登録プロセスと、デバイス製造時のサプライチェーン ロジスティクスが大幅に簡素化されます。 デバイス認証については、X.509 CA 使用の有用性に関する[このシナリオ記事](iot-hub-x509ca-concept.md)で詳細を参照してください。  このシナリオ記事では後続の手順が必要な理由が説明されているため、先に進む前にこの記事を読むことをお勧めします。

## <a name="prerequisite"></a>前提条件

X.509 CA 機能を使用するには、IoT Hub アカウントを持っている必要があります。  まだ持っていない場合は、[IoT Hub インスタンスの作成方法](quickstart-send-telemetry-dotnet.md)を参照してください。

## <a name="how-to-get-an-x509-ca-certificate"></a>X.509 CA 証明書の入手方法

X.509 CA 証明書は、各デバイスの証明書チェーンの最上位にあります。  証明書は、使用方法に応じて、購入または作成することができます。

運用環境の場合、公的なルート証明機関から X.509 CA 証明書を購入することをお勧めします。 CA 証明書を購入することで、ルート CA が、デバイスの正当性を保証する信頼されたサード パーティとして機能するという利点が得られます。 このオプションは、サード パーティの製品やサービスと対話することが予期されるオープン IoT ネットワークにデバイスを含める場合に検討してください。

閉じた IoT ネットワークでの実験または使用のためには、自己署名 X.509 CA を作成することもできます。

X.509 CA 証明書の入手方法に関係なく、必ずその対応する秘密キー シークレットを保存し、常に保護されている状態にしておいてください。  これは、X.509 CA 認証で信頼を構築するために必須です。

[自己署名 CA 証明書の作成](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)方法を参照してください。これは、この機能の説明全体で実験用にご利用いただけます。

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>デバイスに署名して証明書の信頼チェーンに入れる

X.509 CA 証明書の所有者は、中間 CA に暗号で署名できます。この CA は別の中間 CA に署名し、その CA はまた別の中間 CA に署名します。これは、最後の中間 CA がデバイスに署名することによってこのプロセスを終了するまで続きます。 この結果、証明書の信頼チェーンと呼ばれる、証明書の連鎖が生成されます。 現実的には、これは署名デバイスへと向かう信頼の委任として機能します。 この委任によって、暗号で変化する保管チェーンが確立され、署名キーの共有が回避されるため重要です。

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

デバイス証明書 (リーフ証明書とも呼ばれる) では、*サブジェクト名* が、Azure IoT Hub で IoT デバイスを登録するときに使用された **デバイス ID** (`CN=deviceId`) に設定されている必要があります。 この設定は認証に必要です。

デバイスの署名時に行う証明書チェーンの作成方法については、[ここ](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)を参照してください。

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>X.509 CA 証明書を IoT Hub に登録する方法

X.509 CA 証明書を IoT Hub に登録します。この証明書は、登録時および接続時にデバイスの認証に使用されます。  X.509 CA 証明書の登録は、証明書ファイルのアップロードと所有の証明からなる 2 段階のプロセスです。

アップロード プロセスでは、証明書を含むファイルのアップロードを行います。  このファイルには秘密キーが含まれていてはなりません。

所有の証明ステップには、ユーザーと IoT Hub 間で暗号のチャレンジと応答のプロセスが含まれます。  デジタル証明書の内容がパブリックであるために傍受されやすい場合、IoT Hub は、ユーザーが実際に CA 証明書を所有していることを確かめようとします。  IoT Hub はこれを、ランダム チャレンジを生成することによって行います。このチャレンジには、CA 証明書の対応する秘密キーで署名する必要があります。  前述のように秘密キー シークレットを保存して保護している場合、このステップを完了するための情報を持っているのは当該ユーザーのみです。 秘密キーの秘密性が、この方法における信頼の源です。  チャレンジに署名後、結果を含むファイルをアップロードすることによってこのステップを完了します。

[CA 証明書の登録](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)方法については、こちらをご覧ください。

## <a name="how-to-create-a-device-on-iot-hub"></a>IoT Hub 上にデバイスを作成する方法

デバイス偽装を防ぐために、IoT Hub は、どのようなデバイスが必要であるかを指定するようユーザーに要求します。  これは、IoT Hub のデバイス レジストリにデバイス エントリを作成することによって行います。  このプロセスは、IoT Hub [Device Provisioning Service](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) を使用している場合は自動化されます。 

手動で IoT Hub にデバイスを作成する方法については、[ここ](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)を参照してください。

IoT ハブの X.509 デバイスを作成する

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>X.509 CA 証明書で署名されたデバイスを認証する

X.509 CA 証明書を登録し、デバイスに署名して証明書の信頼チェーンに入れたので、後はデバイス接続時 (初回でも同様) にデバイス認証を行います。  X.509 CA 署名デバイスの接続時、デバイスは検証のためにその証明書チェーンをアップロードします。 チェーンには、中間 CA とデバイス証明書がすべて含まれています。  この情報を使用して、IoT Hub は 2 段階のプロセスでデバイスを認証します。  IoT Hub は、証明書チェーンの内部一貫性を暗号によって検証し、続いて所有証明チャレンジをデバイスに対して発行します。  IoT Hub は、デバイスから正常な所有証明応答を受け取ると、デバイスの認証を宣言します。  この宣言は、デバイスの秘密キーが保護されていること、またこのデバイスのみがこのチャレンジに対して正常に応答できることを前提としています。  秘密キーを保護するために、デバイスではハードウェア セキュア モジュール (HSM) のようなセキュアなチップの使用をお勧めします。

IoT Hub へのデバイス接続が正常に行われると、認証プロセスは完了です。これはまた、セットアップが正しいことも示しています。

このデバイス接続手順を完了する方法については、[ここ](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)を参照してください。

## <a name="next-steps"></a>次の手順

IoT での [X.509 CA 認証の価値](iot-hub-x509ca-concept.md)について学習します。

IoT Hub [Device Provisioning Service](../iot-dps/index.yml) を使ってみます。