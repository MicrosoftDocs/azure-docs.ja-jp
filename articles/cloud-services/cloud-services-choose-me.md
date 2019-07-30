---
title: Azure Cloud Services とは| Microsoft Docs
description: Azure Cloud Services について説明します。
services: cloud-services
author: georgewallace
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 61369d51056607d8176d301afa945c7c77895b12
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359708"
---
# <a name="overview-of-azure-cloud-services"></a>Azure Cloud Services の概要
Azure Cloud Services は、[サービスとしてのプラットフォーム](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) の 1 つの例です。 このテクノロジは、[Azure App Service](../app-service/overview.md) と同様に、スケーラブルで信頼性が高く、運用コストが低いアプリケーションをサポートするように設計されています。 App Service と同様に、Azure Cloud Services も仮想マシン (VM) 上でホストされます。 しかし、VM に対してより細かな制御を行うことができます。 Azure Cloud Services を使用する VM に独自のソフトウェアをインストールし、それらにリモートでアクセスできます。

![Azure Cloud Services の図](./media/cloud-services-choose-me/diagram.png)

コントロール能力の高さは、使いやすさが低下することも意味します。 追加のコントロール オプションを必要としない限り、通常は App Service の Web Apps 機能の方が Azure Cloud Services よりも迅速かつ容易に Web アプリケーションを実行できます。

Azure Cloud Services ロールには、次の 2 種類があります。 2 つの唯一の違いは、VM でロールがホストされる方法です。

* **Web ロール**:IIS を使用して自動的にアプリをデプロイおよびホストします。

* **worker ロール**:IIS を使用せず、アプリをスタンドアロンで実行します。

たとえば、単純なアプリケーションでは、web ロールを 1 つだけ使用して web サイトにサービスを提供している場合があります。 もっと複雑なアプリケーションでは、Web ロールを使用してユーザーからの受信要求を処理し、次にそれらの要求を worker ロールに渡して処理を行っている場合があります (この通信は、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) または [Azure Queue Storage](../storage/common/storage-introduction.md) を使用する可能性があります)。

上記の図に示すように、1 つのアプリケーションのすべての VM は同じクラウド サービスで実行されます。 ユーザーは 1 つのパブリック IP アドレスを通してアプリケーションにアクセスし、要求はアプリケーションの VM 間で自動的に負荷分散されます。 プラットフォームは、ハードウェアの単一障害点を回避するように、Azure Cloud Services アプリケーションで VM を[スケールおよびデプロイ](cloud-services-how-to-scale-portal.md)します。

アプリケーションは VM で実行しますが、Azure Cloud Services はサービスとしてのインフラストラクチャ (IaaS) ではなく PaaS を提供することを理解することが重要です。 次の例を考えてみましょう。 Azure Virtual Machines のような IaaS では、アプリケーションが実行する環境を最初に作成して構成します。 次に、この環境にアプリケーションをデプロイします。 この方法では、パッチが適用された新しいオペレーティング システムのバージョンを各 VM にデプロイするなど、ほとんどの管理をユーザーが担当します。 それに対し、PaaS では環境が既に存在するかのように管理できます。 ユーザーはアプリケーションをデプロイするだけで済みます。 オペレーティング システムの新バージョンのデプロイをはじめ、実行するプラットフォームの管理がユーザーに代わって処理されます。

## <a name="scaling-and-management"></a>スケーリングと管理
Azure Cloud Services では、ユーザーは仮想マシンを作成しません。 代わりに、"Web ロール インスタンスを 3 個"、"worker ロール インスタンスを 2 個" のように、それぞれがいくつ必要かを Azure に指示する構成ファイルを提供します。 すると、プラットフォームによってそれらが自動的に作成されます。 その場合も、バッキング VM の [サイズ](cloud-services-sizes-specs.md) を選択する必要がありますが、自身で明示的に作成する必要はありません。 アプリケーションが高い負荷を処理する場合は、追加の VM を要求すると、Azure がそれらのインスタンスを作成します。 負荷が減少した場合は、それらのインスタンスをシャットダウンして支払いを停止できます。

Azure Cloud Services アプリケーションは通常、2 つの手順から成るプロセスで利用可能になります。 最初に、開発者はプラットフォームのステージング領域に [アプリケーションをアップロード](cloud-services-how-to-create-deploy-portal.md) します。 アプリケーションを稼働する準備が整ったら、Azure Portal を使用して運用とステージングを切り替えます。 [ステージングと運用の切り替え](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) にはダウンタイムが生じないため、ユーザーに支障を与えることなく、実行中のアプリケーションを新バージョンにアップグレードできます。

## <a name="monitoring"></a>監視
Azure Cloud Services は監視も提供します。 Virtual Machines と同様に、故障した物理サーバーを検出し、そのサーバーで実行していた VM を別のマシンで再開します。 さらに、Azure Cloud Services はハードウェアの故障だけではなく、エラーが発生した VM やアプリケーションも検出します。 Virtual Machines と異なり、各 Web ロール内と Worker ロール内にエージェントが含まれているので、エラーが発生したときに、新しい VM とアプリケーションのインスタンスを開始できます。

PaaS という Azure Cloud Services の本質には、他の含意もあります。 最も重要なことの 1 つは、このテクノロジを基盤に構築されるアプリケーションは、Web または Worker ロール インスタンスでエラーが発生したときに正しく実行するように記述することが必要です。 これを実現するには、Azure Cloud Services のアプリケーションがそれ自体の VM のファイル システムで状態を維持してはなりません。 Virtual Machines で作成された VM と異なり、Azure Cloud Services VM への書き込みは永続的ではありません。 Virtual Machines のデータ ディスクのようなものはありません。 Azure Cloud Services アプリケーションはすべての状態を Azure SQL Database、BLOB、テーブルか、その他の外部ストレージに明示的に書き込む必要があります。 この方法でアプリケーションを構築すると、スケーリングしやすく、耐障害性が備わります。この 2 つは Azure Cloud Services の重要なゴールです。

## <a name="next-steps"></a>次の手順
* [.NET でクラウド サービス アプリケーションを作成する](cloud-services-dotnet-get-started.md) 
* [nodejs でクラウド サービス アプリケーションを作成する](cloud-services-nodejs-develop-deploy-app.md) 
* [php でクラウド サービス アプリケーションを作成する](../cloud-services-php-create-web-role.md) 
* [Python でのクラウド サービス アプリの作成](cloud-services-python-ptvs.md)



