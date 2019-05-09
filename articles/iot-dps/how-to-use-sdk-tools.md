---
title: Azure IoT Hub Device Provisioning サービス SDK に付属するツールを使用して開発を簡略化する
description: このドキュメントでは、開発用に Azure IoT Hub Device Provisioning サービス SDK に付属するツールを確認します。
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: dc8c29b1c7d4e5056cb6aeee6335e32687fd547f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123676"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>SDK に付属するツールを使用してプロビジョニング用の開発を簡略化する方法
IoT Hub Device Provisioning サービスは、セキュリティで保護されたスケーラブルな方法で、ゼロタッチの Just-In-Time [自動プロビジョニング](concepts-auto-provisioning.md)によってプロビジョニング プロセスを簡略化します。  X.509 証明書またはトラステッド プラットフォーム モジュール (TPM) の形式でのセキュリティの構成証明が必要です。  さらに Microsoft は[他のセキュリティ ハードウェア パートナー](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)と提携することで、IoT デプロイのセキュリティ保護における信頼性を高めています。 ハードウェアのセキュリティ要件を理解することは、開発者にとってきわめて困難な場合があります。 開発者がプロビジョニング サービスと通信するクライアントを作成するために便利なレイヤーを使用できるように、一連の Azure IoT Provisioning サービス SDK が提供されています。 この SDK では、一般的なシナリオのサンプルのほか、開発でのセキュリティ構成証明を簡略化するための一連のツールも提供しています。

## <a name="trusted-platform-module-tpm-simulator"></a>トラステッド プラットフォーム モジュール (TPM) シミュレーター
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) は、プラットフォームを認証するキーを安全に保管するための標準と言えます。または、標準を実装しているモジュールとの対話に使用される I/O インターフェイスと言うこともできます。 TPM は個別ハードウェア、統合ハードウェア、ファームウェアベース、またはソフトウェアベースとして存在する場合があります。  運用環境で、TPM は、個別のハードウェア、統合ハードウェア、またはファームウェア ベースのいずれかとしてデバイス上に配置されます。 テスト段階では、ソフトウェアベースの TPM シミュレーターが開発者に提供されます。  このシミュレーターは、現在のところ、Windows プラットフォームでの開発にのみ使用できます。

TPM シミュレーターを使用する手順は次のとおりです。
1. [開発環境を準備し](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java)、GitHub リポジトリを複製します。
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` の下の TPM シミュレーター フォルダーに移動します。
3. デバイスのプロビジョニングのためのクライアント アプリケーションを実行する前に Simulator.exe を実行します。
4. プロビジョニング プロセス全体でバック グラウンドでシミュレーターを実行させ、登録 ID と保証キーを取得します。  どちらの値も、実行の 1 つのインスタンスに対してのみ有効です。

## <a name="x509-certificate-generator"></a>X.509 証明書ジェネレーター
[X.509 証明書](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates)を構成証明メカニズムとして使用して、運用環境をスケーリングし、デバイスのプロビジョニングを簡略化できます。  X.509 証明書を取得するには[いくつかの方法](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate)があります。
* 運用環境の場合、公的なルート証明機関から X.509 CA 証明書を購入することをお勧めします。
* テスト環境の場合、次を使用して、X.509 ルート証明書または X.509 証明書チェーンを生成できます。
    * OpenSSL:証明書を生成するスクリプトを使用できます。
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell または Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Device Identity Composition Engine (DICE) エミュレーター:DICE は TLS プロトコルと X.509 クライアント証明書に基づいた暗号化デバイス ID と構成証明に使用できます。  [DICE とデバイス ID に関する詳細](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/)を参照してください。

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>DICE エミュレーターで X.509 証明書ジェネレーターを使用する
SDK は、DICE エミュレーターによる X.509 証明書ジェネレーターを提供しており、[Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator) にあります。  このジェネレーターはクロスプラットフォームで機能します。  生成された証明書は、他の言語での開発に使用できます。

現在、DICE エミュレーターは、ルート証明書、中間証明書、リーフ証明書、および関連付けられた秘密キーを出力します。  ただし、ルート証明書または中間証明書は、個別のリーフ証明書の署名に使用できません。  1 つの署名証明書を使用して、複数のデバイスのリーフ証明書を署名するグループ登録シナリオをテストする場合、OpenSSL を使用して、証明書のチェーンを生成できます。

このジェネレーターを使用して X.509 証明書を生成するには、次の手順を実行します。
1. [開発環境を準備し](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java)、GitHub リポジトリを複製します。
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. ルートを azure-iot-sdk-java に変更します。
3. ```mvn install -DskipTests=true``` を実行し、必要なすべてのパッケージをダウンロードし、SDK をコンパイルします
4. ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` の X.509 証明書ジェネレーターのルートに移動します。
5. ```mvn clean install``` でビルドします。
6. 次のコマンドを使用してツールを実行します。
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. プロンプトが表示されたら、必要に応じて証明書の _[Common Name]\(共通名\)_ を入力します。
8. このツールで、**Client Cert** (クライアント証明書)、**Client Cert Private Key** (クライアント証明書の秘密キー)、**Intermediate Cert** (中間証明書)、および **Root Cert** (ルート証明書) がローカルに生成されます。

**Client Cert** (クライアント証明書) は、デバイスのリーフ証明書です。  **Client Cert** (クライアント証明書) および関連付けられた**Client Cert Private Key** (クライアント証明書の秘密キー) はデバイス クライアントで必要です。 選択する言語に応じて、これをクライアント アプリケーションに配置するメカニズムが異なる場合があります。  詳細については、X.509 を使用したシミュレートされたデバイスの作成に関する[クイックスタート](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509)を参照してください。

ルート証明書または中間証明書は、登録グループまたは個々の登録を[プログラムで](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks)、または[ポータル](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)を使用して作成するために使用できます。

## <a name="next-steps"></a>次の手順
* Azure IoT Hub および Azure IoT Hub Device Provisioning Service 用に [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) を使用する開発
