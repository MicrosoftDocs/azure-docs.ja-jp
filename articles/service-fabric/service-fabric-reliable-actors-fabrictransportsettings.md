---
title: "Azure マイクロサービスで FabricTransport の設定を変更する | Microsoft Docs"
description: "Azure Service Fabric アクターの通信の設定を構成する方法について説明します。"
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: f7edee399717ecb96fb920d0a938da551101c9e1
ms.openlocfilehash: 6041541903d4d90710817149be50e05e31fd88f1
ms.lasthandoff: 01/24/2017


---
# <a name="configuring-fabrictransport-settings-for-reliable-actors"></a>Reliable Actors の FabricTransport 設定の構成

ここでは、ユーザーが [FabrictTansportSettings](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings) を構成できる設定の一覧を示します。

次の方法で FabricTransport の既定の構成を変更できます。

1.  アセンブリ属性 [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute) を使用します。

  この属性は、アクターのクライアントとアクター サービスのアセンブリに適用する必要があります。
  次の例では、FabricTransport OperationTimeout 設定の既定値を変更する方法を示します。

  ```csharp
     using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   2 番目の例では、FabricTransport MaxMessageSize と OperationTimeoutInSeconds の既定値を変更します。

    ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
    ```

2. [構成パッケージ](service-fabric-application-model.md)を使用して次の処理を行います。

  * アクター サービスの FabricTransport 設定の構成

    settings.xml ファイルに TransportSettings セクションを追加します。

    * SectionName: 既定では、アクターのコードは "&lt;ActorName&gt;TransportSettings" として SectionName を検索します。 見つからない場合は、"TransportSettings" として sectionName を調べます。

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

  * アクター クライアント アセンブリの FabricTransport 設定の構成

    クライアントがサービスの一部として実行されていない場合は、client exe と同じ場所に "&lt;Client Exe Name&gt;.settings.xml" xml ファイルを作成し、 そのファイル内に TransportSettings セクションを追加できます。 SectionName は "TransportSettings" にする必要があります。

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

