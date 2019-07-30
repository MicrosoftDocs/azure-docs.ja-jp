---
title: Azure Security Center で App Services を保護する | Microsoft Docs
description: この記事は、Azure Security Center で App Services の保護を開始する際に役立ちます。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: v-mohabe
ms.openlocfilehash: 6a6b5b6e247bd7c105286b86257a6ae11c1d1c60
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551856"
---
# <a name="protect-app-service-with-azure-security-center"></a>Azure Security Center で App Service を保護する
この記事は、Azure Security Center を使用し、App Service 上で実行されているアプリケーションを監視し、保護する際に役立ちます。

App Service を使用すると、インフラストラクチャを管理することなく、任意のプログラミング言語で Web アプリケーションを構築し、ホストできます。 App Service では自動スケールと高可用性が提供され、Windows と Linux の両方がサポートされるほか、GitHub、Azure DevOps、または任意の Git リポジトリからの自動化されたデプロイも可能です。 

Web アプリケーションの脆弱性は攻撃者に悪用されることがたびたびあります。インターネット上のほとんどすべての組織に対して共通の動的インターフェイスが使用されるためです。 App Service の上で実行されるアプリケーションに対する要求は、世界中の Azure データ センター内にデプロイされているいくつかのゲートウェイを通過します。このデータ センターは、各要求を該当するアプリケーションに送信する役目を担います。 

Azure Security Center では、VM のサンドボックスまたはオンデマンド インスタンスの App Service で実行されているアプリケーションを評価したり、推奨したりできます。 Azure にクラウド プロバイダーとして与えられている可視性を活用することで、Security Center では App Service の内部ログを分析し、しばしば複数の標的に対して実行される悪名高い Web アプリ攻撃がないか監視します。

Security Center では、クラウドの規模を活用して App Service アプリケーションに対する攻撃を特定します。また、攻撃者が偵察段階にあり、Azure でホストされている複数の Web サイトを対象に脆弱性を見つけようと調べているとき、新しい攻撃の出現に対して集中的に注意します。 Security Center では、分析モデルと機械学習モデルを利用し、HTTP であれ、管理メソッドであれ、アプリケーションとのやりとりを顧客に許可するあらゆるインターフェイスを保護します。 Security Center はさらに、Azure のファーストパーティ サービスとして、この PaaS の計算ノードをカバーするホストベースのセキュリティ分析を提供するというユニークな位置にもあります。Security Center では、既に悪用されている、Web アプリケーションに対する攻撃を検出できます。

## <a name="prerequisites"></a>前提条件

App Service を監視し、セキュリティで保護するには、専用のコンピューターに関連付けられている App Service プランを用意する必要があります。 これらのプランは、Basic、Standard、Premium、Isolated、または Linux です。 Azure Security Center では、Free プラン、Shared プラン、Consumption プランがサポートされていません。 詳細については、[Azure App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)に関するページを参照してください。

## <a name="security-center-protection"></a>Security Center の保護

Azure Security Center では、App Service が実行されている VM インスタンスと管理インターフェイスが保護されます。 App Service で実行されているアプリとの間で送受信される要求と応答も監視されます。

Security Center は App Service とネイティブで統合されており、デプロイやオンボーディングの必要がありません。統合は完全に透過的となっています。



## <a name="enabling-monitoring-and-protection-of-app-service"></a>App Service の監視と保護を有効にする

1. Azure で Security Center を選択します。
2. **[Pricing & settings]\(価格と設定\)** に移動し、サブスクリプションを選択します。
3. **[価格レベル]** の **[App Service]** 行でプランを **[有効]** に切り替えます。

![アプリ サービスを切り替える](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> リソース数量に一覧表示されているインスタンスの数は、価格レベル ブレードを開いた時点でアクティブなアプリ サービスの関連インスタンス数となります。 この数は選択したスケーリング オプションに基づいて変わる可能性があるため、課金されるインスタンスの数もそれに応じて変わります。

App Service の監視と推奨を無効にするには、このプロセスを繰り返し、 **[App Service]** プランを **[無効]** に切り替えます。



## <a name="see-also"></a>関連項目
この記事では、Azure Security Center での監視機能の使用方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md): Azure Security Center でセキュリティ設定を構成する方法について説明します。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md): セキュリティの警告の管理と対応の方法について説明しています。
* [App Service](security-center-virtual-machine-protection.md#app-services):App Service Environment と正常性の概要を一覧で表示します。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md): パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md): このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
