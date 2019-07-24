---
title: Azure Service Fabric コンテナー サービスに対して gMSA を設定する | Microsoft Docs
description: Azure Service Fabric で実行されているコンテナーに対して gMSA を設定する方法について説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo, subramar
ms.openlocfilehash: ae8c5c8ec1e16669b3cbdde8b3eaa3d5dbb7c4de
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668553"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Service Fabric で実行されている Windows コンテナーに対して gMSA を設定する

gMSA (グループの管理されたサービス アカウント) を設定するには、資格情報の指定ファイル (`credspec`) をクラスター内のすべてのノードに配置します。 VM 拡張機能を使用して、すべてのノードにファイルをコピーできます。  `credspec` ファイルには、gMSA アカウント情報を含める必要があります。 `credspec` ファイルの詳細については、[資格情報の仕様の作成](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)に関するページをご覧ください。資格情報の指定と `Hostname` タグは、アプリケーション マニフェストに指定されています。 `Hostname` タグは、コンテナーが実行されている gMSA アカウント名と一致する必要があります。  `Hostname` タグを使用すると、Kerberos 認証を使用して、コンテナーが自身をドメイン内の他のサービスに対して認証することができます。  アプリケーション マニフェストで `Hostname` と `credspec` を指定するサンプルを次のスニペットに示します。

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
次の手順については、次の記事を参照してください。

* [Windows Server 2016 上での Service Fabric への Windows コンテナーのデプロイ](service-fabric-get-started-containers.md)
* [Linux 上での Service Fabric への Docker コンテナーのデプロイ](service-fabric-get-started-containers-linux.md)
