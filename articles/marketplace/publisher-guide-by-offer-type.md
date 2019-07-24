---
title: Azure Marketplace における公開オプションの選定 | Azure
description: この記事では､パートナーが Azure Marketplace にアプリを公開しようとするときに理解しておく必要がある適格性条件と公開要件を説明します｡
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/13/2018
ms.author: qianw211
ms.openlocfilehash: 9dd5b3232184170a59b82d5cafdc1a4cdcb4c172
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727997"
---
# <a name="publishing-guide-by-offer-type"></a>プランの種類別の公開ガイド

[公開オプションを選定](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)したら、オファーの提示に使用するオファーの種類を選択できます。 

オファーは、[Cloud パートナー ポータル](https://cloudpartner.azure.com)で作成、公開、および管理する公開オブジェクトです。 マーケットプレースでのオファーの提示に使用するオファーの構造 (メタデータ、成果物、およびその他のコンテンツを含む) は、*オファーの種類*によって定義されます。

オファーを作成する前に、オファーの種類を選択する必要があります。 オファーの種類は、公開するソリューション、アプリ、またはサービスの種類および Microsoft の製品とサービスとの連携方法に対応します。 

Cloud パートナー ポータルでは、1 つのオファーの種類をさまざまな方法で構成して、異なる公開オプション、行動喚起、プロビジョニング、または価格を使用することができます。 オファーの種類の公開オプションと構成は、オファーの適格性と技術面の要件に合わせて指定されます。 

オファーの作成前に、ネットショップおよびオファーの種類の適格性の要件と、公開に関する技術面の要件を確認してください。

## <a name="list-of-offer-types"></a>オファーの種類の一覧

次の表は、Azure Marketplace のオファーの種類の一覧です。

| **オファリング タイプ**    | **説明**  |
| :------------------- | :-------------------|
| [**Virtual Machines**](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) | 顧客に関連付けられているサブスクリプションに仮想アプライアンスをデプロイするときは、オファーの種類として仮想マシンを使用します。 |
| [**ソリューション テンプレート**](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates) | デプロイと構成に関して、単純な VM を超える追加のオートメーションが必要となるソリューションの場合、オファーの種類としてソリューション テンプレートを使用してください。 |
| [**マネージド アプリケーション**](https://docs.microsoft.com/azure/marketplace/marketplace-managed-apps) | 次の条件が必須であるときは、オファーの種類として Azure アプリ (マネージド アプリ) を使用します。 <br> <ul> <li> VM または完全な IaaS ベースのソリューションを使用して、サブスクリプション ベースのソリューションを顧客向けにデプロイする。 </li> <li>貴社または貴社の顧客の要件として、ソリューションをパートナーが管理しなければならない。 </li> <ul> |
| [**SaaS アプリケーション**](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide) | SaaS ベースの技術的ソリューションをサブスクリプションとして顧客が購入できるようにするには、オファーの種類として SaaS アプリを使用します。 |
| [**コンテナー オファー**](https://docs.microsoft.com/azure/marketplace/marketplace-containers) | 対象となるソリューションが、Kubernetes ベースの Azure コンテナー サービスとしてプロビジョニングされる Docker コンテナー イメージであるときは、オファーの種類としてコンテナーを使用します。 |
| [**IoT Microsoft Edge モジュール**](https://docs.microsoft.com/azure/marketplace/iot-edge-module) | Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Microsoft サービス、サードパーティー サービス、独自ソリューション固有のコードのいずれかを含めることができます。 |
| [**コンサルティング サービス**](https://docs.microsoft.com/azure/marketplace/consulting-services) | Azure Marketplace のコンサルティング サービスは、Azure の使用をサポートし、拡張するために、お客様とサービスを結ぶうえで役立ちます。 |
| [**AppSource オファー**](https://docs.microsoft.com/azure/marketplace/appsource-offer-publishing-guide) | AppSource ストアフロントには、Dynamics 365、Office 365、Power BI、Power Apps 上でビルドされるまたはそれらを拡張するオファーが含まれます。 |

最後に、シングル サインオンの要件の詳細については、リスト オプションとオファーの種類別の [**Azure AD の要件**](https://docs.microsoft.com/azure/marketplace/enable-appsource-marketplace-using-azure-ad)を参照してください。

## <a name="next-steps"></a>次の手順

*   選択したオファーの種類について、公開オプションにおける適格性の要件を確認し、オファーの選択および構成を確定する。
*   ネットショップごとの公開パターン (ソリューションをオファーの種類や構成にマップする方法など) を確認する。
*   [Cloud パートナー ポータル](https://cloudpartner.azure.com)にサインインして、オファーを作成および構成する。
