---
title: "Azure Security Center での OS の脆弱性の修復 | Microsoft Docs"
description: "このドキュメントでは、**OS の脆弱性の修復**という Azure Security Center の推奨事項を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c88dc19cdacc94b9b81adcdbf69f22aac8f6eabe
ms.lasthandoff: 03/22/2017


---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Azure Security Center での OS の脆弱性の修復
Azure Security Center では、仮想マシン (VM) のオペレーティング システム (OS) を日々分析して、攻撃に対する VM の脆弱性を高める可能性のある構成を特定し、これらの脆弱性に対処するための構成の変更を推奨しています。 Security Center では、VM の OS 構成が推奨される構成規則に適合していない場合に脆弱性を解決するよう推奨します。

> [!NOTE]
> 監視対象の具体的な構成の詳細については、[推奨される構成規則の一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)をご覧ください。 現時点では、Windows Server 2016 は完全にはサポートされていません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  ステップ バイ ステップ ガイドではありません。
>
>

1. **[推奨事項]** ブレードで、**[OS の脆弱性の修復]** を選択します。
   ![OS の脆弱性の修復][1]

    **[OS の脆弱性の修復]** ブレードが開き、OS 構成が推奨される構成規則に適合していない VM の一覧が表示されます。  ブレードには、各 VM について次の項目が示されます。

   * **[失敗した規則]** -- VM の OS 構成が失敗した規則の数。
   * **[最終スキャン時刻]** -- Security Center で VM の OS 構成が最後にスキャンされた日時。
   * **[状態]** -- 脆弱性の現在の状態。

     * [オープン]: 脆弱性にまだ対処していません。
     * [処理中]: 現在、脆弱性への対処を進めており、ユーザーのアクションは不要です。
     * [解決済み]: 脆弱性への対処は既に完了しています (問題が解決されると、エントリは淡色表示になります)。
   * **[重大度]** -- すべての脆弱性の重大度が [低] に設定されています。これは、脆弱性に対処する必要はあっても、早急な注意は必要としないことを意味します。

2. VM を選択します。 その VM のブレードが開き、失敗した規則が表示されます。
   ![失敗した構成規則][2]

3. 規則を選択します。 この例では、 **[Password must meet complexity requirements (パスワードは複雑さの要件を満たす必要がある)]**を選択します。 失敗した規則と影響について説明するブレードが開きます。 詳細を確認し、オペレーティング システムの構成を適用する方法を検討します。
  ![失敗した規則の説明][3]

  Security Center では、Common Configuration Enumeration (CCE) を使用して構成規則に一意の識別子を割り当てます。 このブレードでは次の情報が提供されます。

  - [名前] -- 規則の名前
  - [重大度] -- CCE の重大度の値 (重大、重要、または警告)
  - [CCIED] -- 規則の一意の CCE 識別子
  - [説明] -- 規則の説明
  - [脆弱性] -- 脆弱性、または規則が適用されていない場合のリスクの説明
  - [影響] -- 規則を適用したときのビジネスへの影響
  - [期待される値] -- Security Center が規則と照らし合わせて VM の OS 構成を分析したときに求められる値
  - [規則の操作] -- 規則と照らし合わせた VM の OS 構成の分析中に Security Center で使用された規則の操作
  - [実際の値] -- 規則と照らし合わせた VM の OS 構成の分析後に返された値
  - [評価の結果] -- 分析の結果 (合格、不合格)

## <a name="see-also"></a>関連項目
この記事では、"OS の脆弱性の修復" という Security Center の推奨事項を実装する方法について説明しました。 構成規則のセットは [こちら](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)で確認できます。 Security Center では、CCE (Common Configuration Enumeration) を使用して構成規則に一意の識別子を割り当てます。 詳細については、 [CCE](https://nvd.nist.gov/cce/index.cfm) サイトをご覧ください。

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png

