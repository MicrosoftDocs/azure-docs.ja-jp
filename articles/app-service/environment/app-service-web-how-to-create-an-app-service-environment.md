---
title: App Service Environment v1 の作成方法 - Azure
description: App Service Environment v1 の作成フローの説明
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9bc796c4d0d449f72dc3234bc2825554eafaf77f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339894"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>App Service Environment v1 の作成方法 

> [!NOTE]
> この記事は、App Service Environment v1 に関するものです。 より強力なインフラストラクチャ上で実行できる、使いやすい新しいバージョンの App Service Environment があります。 新しいバージョンの詳細については、「[App Service Environment の概要](intro.md)」を参照してください。
> 

### <a name="overview"></a>概要
App Service Environment (ASE) は Azure App Service の Premium サービス オプションであり、マルチテナント スタンプでは使用できない高度な構成機能を提供します。 ASE 機能は、基本的に、Azure App Service を顧客の仮想ネットワークにデプロイします。 App Service 環境が提供される機能の詳細については、[App Service 環境の概要][WhatisASE]に関するページを参照してください。

### <a name="before-you-create-your-ase"></a>ASE を作成する前に
変更できない事柄があることを認識しておくことが重要です。 ASE に関する次の事柄は、ASE を作成した後で変更することはできません。

* 場所
* サブスクリプション
* リソース グループ
* 使用する VNet
* 使用するサブネット 
* サブネットのサイズ

VNet を選択し、サブネットを指定するときは、将来の成長に対応できる十分な大きさになるようにします。 

### <a name="creating-an-app-service-environment-v1"></a>App Service Environment v1 の作成
App Service Environment v1 を作成するには、Azure Marketplace で ***App Service Environment v1*** を検索するか、または **[リソースの作成]** -> **[Web + モバイル]** -> **[App Service Environment]** を選択できます。 ASEv1 を作成するには:

1. ASE の名前を指定します。 ASE に指定する名前は、ASE で作成されたアプリに使用されます。 ASE の名前が appsvcenvdemo である場合、サブドメイン名は *appsvcenvdemo.p.azurewebsites.net* になります。 このようにして *mytestapp* という名前のアプリを作成した場合、そのアプリは *mytestapp.appsvcenvdemo.p.azurewebsites.net* でアドレス指定できます。 ASE の名前に空白文字は使用できません。 名前に大文字を使用した場合、ドメイン名はその名前をすべて小文字で表記したバージョンになります。 ILB を使用した場合、ASE 名はサブドメインでは使用されず、代わりに ASE の作成中に明示的に表明されます。
   
    ![][1]
2. サブスクリプションを選択します。 ASE に使用したサブスクリプションは、その ASE で作成するすべてのアプリにも適用されます。 ASE を別のサブスクリプション内にある VNet に配置することはできません。
3. 新しいリソース グループを選択するか指定します。 ASE で使用されるリソース グループは、VNet で使用されるものと同じである必要があります。 既存の VNet を選択した場合、ASE のリソース グループの選択は、VNet のリソース グループを反映するように更新されます。
   
    ![][2]
4. Virtual Network と場所を選択します。 新しい VNet を作成するか、既存の VNet を選択できます。 新しい VNet を選択した場合は、名前と場所を指定できます。 新しい VNet のアドレス範囲は 192.168.250.0/23 になり、**[既定]** という名前のサブネットが 192.168.250.0/24 として定義されます。 既存のクラシックまたは Resource Manager VNet を選択できます。 選択した [VIP の種類] によって、インターネットから ASE に直接アクセスできる (外部) か、内部ロード バランサー (ILB) を使用するかが決まります。 詳しくは、「[Using an Internal Load Balancer with an App Service Environment][ILBASE]」 (App Service 環境での内部ロード バランサーの使用) をご覧ください。 [VIP の種類] として [外部] を選択した場合は、システムが IPSSL 目的で作成する外部 IP アドレスの数を選択できます。 [内部] を選択した場合は、ASE で使用するサブドメインを指定する必要があります。 ASE は、パブリック アドレス範囲*と* RFC1918 アドレス空間 (つまりプライベート アドレス) の*どちらか*を使用する仮想ネットワークにデプロイできます。 パブリック アドレス範囲の仮想ネットワークを使用するには、あらかじめ VNet サブネットを作成しておく必要があります。 既存の VNet を選択した場合は、ASE の作成時に新しいサブネットを作成する必要があります。 **ポータルで事前に作成したサブネットを使用することはできません。Resource Manager テンプレートを使用して ASE を作成する場合は、既存のサブネットを持つ ASE を作成できます。** テンプレートから ASE を作成するには、[テンプレートからの App Service Environment の作成][ILBAseTemplate]に関するページと「[Azure Resource Manager テンプレートを使用して ILB ASE を作成する方法][ASEfromTemplate]」の情報を使用してください。

### <a name="details"></a>詳細
ASE には、2 つのフロント エンドと 2 つのワーカーが作成されます。 フロント エンドは HTTP/HTTPSS エンドポイントとして動作し、アプリをホストする役割を持つワーカーにトラフィックを送信します。 ASE を作成した後で数を調整でき、これらのリソース プールに関する自動スケール規則を設定することもできます。 App Service Environment の手動スケーリング、管理、監視について詳しくは、[App Service Environment の構成方法][ASEConfig]に関するページを参照してください。 

ASE で使用されるサブネットには、1 つの ASE のみが存在できます。 サブネットは、ASE 以外は使用できません。

### <a name="after-app-service-environment-v1-creation"></a>App Service Environment v1 を作成した後
ASE を作成した後は、次の項目を調整できます。

* フロントエンドの数 (最小: 2)
* ワーカーの数 (最小: 2)
* IP SSL に使用できる IP アドレスの数
* フロントエンドまたはワーカーによって使用される Compute リソースのサイズ (フロントエンドの最小サイズは P2)

App Service Environment の手動スケーリング、管理、監視について詳しくは、[App Service Environment の構成方法][ASEConfig]に関するページを参照してください。 

自動スケーリングについては、[App Service Environment の自動スケーリングを構成する方法][ASEAutoscale]に関するページを参照してください。

データベース、ストレージなど、カスタマイズできない追加の依存関係があります。 これらは Azure によって処理され、システムに付属しています。 システム ストレージは App Service 環境全体で 500 GB までサポートされます。データベースはシステムのスケールで必要とされるサイズに応じて Azure によって調整されます。

## <a name="getting-started"></a>使用の開始
App Service Environment v1 の使用を開始するには、[App Service Environment v1 の概要][WhatisASE]に関するページをご覧ください

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
