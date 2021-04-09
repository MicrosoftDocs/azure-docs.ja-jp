---
title: Azure Service Fabric Mesh の概要
description: Azure Service Fabric Mesh について説明します。 Service Fabric Mesh では、アプリケーションのインフラストラクチャに関するニーズについて心配することなく、アプリケーションをデプロイしてスケーリングできます。
author: georgewallace
ms.author: gwallace
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: 64880a9ac3d6d48ce6d39d0b3a7a3aff6587f328
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626979"
---
# <a name="what-is-service-fabric-mesh"></a>Service Fabric Mesh とは

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

このビデオでは、Service Fabric Mesh の概要を簡単に説明します。
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 Service Fabric Mesh でホストされたアプリケーションは、その基盤となるインフラストラクチャについて心配しなくとも、実行され、スケーリングされます。  Service Fabric Mesh は数千台のマシンのクラスターで構成されます。  すべてのクラスター操作は開発者から見えなくなっています。 コードをアップロードし、必要なリソース、可用性の要件、リソースの制限を指定します。  Service Fabric Mesh では、インフラストラクチャが自動的に割り当てられ、インフラストラクチャの障害も処理されます。これにより、アプリケーションの高可用性が確保されます。 アプリケーションの正常性と応答性さえ管理すればよく、インフラストラクチャを管理する必要がありません。  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

この記事では、Service Fabric Mesh の主なベネフィットについて概説します。

## <a name="great-developer-experience"></a>優れた開発者エクスペリエンス

Service Fabric Mesh では、コンテナーで実行できるあらゆるプログラミング言語またはフレームワークがサポートされます。 Visual Studio 2019 と Visual Studio Code ツール サポートによって、.NET および .NET Core アプリケーション用の強力な編集およびデバッグ エクスペリエンスが提供されます。 

Service Fabric Mesh では、以下が可能です。

- 現在のアプリケーションを最新化して大規模に実行するために、既存のアプリケーションをコンテナーに "リフト アンド シフト" する。
- Azure で新しいマイクロサービス アプリケーションを構築して大規模にデプロイする。  コンテナーで実行されている他の Azure サービスや既存のアプリケーションと統合します。 各マイクロサービスは、ネットワークから分離された、セキュリティで保護されたアプリケーションの一部です。 マイクロサービスには、CPU コア、メモリ、ディスク領域などについて定義されているリソース ガバナンス ポリシーがあります。
- 既存のアプリケーションに変更を加えることなく、それらのアプリケーションと統合して拡張する。 独自の仮想ネットワークを使用して、既存のアプリケーションを新しいアプリケーションに接続します。  
- Service Fabric Mesh に移行して、既存の Cloud Services アプリケーションを最新化する。  

## <a name="simple-operational-lifecycle"></a>シンプルな運用ライフサイクル

運用環境におけるアプリケーションの実行、アプリケーションの監視、デバッグを簡単に管理できます。 この管理には、アプリケーションのアップグレードとバージョン管理が含まれます。 これらのアプリケーションは、独自のネットワーク内に分離された単一または複数のマイクロサービスで構成できます。 デプロイ、配置、フェールオーバーにかかる時間が短く、アプリケーションは効率的に実行されます。

Service Fabric Mesh では、以下が可能です。

- インフラストラクチャを明示的にプロビジョニングして管理することなく、アプリケーションのデプロイと管理を行う。  Service Fabric Mesh では、基盤となるインフラストラクチャのプロビジョニング、アップグレード、パッチ適用、メンテナンスが自動で行われます。
- アプリケーションを簡単にパッケージ化してデプロイできる統合されたツールを使用して、継続的インテグレーションを設定する。
- Azure Resource Manager リソースのすべての機能を活用する。 これらの機能の例には、監査証跡と [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) が含まれます。 Azure で Service Fabric Mesh サービスにデプロイするすべてのリソースは Azure Resource Manager リソースです。 これらのリソースには、アプリケーション、サービス、シークレットなどが含まれます。
- [Azure portal](https://portal.azure.com)、Resource Manager テンプレート、または Azure CLI/PowerShell ライブラリを使用して、リソースのデプロイと管理を行う。
- [Application Insights](/azure/application-insights/) (または好きなツール) を使用して操作の監視とアラートを設定し、プラットフォームから操作と診断のトレースを収集する。
- [Application Insights](/azure/application-insights/) または好きなツールを使用して、アプリケーション モデルから出力されたアプリケーション診断情報にアクセスする。
- アプリケーション定義でサービスの自動スケーリング規則を指定して、リソース使用率を最適化する。

## <a name="mission-critical-platform-capabilities"></a>ミッション クリティカルなプラットフォーム機能

Service Fabric Mesh では、[Azure 可用性ゾーン](../availability-zones/az-overview.md)/地政学的リージョンの境界にまたがる一連のクラスターが作成されます。 Service Fabric Mesh では、スケール、ハードウェア要件、持続性要件、セキュリティ ポリシーなどの一連の意図を使用してアプリケーションを説明します。  アプリケーションのデプロイ時、Service Fabric Mesh によって最適な実行場所が検出されます。

Service Fabric Mesh では、以下が可能です。

- 高可用性、スケールイン/スケールアウト、検出可能性、オーケストレーション、メッセージ ルーティング、信頼できるメッセージング、ダウンタイムのないアップグレード、セキュリティ/シークレット管理、ディザスター リカバリー、状態管理、構成管理、分散トランザクションを活用する。
- アプリケーションの作成時に複数の中からアプリケーション モデルを選択する。
- Swagger で生成された言語固有のバインドを使用して、REST エンドポイントを通じて公開されているプラットフォーム機能を使用する。
- 地域の信頼性を高めるために、[可用性ゾーン](../availability-zones/az-overview.md)と複数のリージョンをまたいでアプリケーションをデプロイする。
- Azure によって提供されるすべてのセキュリティ機能とコンプライアンス機能を使用する。

## <a name="next-steps"></a>次のステップ

Visual Studio でのサンプル プロジェクトのデプロイに必要な手順はわずか数ステップです。 詳細については、[ASP.NET Core Web サイトの作成](service-fabric-mesh-quickstart-dotnet-core.md)に関する記事を参照してください。 

[よく寄せられる質問](service-fabric-mesh-faq.md)に対する回答を確認します。


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
