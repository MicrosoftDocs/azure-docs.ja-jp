---
title: "Azure Security Center でのパートナー統合 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティに強化する方法について説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 095b5c9d1a888a4061450234f80c52c5834fbf53
ms.lasthandoff: 02/16/2017


---
# <a name="partner-integration-in-azure-security-center"></a>Azure Security Center でのパートナー統合
このドキュメントでは、Azure Security Center とパートナーの統合により、パートナーの証明と課金に Azure Marketplace を活用しながら、全体的なセキュリティを強化して Azure での統合されたエクスペリエンスを実現する方法について説明します。

## <a name="why-deploy-partners-solutions-from-security-center"></a>Security Center からパートナーのソリューションをデプロイする理由

Security Center でパートナー統合を利用する主な理由は、次の 4 つです。

- **デプロイの容易さ**: Security Center の推奨事項に従うと、パートナー ソリューションのデプロイがさらに簡単になります。 デプロイ プロセスは、既定の構成とネットワーク トポロジを使用して完全に自動化できます。または、ユーザーは半自動のオプションを選択して、構成の柔軟性とカスタマイズ性を高めることができます。
- **統合された検出機能**: パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**: 統合された正常性イベントでは、ユーザーはすべてのパートナー ソリューションを一覧で監視できます。 基本的な管理は、パートナー ソリューションを使用した高度な構成への簡単なアクセスと共に使用できます。
- **SIEM へのエクスポート**: ユーザーは、Microsoft Azure ログ統合 (プレビュー) を使用して、Security Center とパートナーのすべての警告をオンプレミスの SIEM システムに CEF 形式でエクスポートできるようになりました。


## <a name="what-partners-are-integrated-with-security-center"></a>Security Center と統合されているパートナー
Security Center は現在、次のパートナーと統合されています。

- エンドポイント保護 (Trend Micro) 
- Web アプリケーション ファイアウォール (Barracuda、F5、Imperva のほか、間もなく Microsoft WAF と Fortinet が追加) 
- 次世代ファイアウォール ソリューション (Check Point、Barracuda のほか、間もなく Fortinet と Cisco が追加) 
- 脆弱性評価ソリューション (Qualys - プレビュー) 

Security Center では、これらの既存のカテゴリに含まれるパートナーの数を徐々に拡大し、新しいカテゴリを追加する予定です。 

## <a name="how-to-deploy-a-partner-solution"></a>パートナー ソリューションのデプロイ方法

Azure 環境の構成と定義されたセキュリティ ポリシーに応じて、Security Center がパートナー ソリューションのデプロイを推奨する場合があります。 推奨する際は、パートナー ソリューションの選択とインストールの手順が案内されます。 このときの全体的なデプロイ操作は、ソリューションとパートナーの種類によって異なります。 詳細については、次のリンクを参照してください。

- [Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md)
- [次世代ファイアウォールの追加](security-center-add-next-generation-firewall.md)
- [Endpoint Protection をインストールします](security-center-install-endpoint-protection.md)
- [脆弱性評価がインストールされていません](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>パートナー ソリューションの管理方法

パートナー ソリューションのデプロイが完了したら、Security Center のメイン ダッシュボードにあるパートナー ソリューションのタイルから、ソリューションの正常性に関する情報を確認したり、基本的な管理タスクを実行したりできます。 Security Center でのパートナー ソリューションの管理の詳細については、「[Monitoring partner solutions with Azure Security Center (Azure Security Center を使用したパートナー ソリューションの監視)](security-center-partner-solutions.md)」を参照してください。

![パートナー統合](./media/security-center-partner-integration/security-center-partner-integration-fig1-new.png)


## <a name="see-also"></a>関連項目
このドキュメントでは、Azure Security Center でのパートナー ソリューションの統合方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)
* [Azure Security Center の種類別のセキュリティの警告](security-center-alerts-type.md)
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

