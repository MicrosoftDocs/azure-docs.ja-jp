---
title: Windows Virtual Desktop (クラシック) のデータの場所 - Azure
description: Windows Virtual Desktop (クラシック) のデータとメタデータが格納される場所についての簡単な概要。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008816"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (クラシック) のデータの場所

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../data-locations.md)を参照してください。

現在、Windows Virtual Desktop はすべての地理的な場所で使用できます。 最初は、サービス メタデータは米国 (US) の地域にのみ格納できます。 管理者は、ホスト プールの仮想マシンと関連するサービス (ファイル サーバーなど) を作成するときに、ユーザー データを格納する場所を選択できます。 Azure の地域の詳細については、[Azure データセンターのマップ](https://azuredatacentermap.azurewebsites.net/)を参照してください。

>[!NOTE]
>Microsoft が、お客様またはお客様のユーザーがユーザーとアプリ固有のデータにアクセスできるリージョンを制御または制限することはありません。

>[!IMPORTANT]
>Windows Virtual Desktop では、テナント名、ホスト プール名、アプリ グループ名、ユーザー プリンシパル名などのグローバル メタデータ情報を米国にあるデータセンターに格納します。 格納されたメタデータは保存時に暗号化され、geo 冗長ミラーは米国内で保持されます。 アプリ設定やユーザー データなど、顧客データはすべて、顧客が選択した場所に配置され、サービスによって管理されることはありません。

サービス メタデータは、ディザスター リカバリーのために米国にレプリケートされます。