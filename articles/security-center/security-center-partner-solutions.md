---
title: "Azure Security Center でのパートナー ソリューションの管理 | Microsoft Docs"
description: "このドキュメントでは、Azure サブスクリプションに統合されたパートナー ソリューションの正常性状態を、Azure セキュリティ センターでひと目で監視する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 6b7d2da8ababba65146503ecfbe6fd6e142a359c
ms.openlocfilehash: 2d7ab8fbed1d4edb60416c7c6b7ae08ddbdec91a
ms.lasthandoff: 02/06/2017


---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Azure Security Center を使用したパートナー ソリューションの監視
このドキュメントでは、Azure Security Center でパートナー ソリューションの正常性状態を監視する方法について説明します。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。 ステップ バイ ステップ ガイドではありません。
>
>

## <a name="monitoring-partner-solutions"></a>パートナー ソリューションの監視
**[セキュリティ センター]** ブレードの **[パートナー ソリューション]** タイルでは、Azure サブスクリプションと統合されたパートナー ソリューションの正常性状態をひと目で監視できます。

![Partner solutions tile][1]

**[パートナー ソリューション]** タイルには、パートナー ソリューションの数と、それらのソリューションの状態の概要が表示されます。

パートナー ソリューションの **[状態]** は次のいずれかになります。

* 保護済み (緑) - 正常性の問題はありません。
* 異常 (赤) - 早急に処置が必要な正常性の問題があります。
* レポート停止 (オレンジ) - ソリューションが正常性の報告を停止しています。
* 不明な保護状態 (オレンジ) - 新しいリソースを既存のソリューションに追加するプロセスが失敗したため、この時点のソリューションの正常性が不明です。
* レポートなし (灰色) - ソリューションがまだ何も報告していません。接続されたばかりでデプロイ中の場合、ソリューションの状態は報告されていない可能性があります。

サブスクリプションに統合されたソリューションがない場合、ソリューションが存在しないことがタイルに表示されます。 **[パートナー ソリューション]** タイルを選択すると、**[推奨事項]** ブレードが開き、パートナー セキュリティ ソリューションをデプロイできます。

![No partner solutions][2]

パートナー ソリューションの正常性を確認するには、次の手順を実行します。

1. **[パートナー ソリューション]** タイルを選択します。 Security Center に接続されているパートナー ソリューションの一覧が表示されたブレードが開きます。

   ![パートナー ソリューション][3]
2. パートナー ソリューションを選択します。 この例では、 **[F5-WAF2]** ソリューションを選択します。  パートナー ソリューションの状態およびソリューションの関連付けられたリソースを示すブレードが開きます。 **[ソリューション コンソール]** を選択して、このソリューションのパートナー管理エクスペリエンスを開きます。

   ![パートナー ソリューションの詳細][4]
3. **[F5-WAF2]** ブレードに戻り、**[アプリをリンク]** を選択します。 **[アプリケーションのリンク]** ブレードが開きます。 ここで、パートナー ソリューションにリソースを接続できます。

   ![Link resources to partner solution][5]

## <a name="see-also"></a>関連項目
このドキュメントでは、Security Center の **[パートナー ソリューション]** タイルについて説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

