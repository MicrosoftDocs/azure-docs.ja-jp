---
title: Advanced Threat Protection - Azure Database for MariaDB | Microsoft Docs
description: Advanced Threat Protection は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a96d8c3b9f9e3f9b1aedf269bce141da3e4ccf42
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869378"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB Advanced Threat Protection

Azure Database for MariaDB 用の Advanced Threat Protection は、データベースにアクセスしたりそれを悪用したりしようとする、通常とは異なり、かつ有害なおそれのある試みを示す異常なアクティビティを検出します。

> [!IMPORTANT]
> Advanced Threat Protection は、パブリック プレビュー段階です。

Advanced Threat Protection は、高度なセキュリティ機能の統合パッケージである Advanced Data Security オファリングの一部です。 Advanced Threat Protection は、[Azure portal](https://portal.azure.com) 経由でアクセスおよび管理できます。 この機能は、General Purpose サーバーとメモリ最適化サーバーでご利用いただけます。

> [!NOTE]
> Advanced Threat Protection 機能は、次の Azure 政府およびソブリン クラウド リージョンでは使用**できません**。US Gov テキサス、US Gov アリゾナ、US Gov アイオワ、US Gov バージニア、US DoD 東部、US DoD 中部、ドイツ中部、ドイツ北部、中国東部、中国東部 2。 一般的な製品の可用性については[リージョン毎の製品の可用性](https://azure.microsoft.com/global-infrastructure/services/)を確認してください。


## <a name="what-is-advanced-threat-protection"></a>Advanced Threat Protection とは

Azure Database for MariaDB 用の Advanced Threat Protection では、セキュリティの新しいレイヤーが提供されます。これにより、異常なアクティビティに関するセキュリティ アラートが提供されるため、潜在的な脅威が発生したとき、顧客はそれらの脅威を検出してそれに対応できるようになります。 不審なデータベース アクティビティ、潜在的な脆弱性、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Azure Database for MariaDB 用の Advanced Threat Protection では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されます。これには、疑わしいアクティビティの詳細が含まれ、脅威を調査して軽減する方法に関するアクションが推奨されます。 Azure Database for MariaDB 用の Advanced Threat Protection を使用すると、セキュリティの専門家でなくても、また高度なセキュリティ監視システムを管理しなくても、データベースへの潜在的な脅威に容易に対処できます。 

![Advanced Threat Protection の概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection のアラート 
Azure Database for MariaDB 用の Advanced Threat Protection は、データベースにアクセスしたりそれを悪用したりしようとする、通常とは異なり、かつ有害なおそれのある試みを示す異常なアクティビティを検出します。さらに、次のアラートをトリガーできます。
- **Access from unusual location (通常とは異なる場所からのアクセス)** :このアラートは、だれかが通常とは異なる地理的な場所から Azure Database for MariaDB サーバーにログオンしたことにより、Azure Database for MariaDB サーバーへのアクセス パターンに変化が発生したときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from unusual Azure data center (通常とは異なる Azure データ センターからのアクセス)** :このアラートは、だれかが最近 Azure Database for MariaDB サーバー上で確認された通常とは異なる Azure データ センターからサーバーにログオンしたことにより、このサーバーへのアクセス パターンに変化が発生したときにトリガーされます。 場合によっては、このアラートによって正当なアクションが検出されます (Azure、Power BI での新しいアプリケーション)。 別のケースでは、このアラートによって Azure リソース/サービス (元従業員、外部の攻撃者) からの悪意のあるアクションが検出されます。
- **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)** :このアラートは、だれかが通常とは異なるプリンシパル (Azure Database for MariaDB ユーザー) を使用してサーバーにログオンしたことにより、Azure Database for MariaDB サーバーへのアクセス パターンに変化が発生したときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)** :このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的な攻撃ツールを使用した攻撃が検出されます。
- **Brute force Azure Database for MariaDB credentials (Azure Database for MariaDB 資格情報のブルート フォース攻撃)** : このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートでブルート フォース攻撃が検出されます。

## <a name="next-steps"></a>次の手順

* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について参照してください
* 価格の詳細については、「[Azure Database for MariaDB の価格](https://azure.microsoft.com/pricing/details/mariadb/)」ページを参照してください 
* Azure portal を使用して [Azure Database for MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) を構成する  
