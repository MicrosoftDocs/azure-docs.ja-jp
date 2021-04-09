---
title: App Service Environment での可用性ゾーンのサポート
description: アプリがゾーン冗長になるように App Service Environment をデプロイする方法について説明します。
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624727"
---
# <a name="availability-zone-support-for-app-service-environments"></a>App Service Environment での可用性ゾーンのサポート

App Service Environment (ASE) は、Availability Zones (AZ) にデプロイできます。  ユーザーは、Azure リージョン内の特定の AZ に内部ロード バランサー (ILB) ASE をデプロイできます。 ILB ASE を特定の AZ に固定すると、ILB ASE によって使用されるリソースは、指定した AZ に固定されるか、ゾーン冗長の形式でデプロイされます。  

ILB ASE は特定のゾーンに固定されるため、AZ に明示的にデプロイされた ILB ASE は、ゾーンのリソースと見なされます。 次の ILB ASE の依存関係は、指定されたゾーンに固定されます。

- ASE の内部ロード バランサーの IP アドレス
- Web アプリケーションを管理および実行するために ASE によって使用されるコンピューティング リソース

ゾーン ILB ASE にデプロイされた Web アプリケーションのリモート ファイル ストレージには、ゾーン冗長ストレージ (ZRS) が使用されます。

この記事で説明されている手順に従わない場合、ILB ASE はゾーンの形式で自動的にデプロイされません。 パブリック IP アドレスを持つ外部 ASE を特定の可用性ゾーンに固定することはできません。 

ゾーン ILB ASE は、次のいずれかのリージョンで作成できます。

- オーストラリア東部
- カナダ中部
- 米国中部
- 米国東部
- 米国東部 2
- 米国東部 2 (EUAP)
- フランス中部 
- 東日本
- 北ヨーロッパ
- 西ヨーロッパ
- 東南アジア
- 英国南部
- 米国西部 2

ゾーン ILB ASE にデプロイされたアプリケーションは、同じリージョン内の他のゾーンで障害が発生した場合でも、引き続き実行され、その ASE でトラフィックが処理されます。  非実行時の動作 (アプリケーション サービス プランのスケーリング、アプリケーションの作成、アプリケーションの構成、およびアプリケーションの発行を含む) では、他の可用性ゾーンの障害から影響を受ける可能性があります。 ゾーンに固定されたゾーン ILB ASE のデプロイでは、既にデプロイされているアプリケーションの継続的なアップタイムのみが保証されます。

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>可用性ゾーンに App Service Environment をデプロイする方法 ##

ゾーン ILB ASE は、Resource Manager テンプレートを使用して作成する必要があります。 Resource Manager テンプレートを使用してゾーン ILB ASE が作成されたら、Azure portal と CLI を使用して表示および操作することができます。  Resource Manager テンプレートは、ゾーン ILB ASE を最初に作成するときにのみ必要となります。

ゾーン ILB ASE を指定するために Resource Manager テンプレートに必要な変更は、新しい ***zones** _ プロパティのみです。 _ *_zones_** プロパティには、ILB ASE を固定する論理可用性ゾーンに応じて、値 "1"、"2"、または "3" を設定する必要があります。

次の Resource Manager テンプレートのスニペットの例は、新しい ***zones*** プロパティを示しており、ILB ASE をゾーン 2 に固定する必要があることを指定しています。

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

アプリがゾーン冗長になるようにするには、2 つのゾーン ILB ASE をデプロイする必要があります。 2 つのゾーン ILB ASE は、別々の可用性ゾーンに存在する必要があります。 次に、それぞれの ILB ASE にアプリをデプロイする必要があります。 アプリが作成されたら、負荷分散ソリューションを構成する必要があります。 推奨されるソリューションは、ゾーン ILB ASE の[ゾーン冗長 Application Gateway](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) アップストリームをデプロイすることです。 

## <a name="in-region-data-residency"></a>リージョンのデータの保存場所 ##

可用性ゾーンにデプロイされた ILB ASE には、ゾーン ILB ASE がデプロイされているリージョン内のユーザー データのみが格納されます。 Web サイトのファイル コンテンツ、ユーザーが指定した設定、および App Service に格納されているシークレットは、いずれもゾーン ILB ASE がデプロイされているリージョン内に残ります。

ユーザーは、「可用性ゾーンに App Service Environment をデプロイする方法」の項で説明した手順に従って、単一リージョンのデータの保存場所となるようにしてください。 これらの手順に従って App Service Environment を構成することにより、可用性ゾーンにデプロイされた App Service Environment で、リージョンのデータの保存場所の要件が満たされます ([Azure トラスト センター](https://azuredatacentermap.azurewebsites.net/)に指定されている要件を含む)。

ユーザーは、次の手順に従って、1 つのリージョンにデータが格納されるように App Service Environment が適切に構成されていることを検証できます。 

1. [リソース エクスプローラー](https://resources.azure.com)を使用して、App Service Environment の ARM リソースに移動します。  ASE は *providers/Microsoft. Web/hostingEnvironments* の下に一覧表示されます。
2. *zones* プロパティが ARM JSON 構文のビューに存在し、値が "1"、"2"、または "3" の単一値の JSON 配列が含まれている場合、ASE はゾーン形式でにデプロイされており、ユーザー データは同じリージョンに残ります。
2. *zones* プロパティが存在しない場合、または前に指定した有効なゾーン値がプロパティにない場合、ASE はゾーン形式でデプロイされておらず、ユーザー データは同じリージョンに排他的に格納されません。
