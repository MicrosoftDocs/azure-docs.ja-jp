---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c6f9065786879749eee6187e93283f4c026b7fff
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568806"
---
以下の手順では、次のコンピューターの構成を使用しました。

  | | |
  |---|---|
  |Computer| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |依存関係| strongSwan |

#### <a name="1-install-strongswan"></a>1.strongSwan のインストール

次のコマンドを使用して、必要な strongSwan 構成をインストールします。

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```

#### <a name="2-generate-keys-and-certificate"></a>2.キーと証明書を生成する

キーと証明書を生成します。

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

CA 証明書 を base64 形式で印刷します。 これは Azure でサポートされている形式です。 P2S 構成の一環として、後にこれを Azure にアップロードすることになります。

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

ユーザー証明書を生成します。

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

ユーザー証明書を収納している p12 バンドルを生成します。 このバンドルは、クライアント構成ファイルと連携する場合に、次の手順で使用されます。

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```