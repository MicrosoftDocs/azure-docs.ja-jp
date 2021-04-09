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
ms.date: 10/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 9cb7a97b3f57ee7ac10babc53ee2263d51838777
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92309680"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C:利用可能なリージョンとデータの保存場所

利用可能なリージョンとデータの保存場所は、まったく異なる 2 つの概念です。また、Azure AD B2C とそれ以外の Azure サービスとでは、利用可能なリージョンとデータの保存場所との関係が異なります。 この記事では、これら 2 つの概念の違いについて説明するとともに、Azure と Azure AD B2C に対してそれらがどのように適用されるかを比較します。

Azure AD B2C は **世界中で一般提供** されており、**データの保存場所** としては **米国、ヨーロッパ、またはアジア太平洋** を選択することができます。

[利用可能なリージョン](#region-availability)とは、サービスを使用できる場所を指します。

[データの保存場所](#data-residency)とは、ユーザー データがどこに保存されるかを示します。

## <a name="region-availability"></a>利用可能なリージョン

Azure AD B2C は、Azure パブリック クラウドを介して全世界で提供されています。

この点が、通常は *利用可能な場所* と *データの保存場所* とが結び付いている、他のほとんどの Azure サービスに採用されているモデルと異なります。 その例は、Azure の「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」ページと [Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)に関するページで確認できます。

## <a name="data-residency"></a>データの保存場所

Azure AD B2C では、ユーザー データが米国、ヨーロッパ、またはアジア太平洋のリージョンに保存されます。

データの保存場所は、[Azure AD B2C のテナントを作成する](tutorial-create-tenant.md)ときに選択した国/地域によって決まります。

![[テナントの作成] フォームのスクリーンショット ([国またはリージョン] の選択)。](./media/data-residency/data-residency-b2c-tenant.png)

次の国/地域の場合、データは **米国** に保存されます。

> 米国 (US)、カナダ (CA)、コスタリカ (CR)、ドミニカ共和国 (DO)、エルサルバドル (SV)、グアテマラ (GT)、メキシコ (MX)、パナマ (PA)、プエルトリコ (PR)、トリニダード・トバゴ (TT)

次の国/地域の場合、データは **ヨーロッパ** に保存されます。

> アルジェリア (DZ)、オーストリア (AT)、アゼルバイジャン (AZ)、バーレーン (BH)、ベラルーシ (BY)、ベルギー (BE)、ブルガリア (BG)、クロアチア (HR)、キプロス (CY)、チェコ共和国 (CZ)、デンマーク (DK)、エジプト (EG)、エストニア (EE)、フィンランド (FT)、フランス (FR)、ドイツ (DE)、ギリシャ (GR)、ハンガリー (HU)、アイスランド (IS)、アイルランド (IE)、イスラエル (IL)、イタリア (IT)、ヨルダン (JO)、カザフスタン (KZ)、ケニア (KE)、クウェート (KW)、ラトビア (LV)、レバノン (LB)、リヒテンシュタイン (LI)、リトアニア (LT)、ルクセンブルク (LU)、北マケドニア (ML)、マルタ (MT)、モンテネグロ (ME)、モロッコ (MA)、オランダ (NL)、ナイジェリア (NG)、ノルウェー (NO)、オマーン (OM)、パキスタン (PK)、ポーランド (PL)、ポルトガル (PT)、カタール (QA)、ルーマニア (RO)、ロシア (RU)、サウジアラビア (SA)、セルビア (RS)、スロバキア (SK)、スロベニア (ST)、南アフリカ (ZA)、スペイン (ES)、スウェーデン (SE)、スイス (CH)、チュニジア (TN)、トルコ (TR)、ウクライナ (UA)、アラブ首長国連邦 (AE)、英国 (GB)

次の国/地域の場合、データは **アジア太平洋** に保存されます。

> アフガニスタン (AF)、香港特別行政区 (HK)、インド (IN)、インドネシア (ID)、日本 (日本)、韓国 (韓国)、マレーシア (MY)、フィリピン (PH)、シンガポール (SG)、スリランカ (LK)、台湾 (TW)、タイ (TH)。

以下の国/地域については今後追加されていく予定です。 差し当たって、上記のいずれかの国/地域を選ぶことによって Azure AD B2C を使用することができます。

> アルゼンチン、オーストラリア、ブラジル、チリ、コロンビア、エクアドル、イラク、ニュージーランド、パラグアイ、ペルー、ウルグアイ、ベネズエラ

## <a name="remote-profile-solution"></a>リモート プロファイルのソリューション

Azure AD B2C の[カスタム ポリシー](custom-policy-overview.md)を使用すると、[RESTful API サービス](custom-policy-rest-api-intro.md)と統合できます。これにより、リモート データベース (マーケティング データベース、CRM システム、または任意の基幹業務アプリケーションなど) からユーザー プロファイルを格納して読み取ることができます。  
- サインアップとプロファイル編集のフロー中に、Azure AD B2C からカスタム REST API が呼び出され、ユーザー プロファイルをリモート データ ソースに保持します。 ユーザーの資格情報は、Azure AD B2C ディレクトリに格納されます。 
- サインイン時に、ローカルまたはソーシャル アカウントを使用して資格情報を検証した後、Azure AD B2C によって REST API が呼び出されます。これにより、ユーザーの一意の識別子がユーザーのプライマリ キー (電子メール アドレスまたはユーザーの objectId) として送信されます。 REST API によって、リモート データベースからデータが読み取られ、ユーザー プロファイルが返されます。  

サインアップ、プロファイルの編集、またはサインインを完了すると、Azure AD B2C のアクセス トークンにはユーザー プロファイルが含まれ、これがアプリケーションに返されます。 詳細については、GitHub の [Azure AD B2C リモート プロファイルのサンプル ソリューション](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)に関するページを参照してください。

## <a name="preview-tenant"></a>プレビュー テナント

Azure AD B2C のプレビュー期間中に B2C テナントを作成した場合は、通常、 **[テナントの種類]** が **[Preview tenant (プレビュー テナント)]** になります。

その場合は、開発とテストの目的のみにテナントを使用する必要があります。 運用アプリケーションにはプレビュー テナントを使わないでください。

プレビュー B2C テナントから運用スケール B2C テナントへの **移行パスはありません**。 運用アプリケーション用の新しい B2C テナントを作成する必要があります。

プレビュー B2C テナントを削除し、同じドメイン名で運用スケール B2C テナントを作成する場合、既知の問題があります。 *運用スケール B2C テナントは異なるドメイン名で作成する必要があります*。

![[テナントの種類] (プレビュー テナント) のスクリーンショット。](./media/data-residency/preview-b2c-tenant.png)

## <a name="next-steps"></a>次の手順

- [Azure AD B2C テナントを作成](tutorial-create-tenant.md)します。
