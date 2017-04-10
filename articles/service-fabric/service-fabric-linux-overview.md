---
title: "Linux 上の Azure Service Fabric | Microsoft Docs"
description: "Service Fabric クラスターは Linux と Java をサポートするので、Java と C# で記述された Service Fabric アプリケーションを Linux にデプロイし、ホストすることができます。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 459afade-145d-4ee6-b72b-ddf380ccd1bf
ms.service: service-fabric
ms.devlang: Java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: SubramaR
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 62d7e0088c66a538dff7f3b99882ebef4949211b
ms.lasthandoff: 03/29/2017


---
# <a name="service-fabric-on-linux"></a>Linux 上の Service Fabric
Linux 上の Service Fabric を使用すると、Windows 上と同じように、Linux で可用性とスケーラビリティの高いアプリケーションを構築、デプロイ、および管理できます。 Service Fabric フレームワーク (Reliable Services と Reliable Actors) を、Linux 上の C# (.NET Core) の他に、Java でも使用できるようになりました。  任意の言語またはフレームワークで[ゲスト実行可能サービス](service-fabric-deploy-existing-app.md)を構築することもできます。 さらに、プレビューでは、調整 Docker コンテナーもサポートされています。 Docker コンテナーは、ゲスト実行可能ファイルまたはネイティブ Service Fabric サービスを実行できます。これらは、Service Fabric フレームワークを使用します。

Linux 上の Service Fabric は、概念的には Windows 上の Service Fabric と同じです (OS の詳細およびプログラミング言語のサポートを除く)。 したがって、 [既存のドキュメント](http://aka.ms/servicefabricdocs) のほとんどがこちらにも適用されるため、テクノロジを理解するうえで役立ちます。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Service-Fabric-Linux-Preview/player]
>
>

## <a name="supported-operating-systems-and-programming-languages"></a>サポートされるオペレーティング システムとプログラミング言語
制限付きプレビューは、Ubuntu Server 16.04 が実行されている Azure でマルチマシン クラスターのほか、ワンボックス開発クラスターとの作成をサポートします。 プレビューでは、ゲスト実行可能ファイルと調整 Docker コンテナーの他に、Reliable Actors と Reliable Stateless Services フレームワークを Java および C# でサポートしています。  

> [!NOTE]
> Reliable Collection は Linux ではまだサポートされていません。 スタンド アロン クラスターも、サポートされていません。プレビューでは、ワン ボックスと Azure Linux マルチマシン クラスターだけがサポートされています。
>
>


## <a name="supported-tooling"></a>サポートされているツール
プレビューでは、Azure CLI を介したクラスターの対話操作がサポートされています。 Java 開発者のために、Eclipse および Yeoman との統合が提供されており、Eclipse は Linux および OSX 上でサポートされています。 OSX の統合では、内部的には vagrant を介して Linux VM を使用します。 C# 開発者のためには、アプリケーション テンプレートを生成できるように、Yeoman との統合が提供されています。

## <a name="next-steps"></a>次のステップ
1. [Reliable Actors](service-fabric-reliable-actors-introduction.md) および [Reliable Services](service-fabric-reliable-services-introduction.md) プログラミング フレームワークを理解します。
2. [Linux で開発環境を準備する](service-fabric-get-started-linux.md)
3. [OSX で開発環境を準備する](service-fabric-get-started-mac.md)
4. [Linux 上で最初の Service Fabric Java アプリケーションを作成する](service-fabric-create-your-first-linux-application-with-java.md)
5. [Jenkins と GitHub による Service Fabric の継続的インテグレーションとデプロイの設定](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
6. [Service Fabric における Windows と Linux の違い](service-fabric-linux-windows-differences.md)

