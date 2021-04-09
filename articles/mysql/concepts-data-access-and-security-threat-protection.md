---
title: Advanced Threat Protection - Azure Database for MySQL
description: データベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティを検出する Advanced Threat Protection について、その概念を説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c9e884d153e85e7b68dee38494ac5d6f4271978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542577"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL 用の Advanced Threat Protection

Azure Database for MySQL 用の Advanced Threat Protection では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。

> [!NOTE]
> Advanced Threat Protection は、パブリック プレビュー段階です。

Advanced Threat Protection は、高度なセキュリティ機能の統合パッケージである Advanced Data Security オファリングの一部です。 Advanced Threat Protection は [Azure portal](https://portal.azure.com) または [REST API](/rest/api/mysql) を使ってアクセスおよび管理できます。 この機能は、General Purpose サーバーとメモリ最適化サーバーでご利用いただけます。

> [!NOTE]
> Advanced Threat Protection 機能は、次の Azure Government およびソブリン クラウド リージョンでは使用 **できません**。US Gov テキサス、US Gov アリゾナ、US Gov アイオワ、US Gov バージニア、US DoD 東部、US DoD 中部、ドイツ中部、ドイツ北部、中国東部、中国東部 2。 一般的な製品の可用性については[リージョン毎の製品の可用性](https://azure.microsoft.com/global-infrastructure/services/)を確認してください。


## <a name="what-is-advanced-threat-protection"></a>Advanced Threat Protection とは

Azure Database for MySQL 用の Advanced Threat Protection では、新しいセキュリティ レイヤーが提供されます。これを利用することで、異常なアクティビティに関するセキュリティ アラートが提供され、潜在的脅威が生じたときに検出して対応できるようになります。 不審なデータベース アクティビティ、潜在的な脆弱性、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Azure Database for MySQL 用の Advanced Threat Protection では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されます。ここには不審なアクティビティの詳細と、脅威を調査して軽減する方法に関する推奨アクションが含まれています。 Azure Database for MySQL 用の Advanced Threat Protection を利用すると、データベースに対する潜在的な脅威への対処が簡素化されて、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要がなくなります。 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Advanced Threat Protection の概念":::

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection のアラート 
Azure Database for MySQL 用の Advanced Threat Protection では、普通でなく有害な可能性がある、データベースへのアクセスやその悪用の試みであることを示す不審なアクティビティを検出し、以下のアラートをトリガーすることができます。
- **Access from unusual location (通常とは異なる場所からのアクセス)** : このアラートは、だれかが通常とは異なる地理的な場所から Azure Database for MySQL サーバーにログオンしたことで Azure Database for MySQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from unusual Azure data center (通常とは異なる Azure データ センターからのアクセス)** : このアラートは、だれかが最近このサーバーで記録された通常とは異なる Azure データ センターからサーバーにログオンしたことで Azure Database for MySQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートによって、正当なアクション (Azure、Power BI、Azure Database for MySQL クエリ エディターの新しいアプリケーション) が検出されることがあります。 別のケースでは、このアラートによって Azure リソース/サービス (元従業員、外部の攻撃者) からの悪意のあるアクションが検出されます。
- **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)** : このアラートは、だれかが通常とは異なるプリンシパル (Azure Database for MySQL ユーザー) を使用してサーバーにログオンしたことで Azure Database for MySQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)** :このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的な攻撃ツールを使用した攻撃が検出されます。
- **Brute force Azure Database for MySQL credentials (Azure Database for MySQL 資格情報に対するブルート フォース攻撃)** : このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートでブルート フォース攻撃が検出されます。

## <a name="next-steps"></a>次のステップ

* [Azure Security Center](../security-center/security-center-introduction.md) の詳細について参照してください
* 価格の詳細については、[Azure Database for MySQL の価格ページ](https://azure.microsoft.com/pricing/details/mysql/)をご覧ください 
* Azure portal を使用して [Azure Database for MySQL の Advanced Threat Protection](howto-database-threat-protection-portal.md) を構成する