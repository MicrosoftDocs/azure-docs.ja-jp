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
ms.date: 08/17/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2ebb930e877c5027f4d7b0a316a7f5ebe84471b1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Azure Security Center を使用したパートナー ソリューションの監視
このドキュメントでは、Azure Security Center でパートナー ソリューションの正常性状態を監視する方法について説明します。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。 このドキュメントはステップ バイ ステップ ガイドではありません。
>
>

## <a name="monitoring-partner-solutions"></a>パートナー ソリューションの監視
**[セキュリティ センター]** ブレードの **[パートナー ソリューション]** タイルでは、Azure サブスクリプションと統合されたパートナー ソリューションの正常性状態をひと目で監視できます。

![Partner solutions tile][1]

**[パートナー ソリューション]** タイルには、サブスクリプションに統合されたパートナー ソリューションの数が表示されます。 統合されたソリューションがない場合、タイルには数字の 0 が表示されます。

パートナー ソリューションの正常性を確認するには、次の手順を実行します。

1. **[パートナー ソリューション]** タイルを選択します。 Security Center に接続されているパートナー ソリューションの一覧が表示された **[パートナー ソリューション]** ブレードが開きます。

   ![パートナー ソリューション][3]

   パートナー ソリューションの状態は、次のいずれかになります。

   * 保護済み (緑) - 正常性の問題はありません。
   * 異常 (赤) - 早急に処置が必要な正常性の問題があります。
   * レポート停止 (オレンジ) - ソリューションが正常性の報告を停止しています。
   * 不明な保護状態 (オレンジ) - 新しいリソースを既存のソリューションに追加するプロセスが失敗したため、この時点のソリューションの正常性が不明です。
   * レポートなし (灰色) - ソリューションがまだ何も報告していません。最近接続されたばかりでデプロイ中の場合、ソリューションの状態は報告されていない可能性があります。

2. パートナー ソリューションを選択します。 この例では、**[Qualys]** ソリューションを選択します。  パートナー ソリューションの状態およびソリューションの関連付けられたリソースを示すブレードが開きます。 **[ソリューション コンソール]** を選択して、このソリューションのパートナー管理エクスペリエンスを開きます。

   ![パートナー ソリューションの詳細][4]
3. **[Qualys]** ブレードに戻り、**[VM をリンクする]** を選択します。 **[アプリケーションのリンク]** ブレードが開きます。 ここで、パートナー ソリューションにリソースを接続できます。

   ![Link resources to partner solution][5]

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Security Center の **[パートナー ソリューション]** タイルについて説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 -- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 -- セキュリティの警告の管理と対応の方法について説明しています
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

