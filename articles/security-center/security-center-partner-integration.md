---
title: Azure Security Center でのセキュリティ ソリューションの統合 | Microsoft Docs
description: Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティを強化する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: dd694fd013069c33e4f3af2c81447e014d41b691
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519244"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center でのセキュリティ ソリューションの統合
このドキュメントは、既に Azure Security Center に接続されているセキュリティ ソリューションを管理したり、新しいセキュリティ ソリューションを追加したりする際に役立ちます。

## <a name="integrated-azure-security-solutions"></a>統合された Azure セキュリティ ソリューション
Security Center を使用すると、Azure で統合されたセキュリティ ソリューションを簡単に有効にすることができます。 利点は次のとおりです。

- **簡略化されたデプロイ**:Security Center により、統合されたパートナー ソリューションのプロビジョニングが簡略化されます。 マルウェア対策や脆弱性評価などのソリューションについては、Security Center によって仮想マシンにエージェントをプロビジョニングできます。 ファイアウォール アプライアンスについては、Security Center で、必要なネットワーク構成の多くに対処できます。
- **統合された検出機能**:パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**:ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。

現時点では、統合セキュリティ ソリューションに、[Qualys](https://www.qualys.com/public-cloud/#azure) と [Rapid7](https://www.rapid7.com/products/insightvm/) による脆弱性評価と Microsoft Application Gateway Web アプリケーション ファイアウォールが含まれます。

> [!NOTE]
> ほとんどのセキュリティ ベンダーがアプライアンス上での外部エージェントの実行を禁止しているため、Security Center はパートナー仮想アプライアンスに Log Analytics エージェントをインストールしません。

Standard レベルのお客様が利用できる組み込みのスキャナーなど、Qualys の脆弱性スキャン ツールの統合の詳細については、以下を参照してください。 

- [仮想マシン向けの統合された脆弱性スキャナー](built-in-vulnerability-assessment.md)。
- [パートナーの脆弱性スキャン ソリューションをデプロイする](partner-vulnerability-assessment.md)。

Security Center では、次の脆弱性分析も提供しています：

* SQL データベース - 「[脆弱性評価ダッシュボードで脆弱性評価レポートの探索をする](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports) 」を参照する
* Azure Container Registry イメージ - 「[Azure Container Registry を Security Center と統合する （プレビュー）](azure-container-registry-integration.md)」を参照する

## <a name="how-security-solutions-are-integrated"></a>セキュリティ ソリューションを統合するしくみ
Security Center からデプロイされている Azure セキュリティ ソリューションは自動的に接続されます。 オンプレミスまたは他のクラウドで実行されているコンピューターなど、その他のセキュリティ データ ソースも接続できます。

[![パートナー ソリューションの統合](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>統合された Azure セキュリティ ソリューションとその他のデータ ソースの管理

1. [Azure portal](https://azure.microsoft.com/features/azure-portal/) から **Security Center** を開きます。

1. Security Center のメニューから **[セキュリティ ソリューション]** を選択します。

**[セキュリティ ソリューション]** ページから、統合された Azure セキュリティ ソリューションの正常性を確認し、基本的な管理タスクを実行できます。

### <a name="connected-solutions"></a>接続済みソリューション

**[接続済みソリューション]** セクションには、Security Center に現在接続されているセキュリティ ソリューションが含まれます。 また、各ソリューションの正常性状態も表示されます。  

![接続済みソリューション](./media/security-center-partner-integration/connected-solutions.png)

パートナー ソリューションの状態は、次のいずれかになります。

* **正常** (緑) - 正常性の問題はありません。
* **異常** (赤) - 早急な対処が必要な正常性の問題があります。
* **レポート停止** (オレンジ) - ソリューションが正常性の報告を停止しています。
* **レポートなし** (灰色) - ソリューションがまだ何も報告しておらず、正常性データはありません。 最近接続されて、まだデプロイ中の場合、ソリューションの状態が報告されない可能性があります。

> [!NOTE]
> 正常性状態データを使用できない場合、Security Center には、最後に受信したイベントの日時が表示され、ソリューションが報告を行っているかどうかを示します。 使用できる正常性データがなく、過去 14 日以内にアラートを受信していなかった場合、Security Center は、ソリューションで異常が発生しているか、報告が行われていないことを示します。
>
>

**[表示]** を選択すると、次のような追加情報とオプションを確認できます。

   - **ソリューション コンソール** - このソリューションの管理エクスペリエンスが開きます。
   - **VM をリンクする** - [アプリケーションのリンク] ページが開きます。 ここで、パートナー ソリューションにリソースを接続できます。
   - **Delete solution (ソリューションを削除する)**
   - **構成**

   ![パートナー ソリューションの詳細](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>検出されたソリューション

Security Center は、Azure で実行されていても Security Center に接続されていないセキュリティ ソリューションを自動的に検出し、 **[検出されたソリューション]** セクションにそのソリューションを表示します。 これらのソリューションには、[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) などの Azure ソリューションや、パートナー ソリューションが含まれます。

> [!NOTE]
> 検出されたソリューション機能のサブスクリプション レベルで、Security Center の standard レベルが必要です。 価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>

ソリューションの下の **[接続]** を選択して、Security Center と統合し、セキュリティのアラートが通知されるようにします。

### <a name="add-data-sources"></a>データ ソースの追加

**[データ ソースの追加]** セクションには、接続できるその他の使用可能なデータ ソースが表示されます。 このようなソースのいずれかからデータを追加する手順については、 **[追加]** をクリックしてください。

![データ ソース](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>次のステップ

この記事では、Security Center でパートナー ソリューションを統合する方法について説明しました。 関連情報については、次の記事をご覧ください。

* [セキュリティ アラートと推奨事項のエクスポート](continuous-export.md)。 Azure Sentinel または任意の他の SIEM との統合を設定する方法について説明しています。
* [Security Center でのセキュリティ正常性の監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。