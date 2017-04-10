---
title: "App Service 環境の作成方法"
description: "App Service 環境の作成フローの説明"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: af98ae3b425e2e0584da53721249dbcd605a17d1
ms.openlocfilehash: 8d8e1ce7758691a392fdb6d6459e04c6b4f7f5f6


---
# <a name="how-to-create-an-app-service-environment"></a>App Service Environment の作成方法
### <a name="overview"></a>概要
App Service 環境 (ASE) は Azure App Service の Premium サービス オプションであり、マルチテナント スタンプでは使用できない高度な構成機能を提供します。  ASE 機能は、基本的に、Azure App Service を顧客の仮想ネットワークにデプロイします。  App Service 環境が提供される機能の詳細については、[App Service 環境の概要][WhatisASE]に関するページを参照してください。

### <a name="before-you-create-your-ase"></a>ASE を作成する前に
変更できない事柄があることを認識しておくことが重要です。  ASE に関する次の事柄は、ASE を作成した後で変更することはできません。

* 場所
* サブスクリプション
* リソース グループ
* 使用する VNet
* 使用するサブネット 
* サブネットのサイズ

VNet を選択し、サブネットを指定するときは、将来の成長に対応できる十分な大きさになるようにします。  

### <a name="creating-an-app-service-environment"></a>App Service 環境の作成方法
ASE の作成用 UI にアクセスするには、2 つの方法があります。  1 つは Azure Marketplace で ***App Service Environment*** を検索する方法、もう 1 つは [新規]、[Web + モバイル]、[App Service Environment] の順に選択する方法です。  ASE を作成するには:

1. ASE の名前を指定します。  ASE に指定した名前は、ASE で作成されるアプリに使用されます。  たとえば、ASE の名前が appsvcenvdemo の場合、サブドメイン名は .*appsvcenvdemo.p.azurewebsites.net*になります。  ここで *mytestapp* という名前のアプリを作成した場合、この Web アプリのアドレスは *mytestapp.appsvcenvdemo.p.azurewebsites.net* になります。  ASE の名前に空白文字は使用できません。  名前に大文字を使用した場合、ドメイン名はその名前をすべて小文字で表記したバージョンになります。  ILB を使用する場合、ASE の名前はサブドメインでは使用されず、ASE の作成時に明示的に指定されます。
   
    ![][1]
2. サブスクリプションを選択します。  ASE で使用するサブスクリプションは、その ASE 内のすべてのアプリを作成するときに使用されるサブスクリプションでもあります。  サブスクリプションが異なる VNet に ASE を配置することはできません。
3. 新しいリソース グループを選択するか指定します。  ASE で使用されるリソース グループは、VNet で使用されるものと同じである必要があります。  既存の VNet を選択すると、ASE のリソース グループの選択は、VNet のリソース グループを反映するように更新されます。
   
    ![][2]
4. Virtual Network と場所を選択します。  新しい VNet を作成するか、既存の VNet を選択できます。  新しい VNet を選択した場合は、名前と場所を指定できます。 新しい VNet のアドレス範囲は 192.168.250.0/23 になり、**[既定]** という名前のサブネットが 192.168.250.0/24 として定義されます。  既存のクラシックまたは Resource Manager VNet を選択できます。  選択した [VIP の種類] によって、インターネットから ASE に直接アクセスできる (外部) か、内部ロード バランサー (ILB) を使用するかが決まります。  詳しくは、「[Using an Internal Load Balancer with an App Service Environment][ILBASE]」 (App Service 環境での内部ロード バランサーの使用) をご覧ください。  [VIP の種類] として [外部] を選択した場合は、システムが IPSSL 目的で作成する外部 IP アドレスの数を選択できます。  [内部] を選択した場合は、ASE で使用するサブドメインを指定する必要があります。  ASE は、パブリック アドレス範囲*と* RFC1918 アドレス空間の*どちらか*を使用する仮想ネットワークにデプロイできます (RFC1918 アドレス空間とは プライベート アドレスのことです)。  パブリック アドレス範囲の仮想ネットワークを使用するには、あらかじめ VNet サブネットを作成しておく必要があります。  既存の VNet を選択した場合は、ASE の作成時に新しいサブネットを作成する必要があります。  **ポータルで事前に作成したサブネットを使用することはできません。Resource Manager テンプレートを使用して ASE を作成する場合は、既存のサブネットを持つ ASE を作成できます。**  テンプレートから ASE を作成するには、[テンプレートからの App Service Environment の作成][ILBAseTemplate]に関するページと「[Azure Resource Manager テンプレートを使用して ILB ASE を作成する方法][ASEfromTemplate]」の情報を使用してください。

### <a name="details"></a>詳細
ASE には、2 つのフロント エンドと 2 つのワーカーが作成されます。  フロント エンドは HTTP/HTTPSS エンドポイントとして動作し、アプリをホストする役割を持つワーカーにトラフィックを送信します。   ASE を作成した後で数を調整でき、これらのリソース プールに関する自動スケール規則を設定することもできます。  App Service Environment の手動スケーリング、管理、および監視について詳しくは、「[App Service 環境の構成][ASEConfig]」をご覧ください。 

ASE で使用されるサブネットには、1 つの ASE のみが存在できます。  サブネットは、ASE 以外は使用できません。

### <a name="after-app-service-environment-creation"></a>App Service 環境を作成した後
ASE を作成した後は、次の項目を調整できます。

* フロントエンドの数 (最小: 2)
* ワーカーの数 (最小: 2)
* IP SSL に使用できる IP アドレスの数
* フロントエンドまたはワーカーによって使用される Compute リソースのサイズ (フロントエンドの最小サイズは P2)

App Service 環境の手動スケーリング、管理、および監視について詳しくは、「[App Service 環境の構成][ASEConfig]」をご覧ください。 

自動スケーリングについて詳しくは、[App Service 環境の自動スケールの構成方法][ASEAutoscale]に関するページをご覧ください。

データベース、ストレージなど、カスタマイズできない追加の依存関係があります。  これらは Azure によって処理され、システムに付属しています。  システム ストレージは App Service 環境全体で 500 GB までサポートされます。データベースはシステムのスケールで必要とされるサイズに応じて Azure によって調整されます。

## <a name="getting-started"></a>使用の開始
App Service 環境に関するすべての記事と作業方法は [App Service 環境の README](../app-service/app-service-app-service-environments-readme.md)を参照してください。

App Service 環境の使用を開始するには、「[App Service 環境の概要][WhatisASE]」をご覧ください。

Azure App Service プラットフォームの詳細については、[Azure App Service][AzureAppService] に関するページを参照してください。

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/



<!--HONumber=Dec16_HO3-->


