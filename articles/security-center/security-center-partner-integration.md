---
title: "Azure Security Center でのパートナー統合 | Microsoft Docs"
description: "Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティを強化する方法について説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: ja-jp
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Azure Security Center でのパートナー統合

この記事では、Azure Security Center とパートナーの統合によって全体的なセキュリティを強化する方法について説明します。 Security Center では、Azure の統合されたエクスペリエンスを実現し、パートナーの認定と課金に Azure Marketplace を活用します。

> [!NOTE] 
> 2017 年 6 月時点で、Security Center は、データの収集と格納に Microsoft Monitoring Agent を使用しています。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Security Center からパートナー ソリューションをデプロイする理由

Security Center でパートナー統合を利用する主な理由は、次の 4 つです。

- **デプロイの容易さ**。 Security Center の推奨事項に従うと、パートナー ソリューションのデプロイはさらに簡単になります。 デプロイ プロセスは、既定の設定とネットワーク トポロジを使用して完全に自動化できます。 また、ユーザーは、柔軟性の向上やさらなるカスタマイズのために、半自動のオプションを選択することもできます。
- **統合された検出機能**。 パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**。 ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。
- **SIEM へのエクスポート**。 ユーザーは、Azure ログ統合 (プレビュー) を使用して、Security Center とパートナーの警告すべてをオンプレミスのセキュリティ情報およびイベント管理 (SIEM) システムに共通イベント形式 (CEF) でエクスポートできます。


## <a name="partners-that-integrate-with-security-center"></a>Security Center と統合されるパートナー

現在、Security Center は次のソリューションと統合されています。

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec、[Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Web アプリケーション ファイアウォール ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)、[Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- 次世代ファイアウォール ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- 脆弱性評価 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Security Center では、上記のカテゴリに含まれるパートナーの数を徐々に増やし、新しいカテゴリを追加する予定です。 

## <a name="deploy-a-partner-solution"></a>パートナー ソリューションのデプロイ

ご利用の Azure 環境の設定と定義したセキュリティ ポリシーに応じて、Security Center はパートナー ソリューションのデプロイを推奨する場合があります。 Security Center の推奨事項により、パートナー ソリューションの選択とインストールの手順が案内されます。 全体的なデプロイ操作は、使用するソリューションとパートナーの種類によって異なる場合があります。 詳細については、次の記事を参照してください。

- [[Endpoint Protection のインストール]](security-center-install-endpoint-protection.md)
- [Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md)
- [次世代ファイアウォールの追加](security-center-add-next-generation-firewall.md)
- [脆弱性評価がインストールされていません](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>パートナー ソリューションの管理

デプロイ後、ソリューションの正常性に関する情報を表示したり、基本的な管理タスクを実行したりするには、**[Security Center]** ブレードで **[パートナー ソリューション]** オプションを選択します。 Security Center でのパートナー ソリューションの管理の詳細については、[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)に関する記事を参照してください。

![パートナー統合](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> Symantec Endpoint Protection のサポートは検出に制限されています。 正常性アラートは使用できません。
>

## <a name="see-also"></a>関連項目

この記事では、Azure Security Center でのパートナー ソリューションの統合方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Security Center でセキュリティのアラートの管理と対応を行う](security-center-managing-and-responding-alerts.md)
* [Security Center における各種セキュリティ アラート](security-center-alerts-type.md)
* [Security Center でのセキュリティ正常性の監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。 パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center の FAQ](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

