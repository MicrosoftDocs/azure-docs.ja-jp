---
title: Windows Virtual Desktop のデータの場所 - Azure
description: Windows Virtual Desktop のデータとメタデータが格納される場所についての簡単な概要。
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12ec71a86a5df5954c14097e6a0ec5c8a5138fc5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652430"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Windows Virtual Desktop のデータとメタデータの場所

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[この記事](./virtual-desktop-fall-2019/data-locations-2019.md)を参照してください。

現在、Windows Virtual Desktop はすべての地理的な場所で使用できます。 管理者は、ホスト プールの仮想マシンと関連するサービス (ファイル サーバーなど) を作成するときに、ユーザー データを格納する場所を選択できます。 Azure の地域の詳細については、[Azure データセンターのマップ](https://azuredatacentermap.azurewebsites.net/)を参照してください。

>[!NOTE]
>Microsoft が、お客様またはお客様のユーザーがユーザーとアプリ固有のデータにアクセスできるリージョンを制御または制限することはありません。

>[!IMPORTANT]
>Windows Virtual Desktop では、テナント名、ホスト プール名、アプリ グループ名、ユーザー プリンシパル名などのグローバル メタデータ情報をデータセンターに格納します。 顧客はサービス オブジェクトを作成するたびに、サービス オブジェクトの場所を入力する必要があります。 入力した場所によって、オブジェクトのメタデータが格納される場所が決まります。 顧客が Azure リージョンを選択すると、メタデータが関連する地域に格納されます。 すべての Azure リージョンと関連する地域の一覧については、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」を参照してください。

現在、以下の地域にメタデータを格納することがサポートされています。

- 米国 (US) (一般公開)
- ヨーロッパ (EU) (パブリック プレビュー) 

>[!NOTE]
> Windows Virtual Desktop サービス オブジェクトを作成するリージョンを選択する場合は、米国とヨーロッパの両方の地域に属するリージョンが表示されます。 デプロイに最適なリージョンを確認するには、[Azure グローバル インフラストラクチャ マップ](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)を参照してください。

格納されたメタデータは保存時に暗号化され、geo 冗長ミラーは地域内で保持されます。 アプリ設定やユーザー データなど、顧客データはすべて、顧客が選択した場所に配置され、サービスによって管理されることはありません。 サービスが拡大されるにつれて、より多くの地域で利用できるようになります。

サービス メタデータは、ディザスター リカバリーのために Azure の地域内にレプリケートされます。