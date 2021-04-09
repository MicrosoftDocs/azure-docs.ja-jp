---
title: Azure IoT Edge モジュールとしての機密性の高いアプリケーション
description: 機密性の高いアプリケーションを記述し、コンフィデンシャル コンピューティングのために IoT Edge モジュールとしてそれらをデプロイするには、Open Enclave SDK と API を使用します
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: f9dff1b4c6b2489edd3cd685e3546618961d9757
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487719"
---
# <a name="confidential-computing-at-the-edge"></a>エッジでのコンフィデンシャル コンピューティング

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge では、デバイス上のセキュリティで保護されたエンクレーブ内で実行される機密性の高いアプリケーションがサポートされています。 暗号化により転送中または保存時のデータのセキュリティが提供されるのに対し、エンクレーブでは使用中のデータとワークロードのセキュリティが提供されます。 IoT Edge では、機密性の高いアプリケーションの開発のために、標準として Open Enclave がサポートされています。

多くの場合、モノのインターネット (IoT) デバイスはセキュリティで保護されたプライベートな施設内ではなく外の世界に存在するため、セキュリティは常に IoT にとっての重要な焦点です。 この露出により、悪意のあるアクターが物理的にアクセスできるため、デバイスは改ざんや偽造のリスクを負うことになります。 IoT Edge デバイスの場合、機密性の高いワークロードのエッジでの実行に対応するため、信頼性と整合性がさらに必要になります。 一般的なセンサーやアクチュエータとは異なり、これらのインテリジェントなエッジ デバイスでは、以前であれば保護されたクラウドやオンプレミス環境でのみ実行されていた機密性の高いワークロードが、公開される可能性があります。

[IoT Edge セキュリティ マネージャー](iot-edge-security-manager.md)により、機密性の高いコンピューティングの課題の 1 つの部分が対処されます。 セキュリティ マネージャーでは、ハードウェア セキュリティ モジュール (HSM) を使用して、IoT Edge デバイスの ID ワークロードと進行中のプロセスが保護されます。

機密性の高いコンピューティングのもう 1 つの部分は、エッジで使用されるデータを保護することです。 "*信頼できる実行環境*" (TEE) は、プロセッサ上のセキュリティ保護されて分離された環境であり、"*エンクレーブ*" と呼ばれることもあります。 "*機密性の高いアプリケーション*" は、エンクレーブ内で実行されるアプリケーションです。 エンクレーブの性質により、機密性の高いアプリケーションは、メイン プロセッサまたは TEE で実行されている他のアプリから保護されます。

## <a name="confidential-applications-on-iot-edge"></a>IoT Edge 上の機密性の高いアプリケーション

機密性の高いアプリケーションは、転送時および保存時に暗号化され、信頼できる実行環境の内部で実行される場合にのみ暗号化解除されます。 この標準は、IoT Edge モジュールとしてデプロイされる機密性の高いアプリケーションにも当てはまります。

開発者は、機密性の高いアプリケーションを作成し、IoT Edge モジュールとしてそれをパッケージ化します。 アプリケーションは、コンテナー レジストリにプッシュされる前に暗号化されます。 アプリケーションは、モジュールが IoT Edge デバイスで開始されるまで、IoT Edge 展開プロセス全体を通して暗号化されたままになります。 機密性の高いアプリケーションがデバイスの TEE 内に配置された後、暗号化が解除され、実行を開始できるようになります。

![図 - 機密性の高いアプリケーションは、セキュリティで保護されたエンクレーブに展開されるまで、IoT Edge モジュール内で暗号化されている](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

IoT Edge 上の機密性の高いアプリケーションは、[Azure コンフィデンシャル コンピューティング](../confidential-computing/overview.md)の論理的な拡張機能です。 クラウドのセキュリティで保護されたエンクレーブ内で実行されるワークロードを、エッジのセキュリティで保護されたエンクレーブ内に展開して実行することもできます。

## <a name="open-enclave"></a>Open Enclave

[Open Enclave SDK](https://openenclave.io/sdk/) は、開発者が複数のプラットフォームと環境を対象として機密性の高いアプリケーションを作成するのに役立つオープン ソース プロジェクトです。 Open Enclave SDK がデバイスの信頼できる実行環境内で動作するのに対し、Open Enclave API は、TEE と TEE 以外の処理環境間のインターフェイスとして機能します。

Open Enclave では、複数のハードウェア プラットフォームがサポートされています。 現在、エンクレーブを IoT Edge でサポートするには、Open Portable TEE オペレーティング システム (OP-TEE OS) が必要です。 詳細については、「[OP-TEE OS 用の Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md)」を参照してください。

Open Enclave リポジトリには、開発者が作業を始めるときに役立つサンプルも含まれています。 詳細については、次のいずれかの記事を参照してください。

* [Linux で Open Enclave SDK サンプルを作成する](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Windows で Open Enclave SDK サンプルを作成する](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>ハードウェア

現時点では、機密性の高いアプリケーションを IoT Edge モジュールとしてデプロイするための製造元サービス契約でサポートされているデバイスは、[Scalys による TrustBox](https://scalys.com/trustbox-industrial/) のみです。 TrustBox は TrustBox Edge および TrustBox EdgeXL デバイス上に構築されており、どちらにも Open Enclave SDK と Azure IoT Edge があらかじめ読み込まれています。

詳細については、[Scalys TrustBox 用の Open Enclave の概要](https://aka.ms/scalys-trustbox-edge-get-started)に関するページを参照してください。

## <a name="develop-and-deploy"></a>開発とデプロイ

機密性の高いアプリケーションを開発してデプロイする準備ができたら、Visual Studio Code 用の [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) 拡張機能が役に立つ場合があります。 Linux または Windows を開発用マシンとして使用して、TrustBox 用のモジュールを開発できます。

## <a name="next-steps"></a>次の手順

[Visual Studio Code 用 Open Enclave 拡張機能](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)を使用して IoT Edge モジュールとして機密性の高いアプリケーションの開発を始める方法を学習する
