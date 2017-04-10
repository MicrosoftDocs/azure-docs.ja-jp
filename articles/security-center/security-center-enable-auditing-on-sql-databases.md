---
title: "Azure Security Center での SQL Database の監査の有効化 | Microsoft Docs"
description: "このドキュメントでは、&quot;**SQL Database の監査を有効にする**&quot; という Azure Security Center の推奨事項を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: d36bb775d0b0fd732562b18787b65600c8fb5fa1
ms.lasthandoff: 02/17/2017


---
# <a name="enable-auditing-on-sql-databases-in-azure-security-center"></a>Azure Security Center での SQL Database の監査の有効化
Azure Security Center では、監査をまだ有効にしていない場合に、すべての SQL Database の監査を有効にすることを勧めます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査を有効にしたら、脅威の検出設定と、セキュリティの警告を受信する電子メールを構成できます。 脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。 これにより、発生した潜在的な脅威を検出し、対応することができます。

この推奨事項は Azure SQL サービスにのみ適用されます。仮想マシンで実行されている SQL は含まれません。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  ステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[SQL Database の監査を有効にする]** を選択します。  **[SQL データベースの監査を有効にする]** ブレードが開きます。

   ![SQL データベースの監査の有効化][1]
2. 監査を有効にする SQL Database を選択します。 **[監査と脅威検出]** ブレードが開きます。

   ![監査と脅威の検出][2]
3. **[監査と脅威検出]** ブレードで、**[監査]** の **[オン]** を選択します。

   ![監査と脅威の検出の有効化][3]
4. [Azure Portal での SQL Database の脅威の検出](../sql-database/sql-database-threat-detection-portal.md)に関するページの手順に従って、脅威の検出を有効にして構成し、異常なアクティビティの検出時にセキュリティの警告を受信する電子メールの一覧を構成します。

## <a name="see-also"></a>関連項目
この記事では、"SQL Database の監査の有効化" という Security Center の推奨事項を実装する方法について説明しました。 SQL Database のセキュリティ保護の詳細については、次の記事をご覧ください。

* [SQL Database の保護](../sql-database/sql-database-security-overview.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]:./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

