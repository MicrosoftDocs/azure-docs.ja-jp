---
title: Azure Service Fabric Windows クラスターでの暗号化証明書の設定とシークレットの暗号化 | Microsoft Docs
description: Windows クラスターでの暗号化証明書の設定とシークレットの暗号化を行う方法について説明します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662314"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Windows クラスターでの暗号化証明書の設定とシークレットの暗号化
この記事では、Windows クラスターでの暗号化証明書の設定と、これを使用してシークレットの暗号化を行う方法について説明します。 Linux クラスターについては、[Linux クラスターでの暗号化証明書の設定とシークレットの暗号化][secret-management-linux-specific-link]を参照してください。

ここでは、証明書の安全な格納場所として [Azure Key Vault][key-vault-get-started] を使用します。また、Azure の Service Fabric クラスターにインストールされている証明書を取得する方法としても使用します。 Azure にデプロイしない場合は、Service Fabric アプリケーションでのシークレットの管理に Key Vault を使用する必要はありません。 ただし、アプリケーションでのシークレットの " *使用* " はクラウド プラットフォームに依存しないので、クラスターでホストされている任意の場所にアプリケーションをデプロイできます。 

## <a name="obtain-a-data-encipherment-certificate"></a>データ暗号化証明書を取得する
データ暗号化証明書は、サービスの Settings.xml 内の[パラメータ][parameters-link]およびサービスの ServiceManifest.xml 内の[環境変数][environment-variables-link]の暗号化と暗号化解除のためにのみ使用されます。 認証や暗号テキストの署名には使用されません。 証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
* 証明書キーの使用法として、データ暗号化 (10) を指定する必要があります。サーバー認証やクライアント認証を指定することは使用できません。 
  
  たとえば、PowerShell を使用して自己署名証明書を作成するときは、`KeyUsage` フラグを `DataEncipherment` に設定する必要があります。
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>クラスターへの証明書のインストール
この証明書は、クラスターの各ノードにインストールする必要があります。 セットアップ手順については、[Azure Resource Manager を使用してクラスターを作成する方法][service-fabric-cluster-creation-via-arm]に関する記事をご覧ください。 

## <a name="encrypt-application-secrets"></a>アプリケーション シークレットの暗号化
次の PowerShell コマンドを使用して、シークレットを暗号化します。 このコマンドは値を暗号化するだけであり、暗号化テキストの署名は**行いません**。 クラスターにインストールされている同じ暗号化証明書を使用して、シークレット値の暗号化テキストを生成する必要があります。

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

生成された Base-64 エンコード文字列には、シークレットの暗号化テキストと、暗号化に使用された証明書に関する情報が含まれます。

## <a name="next-steps"></a>次の手順
[アプリケーション内で暗号化されたシークレットを指定する][secret-management-specify-encrypted-secrets-link]方法について学習します。

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
