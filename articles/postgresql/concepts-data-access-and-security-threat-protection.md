---
title: Advanced Threat Protection - Azure Database for PostgreSQL - Single Server
description: データベースへの潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出する Advanced Threat Protection について説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fa136a15e4fa034307e16ef50c2b16060c5af0d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490205"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server での Advanced Threat Protection

Azure Database for PostgreSQL 用の Advanced Threat Protection では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。

> [!NOTE]
> Advanced Threat Protection は、パブリック プレビュー段階です。

Threat Protection は、高度なセキュリティ機能の統合パッケージである Advanced Threat Protection (ATP) オファリングの一部です。 Advanced Threat Protection は [Azure portal](https://portal.azure.com) または [REST API](/rest/api/postgresql) を使ってアクセスおよび管理できます。 この機能は、General Purpose サーバーとメモリ最適化サーバーでご利用いただけます。

> [!NOTE]
> Advanced Threat Protection 機能は、次の Azure Government およびソブリン クラウド リージョンでは使用 **できません**。US Gov テキサス、US Gov アリゾナ、US Gov アイオワ、US Gov バージニア、US DoD 東部、US DoD 中部、ドイツ中部、ドイツ北部、中国東部、中国東部 2。 一般的な製品の可用性については[リージョン毎の製品の可用性](https://azure.microsoft.com/global-infrastructure/services/)を確認してください。

## <a name="what-is-advanced-threat-protection"></a>Advanced Threat Protection とは

Azure Database for PostgreSQL 用の Advanced Threat Protection では、新しいセキュリティ レイヤーが提供されます。この利用によって、異常なアクティビティに関するセキュリティ アラートが提供されて、発生する潜在的脅威の検出と対応が可能になります。 不審なデータベース アクティビティ、潜在的な脆弱性、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Azure Database for PostgreSQL 用の Advanced Threat Protection では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されます。ここには不審なアクティビティの詳細が含まれていて、脅威を調査して軽減する方法に関するアクションが推奨されます。 Azure Database for PostgreSQL 用の Advanced Threat Protection を利用すると、データベースに対する潜在的な脅威への対処が簡素化されて、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要がなくなります。 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Advanced Threat Protection の概念":::

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection のアラート 
Azure Database for PostgreSQL 用の Advanced Threat Protection では、普通でなく有害な可能性がある、データベースへのアクセスやその悪用の試みであることを示す不審なアクティビティを検出し、以下のアラートをトリガーすることができます。
- **Access from unusual location (通常とは異なる場所からのアクセス)** :このアラートは、だれかが通常とは異なる地理的な場所から Azure Database for PostgreSQL サーバーにログオンしたことで Azure Database for PostgreSQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from unusual Azure data center (通常とは異なる Azure データ センターからのアクセス)** :このアラートは、だれかかが最近このサーバーで記録された通常とは異なる Azure データ センターからサーバーにログオンしたことで Azure Database for PostgreSQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートによって、正当なアクション (Azure、Power BI、Azure Database for PostgreSQL クエリ エディターの新しいアプリケーション) が検出されることがあります。 別のケースでは、このアラートによって Azure リソース/サービス (元従業員、外部の攻撃者) からの悪意のあるアクションが検出されます。
- **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)** :このアラートは、だれかが通常とは異なるプリンシパル (Azure Database for PostgreSQL ユーザー) を使用してサーバーにログオンしたことで Azure Database for PostgreSQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)** :このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的な攻撃ツールを使用した攻撃が検出されます。
- **Brute force Azure Database for PostgreSQL credentials (Azure Database for PostgreSQL 資格情報に対するブルート フォース攻撃)** :このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートでブルート フォース攻撃が検出されます。

## <a name="next-steps"></a>次のステップ

* [Azure Security Center](../security-center/security-center-introduction.md) の詳細について参照してください
* 価格の詳細については、[Azure Database for PostgreSQL の価格ページ](https://azure.microsoft.com/pricing/details/postgresql/)をご覧ください 
* Azure portal を使用して [Azure Database for PostgreSQL の Advanced Threat Protection を構成する](howto-database-threat-protection-portal.md)