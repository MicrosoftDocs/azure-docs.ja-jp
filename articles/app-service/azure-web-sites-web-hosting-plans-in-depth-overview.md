---
title: "Azure App Service プランの詳細な概要 | Microsoft Docs"
description: "Azure App Service の App Service プランのしくみと、それが管理機能にもたらすメリットについて説明します。"
keywords: "App Service, Azure App Service, スケール, スケーラブル, App Service プラン, App Service コスト"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.openlocfilehash: 688f57de662fec6a04227c35d6578097c795c6da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-plans-in-depth-overview"></a>Azure App Service プランの詳細な概要

App Service プランは、アプリをホストするために使用する物理リソースのコレクションを表しています。

App Service プランには、次の定義があります。

- リージョン (米国西部、米国東部など)
- スケール カウント (インスタンス数 1、2、3 など)
- インスタンス サイズ (Small、Medium、Large)
- SKU (Free、Shared、Basic、Standard、Premium、PremiumV2、Isolated)

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の Web Apps、Mobile Apps、Function Apps (または Functions) は、すべて App Service プラン内で実行されます。  同じサブスクリプションとリージョン内のアプリは、App Service プランを共有できます。 

**App Service プラン**に割り当てられたすべてのアプリケーションは、プランで定義されたリソースを共有します。 この共有により、1 つの App Service プランで複数のアプリをホストする場合にコストを節約できます。

**App Service プラン**は、**Free** および **Shared** レベルから、**Basic**、**Standard**、**Premium**、および **Isolated** レベルにスケールできます。 上位のレベルになるほど多くのリソースや機能にアクセスできます。

App Service プランを **Basic** レベル以上に設定する場合は、VM の **サイズ**とスケール カウントを制御できるようになります。

たとえば、**Standard** レベルで 2 つの "small" インスタンスを使用するようにプランが構成されている場合、そのプランのすべてのアプリが両方のインスタンス上で実行されます。 また、アプリは **Standard** レベルの機能にアクセスすることができます。 アプリを実行するプランのインスタンスは、完全に管理され、高い可用性が確保されます。

> [!IMPORTANT]
> App Service プランの価格レベル (SKU) によってコストが決まります。ホストできるアプリの数には影響しません。

この記事では、価格レベルとスケール、アプリを管理する中でそれらがどのように作用するかなど、App Service プランの主な特徴を詳しく見ていきます。

## <a name="new-pricing-tier-premiumv2"></a>新しい価格レベル: PremiumV2

新しい価格レベル **PremiumV2** は、より高速なプロセッサ、SSD ストレージを搭載し、メモリ対コア比が **Standard** レベルの 2 倍である [Dv2 シリーズ VM](../virtual-machines/windows/sizes-general.md#dv2-series) を提供します。 **PremiumV2** は、インスタンス数が多いためサポートできるスケールも大きく、その一方で Standard プランの高度な機能もすべて提供します。 既存の **Premium** レベルで使用可能なすべての機能が **PremiumV2** に含まれています。

他の特化したレベルと同様に、次の 3 つの VM サイズをこのレベルに利用できます。

- Small (1 CPU コア、3.5 GB メモリ) 
- Medium (2 CPU コア、7 GB メモリ) 
- Large (4 CPU コア、14 GB メモリ)  

**PremiumV2** の価格については、「[App Service の価格](/pricing/details/app-service/)」をご覧ください。

新しい価格レベル **PremiumV2** を使用するには、「[Configure PremiumV2 tier for App Service (App Service 向け PremiumV2 レベルの構成)](app-service-configure-premium-tier.md)」をご覧ください。

## <a name="apps-and-app-service-plans"></a>アプリと App Service プラン

App Service のアプリは、常時 1 つの App Service プランにのみ関連付けることができます。

アプリとプランは両方とも**リソース グループ**に含まれます。 リソース グループは、そのグループに含まれるすべてのリソースに対してライフサイクルの境界という機能を果たします。 リソース グループを使用することによって、1 つのアプリケーションのすべての構成要素をまとめて管理できます。

App Service プランは 1 つのリソース グループに複数割り当てることができるため、複数のアプリをそれぞれ異なる物理リソースに割り当てることができます。

たとえば開発、テスト、運用の各環境間でリソースを分離することができます。 運用と開発/テスト用に別個の環境を用意することで、リソースを分離できます。 この方法なら、アプリの新しいバージョンに対する負荷テストによって、実際の顧客に提供している運用環境のアプリ用のリソースが使用されるようなことはありません。

また、1 つのリソース グループに複数のプランを割り当てることによって、複数の地理的リージョンにまたがるアプリケーションを定義することもできます。

たとえば、1 つの高可用性アプリを 2 つのリージョンで実行する場合、2 つ以上のプラン (リージョンごとに 1 つ) を追加したうえで、各プランにアプリを 1 つ関連付けます。 このような場合は、アプリのすべてのコピーが 1 つのリソース グループに含まれます。 複数のプランと複数のアプリを 1 つのリソース グループにまとめることで、アプリケーションの管理と統制がしやすくなり、正常性の確認も容易になります。

## <a name="create-an-app-service-plan-or-use-existing-one"></a>App Service プランを作成する場合と既存のプランを使用する場合

App Service で新しい Web アプリを作成する場合は、既存の App Service プランにアプリを配置することによってホスト リソースを共有できます。 新しいアプリが必要なリソースを持てるかどうかを判断するには、既存の App Service プランの容量と新しいアプリの予想される負荷について理解する必要があります。 リソースの過剰割り当ては、新規および既存のアプリのダウンタイムを引き起こす可能性があります。

次の場合にアプリを新しい App Service プランに分離することをお勧めします。

- アプリが多くのリソースを消費している。
- アプリが既存のプランでホストされている他のアプリとは異なるスケーリング要因を持つ。
- アプリに別の地理的リージョン内のリソースが必要である。

こうすると、アプリの新しいリソース セットを割り当てることができるため、アプリをより効果的に制御できます。

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

> [!TIP]
> App Service 環境がある場合は、 「[App Service 環境で App Service プランを作成する](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)」をご覧ください。

空の App Service プランを作成したり、またはアプリ作成の一部として作成したりすることができます。

[Azure Portal](https://portal.azure.com) で **[新規]** > **[Web + モバイル]** の順にクリックし、**[Web アプリ]** またはその他の App Service アプリの種類をクリックします。

![Azure Portal でアプリを作成します。][createWebApp]

その後、新しいアプリのための App Service プランを選択または作成できます。

 ![Create an App Service plan.][createASP]

App Service プランを作成するには、**[+ 新規作成]** をクリックし、**App Service プラン**名を入力して、適切な**場所**を選択します。 **[価格レベル]**をクリックし、サービスに適切な価格レベルを選択します。 **[すべて表示]** を選択して、**Free** や **Shared** などの価格オプションをさらに表示します。 価格レベルを選択したら、 **[選択]** をクリックします。

## <a name="move-an-app-to-a-different-app-service-plan"></a>アプリを別の App Service プランに移動する

別の App Service プランへのアプリの移動は、[Azure Portal](https://portal.azure.com) で行うことができます。 プラン間でのアプリの移動は、対象となるプランが_同じリソース グループおよび同じ地理的リージョン_に属している場合に限り可能です。

アプリを別のプランに移動するには、次の手順に従います。

- 移動するアプリに移動します。
- **[メニュー]** で、**[App Service プラン]** セクションを探します。
- **[App Service プランの変更]** を選択して処理を開始します。

**[App Service プランの変更]** で **[App Service プラン]** セレクターが表示されます。 この時点では、このアプリを移動する既存のプランを選択できます。 同じリソース グループとリージョンのプランのみが表示されます。

![App Service plan selector.][change]

各プランには価格レベルが割り当てられています。 たとえば、Free レベルから Standard レベルにサイトを移動すると、割り当てられたすべてのアプリで Standard レベルの機能とリソースを使用できるようになります。

## <a name="clone-an-app-to-a-different-app-service-plan"></a>アプリを別の App Service プランに複製する

アプリを別のリージョンに移動する場合、アプリの複製を作成するという方法もあります。 複製によって、任意のリージョンの新規または既存の App Service プランにアプリをコピーすることができます。

メニューの **[開発ツール]** セクションに **[アプリの複製]** があります。

> [!IMPORTANT]
> 複製にはいくつかの制限があります。これらの制限については、[Azure App Service アプリの複製](app-service-web-app-cloning.md)に関する記事をご覧ください。

## <a name="scale-an-app-service-plan"></a>App Service プランのスケーリング

プランは 3 つの方法でスケールできます。

- **プランの価格レベルを変更する**。 Basic レベルのプランは Standard に変換でき、割り当てられたすべてのアプリで Standard レベルの機能を使用できるようになります。
- **プランのインスタンス サイズを変更する**。 たとえば、Small インスタンスを使用する Basic レベルのプランを、Large インスタンスを使用するように変更できます。 そのプランに関連付けられているすべてのアプリでは、より大きなサイズのインスタンスによって提供される追加のメモリおよび CPU リソースを利用できるようになります。
- **プランのインスタンス数を変更する**。 たとえば、3 インスタンスへのスケール アウトに対応した Standard プランを 10 インスタンスに拡張します。 Premium プランは 20 インスタンスにスケール アウトすることができます (利用できる場合)。 そのプランに関連付けられているすべてのアプリでは、より多くのインスタンスによって提供される追加のメモリおよび CPU リソースを利用できるようになります。

価格レベルとインスタンスのサイズを変更するには、アプリまたは App Service プランのどちらかの [設定] の **[スケール アップ]** 項目をクリックします。 変更は、App Service プランに適用され、このプランによってホストされるすべてのアプリに影響を与えます。

 ![Set values to scale up an app.][pricingtier]

## <a name="app-service-plan-cleanup"></a>App Service プランのクリーンアップ

> [!IMPORTANT]
> **App Service プラン**にアプリが関連付けられていない場合でも、コンピューティング容量が引き続き予約されるため、料金が発生します。

予期しない料金を避けるために、App Service プランでホストされている最後のアプリが削除されると、残りの空の App Service プランも既定で削除されます。

## <a name="summary"></a>概要

App Service プランは、アプリ間で共有できる一連の機能と容量を表します。 App Service プランによって、特定のアプリのリソース セットへの割り当てが柔軟に行えるようになり、最適な条件で Azure リソースが利用できるようになります。 テスト環境でのコストを削減する必要がある場合、この方法によって複数のアプリでプランを共有することができます。 また、複数のリージョンおよびプランにわたってスケーリングを行い、運用環境でのスループットを最大化させることもできます。

## <a name="whats-changed"></a>変更内容

- Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
