---
title: "Azure Security Center での Endpoint Protection の正常性アラートの解決 | Microsoft Docs"
description: "このドキュメントでは、&quot;Endpoint Protection の正常性アラートの解決&quot; という Azure Security Center の推奨事項を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4050f453-98fc-4314-8438-d476469757fb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e6b136d6bd3b11fb82126d104fd0cb149255118


---
# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Azure Security Center での Endpoint Protection の正常性アラートの解決
Azure Security Center では、検出されたEndpoint Protection の正常性アラートを解決するよう推奨します。  Security Center では、Endpoint Protection エラーが発生している仮想マシン (VM) とエラーの数を確認できます。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。 ステップ バイ ステップ ガイドではありません。
> 
> 

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[Endpoint Protection の正常性に関する警告を解決する]** を選択します。
   ![Endpoint Protection の正常性アラートの解決][1]
2. **[Endpoint Protection failure (Endpoint Protection エラー)]** ブレードが開き、エラーが発生している VM と各 VM のエラーの数が表示されます。 一覧から VM を選択します。
   ![Endpoint protection failure][2]
3. 選択した VM の **[Failures List (エラー リスト)]** ブレードが開き、エラーの一覧が表示されます。 一覧から詳細を確認するエラーを選択します。 選択したエラーに関する情報が含まれたブレードが開きます。
   ![エラー リスト][3]
   ![エラー イベント][4]

## <a name="see-also"></a>関連項目
セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png



<!--HONumber=Nov16_HO3-->


