---
title: OPC Vault を利用し、OPC クライアントと OPC PLC の通信をセキュリティで保護する - Azure | Microsoft Docs
description: OPC Vault CA を使用して証明書に署名することで、OPC クライアントおよび OPC PLC の通信がセキュリティで保護されます。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9b7123f6e74d13ad1d7efa07df6de7a184ad7401
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605739"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>OPC クライアントと OPC PLC の通信をセキュリティで保護する

OPC Vault は、OPC UA サーバーとクライアント アプリケーションに使用される証明書のライフサイクルの構成、登録、管理をクラウドで行うマイクロサービスです。 この記事では、OPC Vault CA を使用して証明書に署名することで、OPC クライアントおよび OPC PLC の通信をセキュリティで保護する方法を示します。

次のセットアップでは、OPC クライアントは OPC PLC への接続をテストします。 既定では、両方のコンポーネントが適切な証明書を使用してプロビジョニングされていないため、接続は不可能となっています。 OPC UA コンポーネントが証明書を使用してまだプロビジョニングされていなかった場合は、起動時に自己署名証明書が生成されます。 ただし、証明書の署名を証明機関 (CA) が行って、それを OPC UA コンポーネントにインストールすることができます。 OPC クライアントおよび OPC PLC に対してこれが実行されると、接続が有効になります。 下のワークフローは、このプロセスを説明しています。 OPC UA のセキュリティに関する一部の背景情報については、[このドキュメント](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) (ホワイト ペーパー) に記載されています。 完全な情報は、OPC UA の仕様書に記載されています。

テスト環境:テストのために次の環境が構成されています。

OPC Vault スクリプト:
- OPC Vault CA を使用して証明書に署名することで、OPC クライアントおよび OPC PLC の通信をセキュリティで保護します。

> [!NOTE]
> 詳細については、GitHub [リポジトリ](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)を参照してください。

## <a name="generate-a-self-signed-certificate-on-startup"></a>起動時に自己署名証明書を作成する

**準備**

- 環境変数 `$env:_PLC_OPT` および `$env:_CLIENT_OPT` が未定義であるようにします。たとえば、PowerShell で `$env:_PLC_OPT=""` と記述します。

- 環境変数 `$env:_OPCVAULTID` を、OPC Vault で再度データを見つけることができる文字列に設定します。 英数字のみを使用できます。 たとえば、この変数の値として "123456" が使用されました。

- Docker ボリュームの `opcclient` や `opcplc` がないようにします。 `docker volume ls` で調べて、`docker volume rm <volumename>` でそれらを削除します。 コンテナーによってまだボリュームが使用されている場合は、`docker rm <containerid>` でコンテナーも削除する必要が生じることがあります。

**クイックスタート**

リポジトリのルートで次の PowerShell コマンドを実行します。

```
docker-compose -f connecttest.yml up
```

**確認**

ログで、初回起動時にインストールされた証明書がないことを確認します。 OPC PLC のログ出力は次のようになります (OPC クライアントの場合も同様の内容が出力されます)。
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
報告対象の証明書が表示される場合は、上記の準備手順に従って Docker ボリュームを削除します。

OPC PLC への接続が失敗したことを確認します。 OPC クライアント ログ出力には次の出力が表示されるはずです。

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
失敗の理由は、証明書が信頼されていないことです。 これは、`opc-client` が `opc-plc` に接続しようとして、`opc-plc` が `opc-client` を信頼していないという応答を受信したことを示しています。これは、`opc-client` が提供した証明書を検証できない `opc-plc` の結果です。 これは、`opc-plc` に関する以後の証明書の構成がないために接続が失敗する自己署名証明書です。

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>署名して OPC UA コンポーネントに証明書をインストールする

**準備**
1. 手順 1 のログ出力を確認し、OPC PLC と OPC クライアントについての "CreateSigningRequest information" をフェッチします。 ここでの出力は、OPC PLC についてのみ表示されています。

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. [OPC Vault の Web サイト](https://opcvault.azurewebsites.net/)に移動します。

1. `Register New` を選択します

1. ログ出力の `CreateSigningRequest information` 領域から得た OPC PLC の情報を `Register New OPC UA Application` ページの入力フィールドに入力し、ApplicationType として `Server` を選択します。

1. [`Register`] を選択します。

1. 次のページ `Request New Certificate for OPC UA Application` で `Request new Certificate with Signing Request` を選択します。

1. 次のページ `Generate a new Certificate with a Signing Request` で、ログ出力からの `CSR (base64 encoded)` 文字列を `CreateRequest` 入力フィールドに貼り付けます。 文字列全体をコピーしたことを確認します。

1. [`Generate New Certificate`] を選択します。

1. これで `View Certificate Request Details` に進むことになります。 このページでは、`opc-plc` の証明書ストアをプロビジョニングするために必要なすべての情報をダウンロードできます。

1. このページでの手順:  
    - `Download as Base64` で `Certificate` を選択し、`EncodedBase64` フィールドに表示されるテキスト文字列をコピーしたら、後で使用するために保存します。 後で `<applicationcertbase64-string>` として参照します。 [`Back`] を選択します。

    - `Download as Base64` で `Issuer` を選択し、`EncodedBase64` フィールドに表示されるテキスト文字列をコピーしたら、後で使用するために保存します。 後で `<addissuercertbase64-string>` として参照します。 [`Back`] を選択します。

    - `Download as Base64` で `Crl` を選択し、`EncodedBase64` フィールドに表示されるテキスト文字列をコピーしたら、後で使用するために保存します。 後で `<updatecrlbase64-string>` として参照します。 [`Back`] を選択します。

1. ここで、PowerShell で `$env:_PLC_OPT` という名前の変数を設定します。

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Web サイトからフェッチしたオプション値の Base64 文字列として渡される文字列を置換します。

`Register New` (上記の手順 3) で始まるプロセス全体を、OPC クライアントに対して繰り返します。 注意する必要がある相違点は以下のみです。

- `opcclient` からのログ出力を使用します。
- 登録時の ApplicationType として `Client` を選択します。
- PowerShell 変数の名前として `$env:_CLIENT_OPT` を使用します。

> [!NOTE]
> このシナリオの作業中に、`opcplc` と `opcclient` について、`<addissuercertbase64-string>` と `<updatecrlbase64-string>` の値が同一であると気付いたかもしれません。 今回のユース ケースの場合はそのとおりで、手順の実行中に少し時間の節約になります。

**クイックスタート**

リポジトリのルートで次の PowerShell コマンドを実行します。

```
docker-compose -f connecttest.yml up
```

**確認** 2 つのコンポーネントに、署名のあるアプリケーション証明書が用意されたことを確認します。 ログ出力を調べ、次の出力を探します。

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
そこにアプリケーション証明書があり、CA によって署名されています。

ログで、今回はインストールされた証明書があることを確認します。 次に OPC PLC のログ出力を示します。OPC クライアントも同様の出力となります。
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
アプリケーション証明書の発行者は CA `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` で、OPC PLC は、この CA によって署名されたすべての証明書も信頼します。


OPC PLC への接続が正常に作成されていて、OPC クライアントが OPC PLC のデータを読み取れることを確認します。 OPC クライアント ログ出力には次の出力が表示されるはずです。
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
この出力が表示される場合、OPC PLC と OPC クライアントが相互に信頼するようになっています。CA によって署名された証明書がこれで両方に用意されて、両方がこの CA によって署名された証明書を信頼しているためです。

> [!NOTE] 
> OPC PLC についての最初の 2 つの確認手順のみを示しましたが、それらは OPC クライアントに対しても確認する必要があります。


## <a name="next-steps"></a>次の手順

ここでは、OPC Vault を既存のプロジェクトにデプロイする方法を学習しました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [既存のプロジェクトに OPC Twin をデプロイする](howto-opc-twin-deploy-existing.md)