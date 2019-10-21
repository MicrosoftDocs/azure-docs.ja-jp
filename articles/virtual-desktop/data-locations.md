---
title: Windows Virtual Desktop のデータの場所 - Azure
description: Windows Virtual Desktop のデータとメタデータが格納される場所についての簡単な概要。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
ms.openlocfilehash: 5a634f3449d88e2c1885f4a32ae2662c93811c63
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349941"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Windows Virtual Desktop のデータの場所

現在、Windows Virtual Desktop はすべての地理的な場所で使用できます。 最初は、サービス メタデータは米国 (US) の地域にのみ格納できます。 管理者は、ホスト プールの仮想マシンと関連するサービス (ファイル サーバーなど) を作成するときに、ユーザー データを格納する場所を選択できます。 Azure の地域の詳細については、[Azure データセンターのマップ](https://azuredatacentermap.azurewebsites.net/)を参照してください。

>[!NOTE]
>Microsoft が、お客様またはお客様のユーザーがユーザーとアプリ固有のデータにアクセスできるリージョンを制御または制限することはありません。

>[!IMPORTANT]
>Windows Virtual Desktop では、テナント名、ホスト プール名、アプリ グループ名、ユーザー プリンシパル名などのグローバル メタデータ情報を米国にあるデータセンターに格納します。 格納されたメタデータは保存時に暗号化され、geo 冗長ミラーは米国内で保持されます。 アプリ設定やユーザー データなど、顧客データはすべて、顧客が選択した場所に配置され、サービスによって管理されることはありません。

サービス メタデータは、ディザスター リカバリーのために米国にレプリケートされます。