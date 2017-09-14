---
title: "Azure Service Fabric コンテナーのセキュリティ | Microsoft Docs"
description: "コンテナー サービスをセキュリティで保護する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: a262730aec6ce5a1c6f3b7d2d41056a6e6edfbe0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---

# <a name="container-security"></a>コンテナーのセキュリティ

Service Fabric には、コンテナー内のサービスから、Windows または Linux クラスター (バージョン 5.7 以降) のノードにインストールされている証明書にアクセスできるしくみがあります。 また、Service Fabric は、Windows コンテナーについて gMSA (グループの管理されたサービス アカウント) もサポートしています。 

## <a name="certificate-management-for-containers"></a>コンテナーの証明書管理

証明書を指定して、コンテナー サービスをセキュリティで保護することができます。 この証明書は、クラスターのノードにインストールする必要があります。 次のスニペットのように、証明書情報は `ContainerHostPolicies` タグのアプリケーション マニフェストで提供されます。

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

アプリケーションの起動時に、ランタイムは証明書を読み取り、各証明書の PFX ファイルとパスワードを生成します。 次の環境変数を使用して、コンテナー内のこの PFX ファイルとパスワードにはアクセスすることができます。 

* **Certificate_[CodePackageName]_[CertName]_PFX**
* **Certificate_[CodePackageName]_[CertName]_Password**

コンテナー サービスまたはプロセスが、PFX ファイルをコンテナーにインポートする責任を負います。 証明書をインポートするには、コンテナー プロセス内で`setupentrypoint.sh` スクリプトまたは実行済みのカスタム コードを使用できます。 PFX ファイルをインポートする C# のサンプル コードは次のとおりです。

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
この PFX 証明書は、アプリケーションやサービスを認証する場合や、他のサービスとの通信をセキュリティで保護する場合に使用できます。


## <a name="set-up-gmsa-for-windows-containers"></a>Windows コンテナーの gMSA を設定する

gMSA (グループの管理されたサービス アカウント) を設定するには、資格情報の指定ファイル (`credspec`) をクラスター内のすべてのノードに配置します。 VM 拡張機能を使用して、すべてのノードにファイルをコピーできます。  `credspec` ファイルには、gMSA アカウント情報を含める必要があります。 `credspec` ファイルの詳細については、「[Service Accounts](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts)」(サービス アカウント) を参照してください。 資格情報の指定と `Hostname` タグは、アプリケーション マニフェストに指定されています。 `Hostname` タグは、コンテナーが実行されている gMSA アカウント名と一致する必要があります。  `Hostname` タグを使用すると、Kerberos 認証を使用して、コンテナーが自身をドメイン内の他のサービスに対して認証することができます。  アプリケーション マニフェストで `Hostname` と `credspec` を指定するサンプルを次のスニペットに示します。

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>次のステップ

* [Windows Server 2016 上での Service Fabric への Windows コンテナーのデプロイ](service-fabric-get-started-containers.md)
* [Linux 上での Service Fabric への Docker コンテナーのデプロイ](service-fabric-get-started-containers-linux.md)

