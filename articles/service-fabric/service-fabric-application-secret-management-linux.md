---
title: Linux クラスターでの暗号化証明書の設定
description: Linux クラスターでの暗号化証明書の設定とシークレットの暗号化を行う方法について説明します。
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78969034"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Linux クラスターでの暗号化証明書の設定とシークレットの暗号化
この記事では、Linux クラスターでの暗号化証明書の設定と、これを使用してシークレットの暗号化を行う方法について説明します。 Windows クラスターについては、「[Windows クラスターでの暗号化証明書の設定とシークレットの暗号化][secret-management-windows-specific-link]」を参照してください。

## <a name="obtain-a-data-encipherment-certificate"></a>データ暗号化証明書を取得する
データ暗号化証明書は、サービスの Settings.xml 内の[パラメーター][parameters-link]、およびサービスの ServiceManifest.xml 内の[環境変数][environment-variables-link]の暗号化と暗号化解除のためにのみ使用されます。 認証や暗号テキストの署名には使用されません。 証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書キーの使用法として、データ暗号化 (10) を指定する必要があります。サーバー認証やクライアント認証を指定することは使用できません。

  たとえば、OpenSSL を使用して必要な証明書を生成するには、次のコマンドを使用できます。
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>クラスターへの証明書のインストール
この証明書は、クラスターの各ノードの `/var/lib/sfcerts` にインストールする必要があります。 サービスを実行しているユーザー アカウント (既定では sfuser) は、インストール済みの証明書 (現在の例では `/var/lib/sfcerts/TestCert.pem`) に対する **読み取りアクセスを持つ必要があります**。

## <a name="encrypt-secrets"></a>シークレットを暗号化する
次のスニペットは、シークレットの暗号化に使用できます。 このスニペットは値を暗号化するだけであり、暗号化テキストの署名は **行いません**。 クラスターにインストールされている同じ暗号化証明書を使用して、シークレット値の暗号化テキストを生成する **必要があります**。

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
encrypted.txt に出力される生成された Base-64 エンコード文字列は、シークレットの暗号化テキストと、暗号化に使用された証明書に関する情報が含まれています。 その有効性は、OpenSSL で暗号化を解除することによって確認できます。
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>次のステップ
[アプリケーション内で暗号化されたシークレットを指定する][secret-management-specify-encrypted-secrets-link]方法について学習します。

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
