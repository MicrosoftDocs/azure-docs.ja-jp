---
title: Azure IoT OPC UA 証明書管理を使用した OPC UA クライアントと OPC UA サーバー アプリケーションの保護 | Microsoft Docs
description: OPC Vault を使用する新しいキー ペアと証明書により、OPC UA クライアントと OPC UA サーバー アプリケーションを保護します。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bfa6bdf6a54cb5e54087055988e9682565667105
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759226"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>OPC UA クライアントと OPC UA サーバー アプリケーションを保護する 
Azure IoT OPC UA 証明書管理 (OPC Vault とも呼ばれます) は、OPC UA サーバーとクライアント アプリケーションに使用される証明書のライフサイクルの構成、登録、管理をクラウドで行うマイクロサービスです。 この記事では、OPC Vault を使用する新しいキー ペアと証明書で OPC UA クライアントと OPC UA サーバー アプリケーションを保護する方法について説明します。

次のセットアップでは、OPC クライアントは OPC PLC への接続をテストします。 既定では、両方のコンポーネントが適切な証明書を使用してプロビジョニングされていないため、接続は不可能となっています。 このワークフローでは、OPC UA コンポーネントの自己署名証明書は使用せず、OPC Vault を介して署名します。 前の「[テスト環境](howto-opc-vault-deploy-existing-client-plc-communication.md)」を参照してください。 代わりに、このテスト環境では、新しい証明書に加えて、新しい秘密キーでコンポーネントをプロビジョニングします。これらは、両方とも OPC Vault によって生成されます。 OPC UA セキュリティに関する背景情報の一部が、こちらの[ホワイトペーパー](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf)に記載されています。 完全な情報は、OPC UA の仕様書に記載されています。

テスト環境:テストのために次の環境が構成されています。

OPC Vault スクリプト:
- OPC Vault を使用する新しいキー ペアと証明書により、OPC UA クライアントと OPC UA サーバー アプリケーションを保護します。

> [!NOTE]
> 詳細については、GitHub [リポジトリ](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)を参照してください。

## <a name="generate-a-new-certificate-and-private-key"></a>新しい証明書と秘密キーを生成する 
**準備**
- 環境変数 `$env:_PLC_OPT` および `$env:_CLIENT_OPT` を未定義にします。 たとえば、PowerShell で `$env:_PLC_OPT=""` と記述します。
- 環境変数 `$env:_OPCVAULTID` を、OPC Vault で再度データを見つけることができる文字列に設定します。 6 桁の数字に設定することをお勧めします。 この例では、この変数の値として "123456" が使用されています。
- Docker ボリュームの `opcclient` や `opcplc` がないようにします。 `docker volume ls` で調べて、`docker volume rm <volumename>` でそれらを削除します。 コンテナーによってまだボリュームが使用されている場合は、`docker rm <containerid>` でコンテナーも削除する必要が生じることがあります。

**クイックスタート**
1. [OPC Vault の Web サイト](https://opcvault.azurewebsites.net/)に移動します。

1. `Register New` を選択します

1. 前のテスト環境のログ出力の `CreateSigningRequest information` 領域に示された OPC PLC の情報を `Register New OPC UA Application` ページの入力フィールドに入力し、ApplicationType として `Server` を選択します。

1. `Register` を選択します

1. 次のページ `Request New Certificate for OPC UA Application` で `Request new KeyPair and Certificate` を選択します。

1. 次のページ `Generate a new Certificate with a Signing Request` で、ログ出力からの `CSR (base64 encoded)` 文字列を `CreateRequest` 入力フィールドに貼り付けます。 文字列全体をコピーしたことを確認します。

1. 次のページ `Request New Certificate for OPC UA Application` で `Request new Certificate with Signing Request` を選択します。

1. 次のページ `Generate a new KeyPair and for an OPC UA Application` で、SubjectName として `CN=OpcPlc` を入力し、DomainName として `opcplc-<_OPCVAULTID>` を入力し (`<_OPCVAULTID>` をご使用の ID と置き換えてください)、PrivateKeyFormat として `PEM` を選択し、パスワード (後で `<certpassword-string>` として参照します) を入力します。

1. `Generate New KeyPair` を選択します

1. 次に `View Certificate Request Details` に進みます。 このページでは、`opc-plc` の証明書ストアをプロビジョニングするために必要なすべての情報をダウンロードできます。

1. このページでの手順:  
    - `Download as Base64` で `Certificate` を選択し、`EncodedBase64` フィールドに表示されるテキスト文字列をコピーしたら、後で使用するために保存します。 後で `<applicationcertbase64-string>` として参照します。 [`Back`] を選択します。
    - `Download as Base64` で `PrivateKey` を選択し、`EncodedBase64` フィールドに表示されるテキスト文字列をコピーしたら、後で使用するために保存します。 後で `<privatekeybase64-string>` として参照します。 [`Back`] を選択します。
    - `Download as Base64` で `Issuer` を選択し、`EncodedBase64` フィールドに表示されるテキスト文字列をコピーしたら、後で使用するために保存します。 後で `<addissuercertbase64-string>` として参照します。 [`Back`] を選択します。
    - `Download as Base64` で `Crl` を選択し、`EncodedBase64` フィールドに表示されるテキスト文字列をコピーしたら、後で使用するために保存します。 後で `<updatecrlbase64-string>` として参照します。 [`Back`] を選択します。

1. ここで、PowerShell で `$env:_PLC_OPT` という名前の変数を設定します。

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Web サイトからフェッチしたオプション値の Base64 文字列として渡される文字列を置換します。  

1. `Register New` で始まるプロセス全体を、OPC クライアントに対して繰り返します。 注意する必要がある相違点は以下のみです。
    - `opcclient` からのログ出力を使用します。
    - 登録時の ApplicationType として `Client` を選択します。
    - PowerShell 変数の名前として `$env:_CLIENT_OPT` を使用します。

    > [!NOTE] 
    > このシナリオの作業中に、`opcplc` と `opcclient` について、`<addissuercertbase64-string>` と `<updatecrlbase64-string>` の値が同一であると気付いたかもしれません。 今回のユース ケースの場合はその通りで、手順の実行中に少し時間の節約になります。

**クイックスタート**

リポジトリのルートで次の PowerShell コマンドを実行します。

```
docker-compose -f connecttest.yml up
```

**確認**

2 つのコンポーネントに、既存のアプリケーション証明書がないことを確認します。 ログ出力を確認します。 OPC PLC の出力を次に示します。OPC クライアントのログ出力も類似しています。

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
アプリケーション証明書がある場合は、準備手順に従って Docker ボリュームを削除します。

発行元証明書ストアおよび信頼できるピア証明書ストアに、OPC Vault CA 証明書がインストールされたことをログで確認します。 OPC PLC のログ出力を次に示します。OPC クライアントのログ出力も類似しています。 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC PLC では、OPC Vault によって署名された一部の OPC UA クライアントを信頼しません。

秘密キーの形式が PEM として認識され、新しいアプリケーション証明書がインストールされたことをログで確認します。 OPC PLC のログ出力を次に示します。OPC クライアントのログ出力も類似しています。 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

これで、アプリケーション証明書と秘密キーがアプリケーション証明書ストアにインストールされ、OPC UA アプリケーションによって使用されます。

OPC クライアントと OPC PLC の間の接続が正常に確立でき、OPC クライアントが OPC PLC からデータを正常に読み取れることを確認します。 OPC クライアントのログ出力に、次の出力が表示されるはずです。
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
この出力が表示される場合、OPC PLC と OPC クライアントが相互に信頼するようになっています。CA によって署名された証明書がこれで両方に用意されて、両方がこの CA によって署名された証明書を信頼しているためです。

### <a name="a-testbed-for-opc-publisher"></a>OPC Publisher のテスト環境 ###

**クイックスタート**

リポジトリのルートで次の PowerShell コマンドを実行します。
```
docker-compose -f testbed.yml up
```

**確認**
- [デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) または [iothub-explorer](https://github.com/Azure/iothub-explorer) を使用して `_HUB_CS` を設定することで構成した IoTHub に、データが送信されることを確認します。
- OPC テスト クライアントでは、OPC テスト サーバーからノードの発行/発行の取り消しを行うように OPC Publisher を構成するために、IoTHub ダイレクト メソッド呼び出しと OPC メソッド呼び出しが使用されます。
- 出力でエラー メッセージを確認します。

## <a name="next-steps"></a>次の手順

ここでは、OPC Vault を既存のプロジェクトにデプロイする方法を学習しました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [既存のプロジェクトに OPC Twin をデプロイする](howto-opc-twin-deploy-existing.md)