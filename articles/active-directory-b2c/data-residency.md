---
title: 利用可能なリージョンとデータの保存場所
titleSuffix: Azure AD B2C
description: 利用可能なリージョン、データの保存場所、Azure Active Directory B2C プレビュー テナントに関する情報。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: bb9c6dbf9984ec81fbd4b93a61552211928d0f0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388716"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C:利用可能なリージョンとデータの保存場所

利用可能なリージョンとデータの保存場所は、まったく異なる 2 つの概念です。また、Azure AD B2C とそれ以外の Azure サービスとでは、利用可能なリージョンとデータの保存場所との関係が異なります。 この記事では、これら 2 つの概念の違いについて説明するとともに、Azure と Azure AD B2C に対してそれらがどのように適用されるかを比較します。

Azure AD B2C は**世界中で一般提供**されており、**データの保存場所**としては**米国、ヨーロッパ、またはアジア太平洋**を選択することができます。

[利用可能なリージョン](#region-availability)とは、サービスを使用できる場所を指します。

[データの保存場所](#data-residency)とは、ユーザー データがどこに保存されるかを示します。

## <a name="region-availability"></a>利用可能なリージョン

Azure AD B2C は、Azure パブリック クラウドを介して全世界で提供されています。

この点が、通常は*利用可能な場所*と*データの保存場所*とが結び付いている、他のほとんどの Azure サービスに採用されているモデルと異なります。 その例は、Azure の「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」ページと [Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)に関するページで確認できます。

## <a name="data-residency"></a>データの保存場所

Azure AD B2C では、ユーザー データが米国、ヨーロッパ、またはアジア太平洋のリージョンに保存されます。

データの保存場所は、[Azure AD B2C のテナントを作成する](tutorial-create-tenant.md)ときに選択した国/地域によって決まります。

![[テナントの作成] フォームのスクリーンショット ([国またはリージョン] の選択)。](./media/data-residency/data-residency-b2c-tenant.png)

次の国/地域の場合、データは**米国**に保存されます。

> 米国、カナダ、コスタリカ、ドミニカ共和国、エルサルバドル、グアテマラ、メキシコ、パナマ、プエルトリコ、トリニダード・トバゴ

次の国/地域の場合、データは**ヨーロッパ**に保存されます。

> アルジェリア、オーストリア、アゼルバイジャン、バーレーン、ベラルーシ、ベルギー、ブルガリア、クロアチア、キプロス、チェコ共和国、デンマーク、エジプト、エストニア、フィンランド、フランス、ドイツ、ギリシャ、ハンガリー、アイスランド、アイルランド、イスラエル、イタリア、ヨルダン、カザフスタン、ケニア、クウェート、ラトビア、レバノン、リヒテンシュタイン、リトアニア、ルクセンブルク、北マケドニア、マルタ、モンテネグロ、モロッコ、オランダ、ナイジェリア、ノルウェー、オマーン、パキスタン、ポーランド、ポルトガル、カタール、ルーマニア、ロシア、サウジアラビア、セルビア、スロバキア、スロベニア、南アフリカ、スペイン、スウェーデン、スイス、チュニジア、トルコ、ウクライナ、アラブ首長国連邦、イギリス

次の国/地域の場合、データは**アジア太平洋**に保存されます。

> アフガニスタン、香港特別行政区、インド、インドネシア、日本、韓国、マレーシア、フィリピン、シンガポール、スリランカ、台湾、タイ

以下の国/地域については今後追加されていく予定です。 差し当たって、上記のいずれかの国/地域を選ぶことによって Azure AD B2C を使用することができます。

> アルゼンチン、オーストラリア、ブラジル、チリ、コロンビア、エクアドル、イラク、ニュージーランド、パラグアイ、ペルー、ウルグアイ、ベネズエラ

## <a name="preview-tenant"></a>プレビュー テナント

Azure AD B2C のプレビュー期間中に B2C テナントを作成した場合は、通常、 **[テナントの種類]** が **[Preview tenant (プレビュー テナント)]** になります。

その場合は、開発とテストの目的のみにテナントを使用する必要があります。 運用アプリケーションにはプレビュー テナントを使わないでください。

プレビュー B2C テナントから運用スケール B2C テナントへの**移行パスはありません**。 運用アプリケーション用の新しい B2C テナントを作成する必要があります。

プレビュー B2C テナントを削除し、同じドメイン名で運用スケール B2C テナントを作成する場合、既知の問題があります。 *運用スケール B2C テナントは異なるドメイン名で作成する必要があります*。

![[テナントの種類] (プレビュー テナント) のスクリーンショット。](./media/data-residency/preview-b2c-tenant.png)