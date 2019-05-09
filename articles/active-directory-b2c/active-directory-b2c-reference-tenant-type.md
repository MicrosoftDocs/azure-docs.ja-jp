---
title: Azure Active Directory B2C で利用可能なリージョンとデータの保存場所 | Microsoft Docs
description: Azure Active Directory B2C テナントの種類に関するトピック。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 928c6316ea964472faadc82213c4c1ff81c3e038
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608250"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C:利用可能なリージョンとデータの保存場所
利用可能なリージョンとデータの保存場所は、まったく異なる 2 つの概念です。また、Azure AD B2C とそれ以外の Azure サービスとでは、利用可能なリージョンとデータの保存場所との関係が異なります。 この記事では、2 つの概念の違いについて説明するとともに、Azure AD B2C とそれ以外の Azure サービスに対するそれらの概念の意味合いを比較します。

## <a name="summary"></a>まとめ
Azure AD B2C は**世界中で一般提供**され、**データの保存場所としては米国またはヨーロッパ**を選択することができます。

## <a name="concepts"></a>概念
* **利用可能なリージョン**とは、サービスを使用できる場所を指します。
* **データの保存場所**とは、ユーザー データがどこに保存されるかを示します。

## <a name="region-availability"></a>利用可能なリージョン
Azure AD B2C は、Azure パブリック クラウドを介して全世界で提供されています。 

この点が、利用可能な場所とデータの保存場所とが結び付いている、他の多くの Azure サービスに採用されているモデルと異なります。 その例は、Azure の「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」ページと [Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)に関するページで確認できます。

## <a name="data-residency"></a>データの保存場所
Azure AD B2C では、ユーザー データが米国またはヨーロッパに保存されます。

データの保存場所は、[Azure AD B2C テナントの作成](active-directory-b2c-get-started.md)時に、どの国/地域を選んだかによって決まります。

![プレビュー テナントのスクリーンショット](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

次の国/地域の場合、データは米国に保存されます。

> 米国、カナダ、コスタリカ、ドミニカ共和国、エルサルバドル、グアテマラ、メキシコ、パナマ、プエルトリコ、トリニダード・トバゴ

次の国/地域の場合、データはヨーロッパに保存されます。

> アルジェリア、オーストリア、アゼルバイジャン、バーレーン、ベラルーシ、ベルギー、ブルガリア、クロアチア、キプロス、チェコ共和国、デンマーク、エジプト、エストニア、フィンランド、フランス、ドイツ、ギリシャ、ハンガリー、アイスランド、アイルランド、イスラエル、イタリア、ヨルダン、カザフスタン、ケニア、クウェート、ラトビア、レバノン、リヒテンシュタイン、リトアニア、ルクセンブルク、北マケドニア、マルタ、モンテネグロ、モロッコ、オランダ、ナイジェリア、ノルウェー、オマーン、パキスタン、ポーランド、ポルトガル、カタール、ルーマニア、ロシア、サウジアラビア、セルビア、スロバキア、スロベニア、南アフリカ、スペイン、スウェーデン、スイス、チュニジア、トルコ、ウクライナ、アラブ首長国連邦、イギリス

その他の国/地域については今後追加されていく予定です。  差し当たって、上記のいずれかの国/地域を選ぶことによって Azure AD B2C を使用することができます。

> アフガニスタン、アルゼンチン、オーストラリア、ブラジル、チリ、コロンビア、エクアドル、香港特別行政区、インド、インドネシア、イラク、日本、韓国、マレーシア、ニュージーランド、パラグアイ、ペルー、フィリピン、シンガポール、スリランカ、台湾、タイ、ウルグアイ、ベネズエラ

## <a name="preview-tenant"></a>プレビュー テナント
Azure AD B2C のプレビュー期間中に B2C テナントを作成した場合は、通常、**[Tenant type (テナントの種類)]** が **[Preview tenant (プレビュー テナント)]** になります。 その場合は、開発とテストの目的のみにテナントを使用する必要があります。運用アプリ用には使用しないでください。

> [!IMPORTANT]
> プレビュー B2C テナントから運用スケール B2C テナントへの移行パスはありません。 プレビュー B2C テナントを削除し、同じドメイン名で運用スケール B2C テナントを再作成する場合に、既知の問題があることに注意してください。 運用スケール B2C テナントは異なるドメイン名で作成する必要があります。


![プレビュー テナントのスクリーンショット](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
