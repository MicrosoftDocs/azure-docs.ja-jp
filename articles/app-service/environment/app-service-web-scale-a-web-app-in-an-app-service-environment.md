---
title: App Service 環境内でアプリをスケーリングする方法 - Azure
description: App Service 環境内でのアプリのスケーリング
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6e683eb07b690d7d5680b7a4d429d1150f22f67e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717844"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>App Service 環境内でのアプリのスケーリング
Azure App Service では、通常、次の 3 つの項目をスケーリングできます。

* 料金プラン
* ワーカーのサイズ 
* インスタンスの数

ASE では、料金プランを選択または変更する必要はありません。  機能に関しては、既に Premium 料金機能レベルにあります。  

ワーカー サイズについては、ASE 管理者が各ワーカー プールに使用するコンピューティング リソースのサイズを割り当てることができます。  つまり、必要に応じて、ワーカー プール 1 に P4 コンピューティング リソースを割り当て、ワーカー プール 2 に P1 コンピューティング リソースを割り当てることができます。  サイズの順に従う必要はありません。  サイズとその料金設定の詳細については、「[Azure App Service の価格][AppServicePricing]」を参照してください。  結果として、App Service 環境内の Web アプリおよび App Service プランに対しては、次のスケーリング オプションが残されます。

* ワーカー プールの選択
* インスタンスの数

どちらの項目も、ASE でホストされる App Service プランに対して表示される適切な UI を使用して変更できます。  

![][1]

ASP が属しているワーカー プールで利用可能なコンピューティング リソースの数を超えて ASP をスケールアップすることはできません。  そのワーカー プールにコンピューティング リソースが必要な場合は、ASE 管理者に依頼してコンピューティング リソースを追加してもらう必要があります。  ASE を再構成する方法については、[App Service 環境の構成方法][HowtoConfigureASE]に関するページを参照してください。  また、ASE の自動スケール機能を利用することで、スケジュールまたはメトリックに基づいて容量を追加することができます。  ASE 環境自体に対して自動スケールを構成する方法の詳細については、「[App Service 環境の自動スケールの構成方法][ASEAutoscale]」を参照してください。

異なるワーカー プールまたは同じワーカー プールのコンピューティング リソースを使用して、複数の App Service プランを作成できます。  たとえば、ワーカー プール 1 に利用可能なコンピューティング リソースが 10 ある場合、6 つのコンピューティング リソースを使用する App Service プランと 4 つのコンピューティング リソースを使用する App Service プランを作成することを選択できます。

### <a name="scaling-the-number-of-instances"></a>インスタンスの数のスケーリング
App Service 環境で初めて Web アプリケーションを作成するときは、1 つのインスタンスで始まります。  スケールアウトしてインスタンスを追加することで、アプリ用の追加コンピューティング リソースを用意できます。   

ASE に十分な容量がある場合、この操作は非常に単純です。  スケールアップするサイトが保持されている App Service プランに移動し、[スケール] を選択します。  UI が開きます。ここでは、ASP のスケールを手動で設定することも、ASP の自動スケール ルールを構成することもできます。  アプリを手動でスケーリングするには、***[スケールの基準]*** を ***[手動で入力したインスタンス数]*** に設定するだけです。  あとは、スライダーを目的の数量までドラッグするか、スライダーの横のボックスに目的の値を入力します。  

![][2] 

ASE の ASP 用の自動スケール ルールは、通常と同じように機能します。  ***[スケールの基準]*** で ***[CPU の割合]*** を選択し、CPU の割合に基づいて ASP の自動スケール ルールを作成することも、***[スケジュールおよびパフォーマンスのルール]*** を使用してより複雑なルールを作成することもできます。  自動スケールの構成方法の詳細については、「[Azure App Service のアプリをスケーリングする][AppScale]」を参照してください。 

### <a name="worker-pool-selection"></a>ワーカー プールの選択
前述のように、ワーカー プールを選択するには、ASP の UI を使用します。  スケーリングする ASP 用のブレードを開き、[ワーカー プール] を選択します。  App Service 環境内に構成されているすべてのワーカー プールが表示されます。  構成されているワーカー プールが 1 つの場合は、1 つのプールが表示されます。  ASP が属しているワーカー プールを変更するには、App Service プランの移動先のワーカー プールを選択します。  

![][3]

ワーカー プール間で ASP を移動する場合は、ASP のための容量が十分に確保されているか事前に確認することが重要です。  ワーカー プールの一覧には、ワーカー プールの名前だけでなく、そのワーカー プールで使用可能なワーカーの数も表示されます。  App Service プランを格納するための十分なインスタンスがあることを確認します。  移動先のワーカー プールにより多くのコンピューティング リソースが必要な場合は、ASE 管理者に依頼してコンピューティング リソースを追加してもらう必要があります。  

> [!NOTE]
> ASP を 1 つのワーカー プールから移動すると、その ASP のアプリがコールド スタートします。  アプリが新しいコンピューティング リソースでコールド スタートするため、要求の実行が遅くなる可能性があります。  このコールド スタートは、Azure App Service の[アプリケーション ウォームアップ機能][AppWarmup]を使用することで回避できます。  アプリが新しいコンピューティング リソースでコールド スタートするときに初期化プロセスも呼び出されるため、この記事で説明しているアプリケーション初期化モジュールはコールド スタートでも有効です。 
> 
> 

## <a name="getting-started"></a>使用の開始
App Service 環境の使用を開始するには、[App Service Environment の作成方法][HowtoCreateASE]に関するページを参照してください。

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
