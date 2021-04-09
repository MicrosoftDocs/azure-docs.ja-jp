---
title: Azure Service Fabric の概要
description: Service Fabric は、スケーラブルで信頼性が高く管理しやすいマイクロサービスを構築するための分散システム プラットフォームです。
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: b6a14ba3f905fbef3fc3796b616cd594941a3d60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97028633"
---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric の概要

Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのパッケージ化とデプロイ、管理を簡単に行うことができる[分散システム プラットフォーム](#container-orchestration)です。 Service Fabric は、クラウド ネイティブ アプリケーションの[開発と管理](#application-lifecycle-management)における重要な課題にも対処します。

Service Fabric の主な差別化要因は、ステートフル サービスの構築に大きな重点を置いていることです。 Service Fabric [プログラミング モデル](#stateless-and-stateful-microservices)を使用することも、任意の言語またはコードで記述されたコンテナー化されたステートフル サービスを実行することもできます。 Azure 以外にも、[Service Fabric クラスターは任意の場所](#any-os-any-cloud)に作成できます。これには、Windows Server、Linux のオンプレミスや他のパブリック クラウドが含まれます。

![Service Fabric プラットフォームにより、Azure、オンプレミス、他のクラウド、開発マシンに、ライフサイクル管理、可用性、オーケストレーション、プログラミング モデル、正常性と監視、開発と運用のツール、および自動スケールが提供されます。][Image1]

現在、Service Fabric は、Azure SQL Database、Azure Cosmos DB、Cortana、Microsoft Power BI、Microsoft Intune、Azure Event Hubs、Azure IoT Hub、Dynamics 365、Skype for Business、などの Azure の核となる多くのサービスを含む、さまざまな Microsoft サービスで使用されています。

## <a name="container-orchestration"></a>コンテナーのオーケストレーション

Service Fabric は、マシンのクラスター全体にマイクロサービスをデプロイして管理するための Microsoft の[コンテナー オーケストレーター](service-fabric-cluster-resource-manager-introduction.md)であり、Microsoft サービスを大規模に実行することで学んだ教訓を活用しています。 Service Fabric により、マシンあたり数百から数千のアプリケーションまたはコンテナーという高密度で、アプリケーションを短時間でデプロイできます。 Service Fabric を使用すると、プロセスとしてのサービスとコンテナー内のサービスの両方を同じアプリケーション内で混在させることができます。

Service Fabric の主要な概念、プログラミング モデル、アプリケーション ライフサイクル、テスト、クラスター、および正常性の監視についての詳細は、[こちら](service-fabric-content-roadmap.md)をご覧ください。

## <a name="stateless-and-stateful-microservices"></a>ステートレスとステートフル マイクロサービス

Service Fabric は、ステートレス マイクロサービスとステートフル マイクロサービスをサポートする高度な軽量ランタイムを提供します。 Service Fabric の主な差別化要因は、Service Fabric の[組み込みプログラミング モデル](service-fabric-choose-framework.md)またはコンテナー化されたステートフル サービスを使用して、ステートフル サービスを構築するためのその堅牢なサポートにあります。

Service Fabric ステートフル サービスの恩恵を受けるアプリケーション シナリオの詳細については、[こちら](service-fabric-application-scenarios.md)をご覧ください。

## <a name="application-lifecycle-management"></a>アプリケーション ライフサイクル管理

Service Fabric により、開発からデプロイ、日常的な管理、保守、最終的な使用停止に至るまでのアプリケーション ライフサイクルとコンテナーを含むクラウド アプリケーションの完全な CI/CD のサポートが提供されます。 Service Fabric は、[Azure Pipelines](https://www.visualstudio.com/team-services/)、[Jenkins](https://jenkins.io/index.html)、[Octopus Deploy](https://octopus.com/) などの CI/CD ツールと統合し、他のすべての一般的な CI/CD ツールで使うことができます。

アプリケーション ライフサイクル管理の詳細については、[アプリケーションのライフサイクル](service-fabric-application-lifecycle.md)に関するページを参照してください。 既存のアプリケーションを Service Fabric にデプロイする方法については、[ゲスト実行可能ファイルのデプロイ](service-fabric-deploy-existing-app.md)に関するページを参照してください。

## <a name="any-os-any-cloud"></a>すべての OS、すべてのクラウド

[Windows Server または Linux](service-fabric-linux-windows-differences.md) 上のさまざまな環境 ([Azure またはオンプレミス](service-fabric-deploy-anywhere.md)を含む) で、Service Fabric のクラスターを作成できます。 他のパブリック クラウド上にクラスターを作成することさえできます。 Service Fabric SDK の開発環境は、エミュレーターが関係していない運用環境と同じです。 つまり、ローカルの開発クラスターで実行されるものが、他の環境内のクラスターにデプロイされます。

[Windows 開発](service-fabric-get-started.md)の場合、Service Fabric .NET SDK は、Visual Studio と PowerShell に統合されています。 [Linux 開発](service-fabric-get-started-linux.md)の場合、Service Fabric Java SDK が Eclipse に統合されており、Java、.NET Core、およびコンテナー アプリケーション用のテンプレートを生成するために Yeoman が使用されています。

## <a name="compliance"></a>コンプライアンス

Azure Service Fabric リソース プロバイダーは、すべての Azure リージョンで利用でき、すべての Azure コンプライアンス認定に準拠しています。これには、SOC、ISO、PCI DSS、HIPAA、および GDPR が含まれます。 完全な一覧については、[Microsoft コンプライアンス認証](https://www.microsoft.com/trustcenter/compliance/complianceofferings)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure Service Fabric で最初のアプリケーションを作成してデプロイする:

> [!div class="nextstepaction"]
> [Service Fabric クイックスタート][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
