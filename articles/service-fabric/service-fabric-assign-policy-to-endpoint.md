---
title: Azure Service Fabric サービス エンドポイントにアクセス ポリシーを割り当てる | Microsoft Docs
description: ご利用の Service Fabric サービスの HTTP または HTTPS エンドポイントにセキュリティ アクセス ポリシーを割り当てる方法について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 3e892e443f5e3309add48f939f26ba14eaf5a51b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670406"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>HTTP と HTTPS エンドポイントのセキュリティ アクセス ポリシーを割り当てる
実行ポリシーを適用する場合、サービス マニフェストによって HTTP エンドポイント リソースを宣言するときは、**SecurityAccessPolicy** を指定する必要があります。  **SecurityAccessPolicy** により、これらのエンドポイントに割り当てられたポートは、サービスが実行されるユーザー アカウントでのみ正しく使用できるようになります。 それ以外の場合は、 **http.sys** はサービスにアクセスできず、クライアントからの呼び出しで失敗します。 次の例では、Customer1 アカウントを **EndpointName** エンドポイントに適用し、フル アクセス権限を付与しています。

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

HTTPS エンドポイントの場合は、クライアントに返す証明書の名前も指示します。 証明書を参照するには、**EndpointBindingPolicy** を使用します。  証明書は、アプリケーション マニフェストの**証明書**セクションに定義されています。

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> HTTPS を使用する場合は、同じノードにデプロイされる (アプリケーションから独立した) 異なるサービス インスタンスに同じポートと証明書を使用しないでください。 異なるアプリケーション インスタンスで同じポートを使用して 2 つの異なるサービスをアップグレードすると、アップグレード エラーが発生します。 詳細については、「[HTTPS エンドポイントを持つ複数のアプリケーションのアップグレード](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)」を参照してください。
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
次の手順については、次の記事を参照してください。
* [アプリケーション モデルを理解する](service-fabric-application-model.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
