---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133400"
---
ゾーン冗長ストレージ (ZRS) は、1 つのリージョン内の 3 つのストレージ クラスターにデータを同期してレプリケートします。 各ストレージ クラスターは物理的に他のストレージ クラスターと分離されており、それぞれの可用性ゾーン (AZ) 内にあります。 各可用性ゾーンは&mdash;その中の ZRS クラスターとともに&mdash;自律的であり、独立したユーティリティとネットワーク機能を備えています。 ZRS ストレージ アカウントへの書き込み要求は、3 つのクラスターにまたがってすべてのレプリカにデータが書き込まれた後にのみ、正常に返されます。

ZRS レプリケーションを使用してストレージ アカウントにデータを格納した場合、可用性ゾーンが使用できなくなったときに、データに引き続きアクセスして管理することができます。 ZRS はパフォーマンスに優れ、短い待機時間です。 ZRS は、[ローカル冗長ストレージ (LRS)](../articles/storage/common/storage-redundancy-lrs.md) と同じ[スケーラビリティ ターゲット](../articles/storage/common/storage-scalability-targets.md)を実現します。

一貫性、持続性、および高可用性を必要とするシナリオの場合は ZRS を検討してください。 停電または自然災害によって可用性ゾーンが利用できない場合でも、ZRS は、ストレージ オブジェクトに年間 99.9999999999% (トゥエルブ ナイン) 以上の持続性を実現します。

可用性ゾーンの詳細については、[Availability Zones の概要](https://docs.microsoft.com/azure/availability-zones/az-overview)に関するページを参照してください。