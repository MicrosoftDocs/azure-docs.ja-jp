---
title: "Azure Security Center 入門 | Microsoft Docs"
description: "Azure Security Center の主な機能とそのしくみについて説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: caa4f001871beeb83cc0406bd2f89c5aa026cd97
ms.lasthandoff: 12/08/2016


---
# <a name="introduction-to-azure-security-center"></a>Azure Security Center 入門
Azure Security Center の主な機能とそのしくみについて説明します。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。
>
>

## <a name="what-is-azure-security-center"></a>Azure セキュリティ センターとは
 セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

## <a name="key-capabilities"></a>主な機能
 セキュリティ センターには、脅威の回避、検出、対応を容易かつ効果的に行える Azure 組み込みの機能が用意されています。 主な機能は次のとおりです。

| 段階 | 機能 |
| --- | --- |
| 回避 |Azure リソースのセキュリティの状態を監視します。 |
| 回避 | Azure サブスクリプションとリソース グループのポリシーを、会社のセキュリティ要件、使用するアプリケーションの種類、データの機密度に基づいて定義します。 |
| 回避 | ポリシーに基づいたセキュリティに関する推奨事項を使用して、サービスの所有者が必要な制御を実装するプロセスをサポートします。 |
| 回避 | Microsoft やパートナーからのセキュリティ サービスやアプライアンスを迅速にデプロイします。 |
| 検出 |Azure リソース、ネットワーク、パートナー ソリューション (マルウェア対策プログラム、ファイアウォールなど) からセキュリティ データを自動的に収集して分析します。 |
| 検出 | グローバルな脅威インテリジェンスを活用します。情報源としては、Microsoft の製品とサービス、Microsoft デジタル犯罪対策ユニット (DCU: Digital Crimes Unit)、Microsoft セキュリティ レスポンス センター (MSRC)、および外部フィードがあります。 |
| 検出 | 機械学習や動作分析などの高度な分析を適用します。 |
| 対応 |優先順位付きのセキュリティ インシデントやアラートを提供します。 |
| 対応 | 攻撃元や影響を受けたリソースを分析します。 |
| 対応 | 現在の攻撃を阻止し、将来の攻撃を防止する方法を示します。 |

## <a name="introductory-walkthrough"></a>入門チュートリアル
 Security Center には [Azure ポータル](https://azure.microsoft.com/features/azure-portal/)からアクセスします。 [ポータルにサインイン](https://portal.azure.com)し、**[参照]** を選択します。次に、**[セキュリティ センター]** オプションまでスクロールするか、以前ポータルのダッシュボードにピン留めした **[セキュリティ センター]** タイルを選択します。

![Security tile in Azure portal][1]

セキュリティ センターからセキュリティ ポリシーの設定、セキュリティ構成の監視、セキュリティ アラートの表示を行えます。

### <a name="security-policies"></a>セキュリティ ポリシー
Azure サブスクリプションとリソース グループのポリシーは、会社のセキュリティ要件に応じて定義できます。 お使いのアプリケーションの種類や、各サブスクリプションのデータの機密度に合わせて定義することもできます。 たとえば、開発またはテストに使用されるリソースには、実稼働アプリケーションで使われるものと異なるセキュリティ要件が存在することがあります。 同様に、PII のような規制されたデータを持つアプリケーションには、より高いレベルのセキュリティが必要です。

> [!NOTE]
> サブスクリプション レベルまたはリソース グループ レベルのセキュリティ ポリシーを変更するには、サブスクリプションの所有者または共同作成者であることが必要です。
>
>

**[セキュリティ センター]** ブレードの **[ポリシー]** タイルを選ぶと、サブスクリプションとリソース グループの一覧が表示されます。   

![[セキュリティ センター] ブレード][2]

**[セキュリティ ポリシー]** ブレードで、どのサブスクリプションのポリシー詳細を表示するかを選びます。

![[セキュリティ ポリシー] ブレードのサブスクリプション][3]

**データの収集** (上記参照) はセキュリティ ポリシーについてのデータ コレクションを有効にします。 有効にすると、次のことが行われます。

* サポートされているすべての仮想マシン (VM) を毎日スキャンします。この目的は、セキュリティの監視と推奨事項の提示です。
* 分析と脅威の検出を目的として、セキュリティ イベントを収集します。

**[リージョンごとにストレージ アカウントを選択]** (上図参照) をクリックすると、 VM を実行しているリージョンごとに、その VM から収集したデータを保存するためのストレージ アカウントを選択できます。 ストレージ アカウントを選択しないと、リージョンごとに自動的に作成されます。 収集されたデータは、セキュリティ上の理由で他の顧客データから論理的に分離されます。

> [!NOTE]
> データの収集とリージョンごとのストレージ アカウントの選択は、サブスクリプション レベルで構成されます。
>
>

**[防止ポリシー]** を選択して (上図参照)、**[防止ポリシー]** ブレードを開きます。 **[推奨事項を表示]** では、サブスクリプション内のリソースのセキュリティのニーズに基づいて、監視と推奨事項の提示を必要とするセキュリティ制御項目を選択できます。

次に、どのリソース グループのポリシー詳細を表示するかを選択します。

![[セキュリティ ポリシー] ブレードのリソース グループ][4]

**[継承]** (上図参照) では、リソース グループを次のどちらにするかを定義できます。

* [継承] (既定値) を選択すると、このリソース グループに対するセキュリティ ポリシーはすべて、サブスクリプション レベルから継承されます。
* [一意] を選択すると、このリソース グループは独自のセキュリティ ポリシーを持つことになります。 **[推奨事項を表示]** で変更を加える必要があります。

> [!NOTE]
> サブスクリプション レベルのポリシーとリソース グループ レベルのポリシーとの間に競合がある場合は、リソース グループ レベルのポリシーが優先されます。
>
>

### <a name="security-recommendations"></a>セキュリティに関する推奨事項
 セキュリティ センターは Azure リソースのセキュリティの状態を分析して、潜在的なセキュリティ脆弱性を特定します。 推奨事項の一覧では、必要な制御を構成する手順を説明します。 たとえば、次のようになります。

* 悪意のあるソフトウェアを識別して削除するためのマルウェア対策をプロビジョニングする
* VM へのトラフィックを制御するためにネットワーク セキュリティ グループとルールを構成する
* Web アプリケーションを対象とする攻撃から保護するための Web アプリケーション ファイアウォールをプロビジョニングする
* 不足しているシステムの更新をデプロイする
* 推奨基準と一致しない OS 構成に対処する

**推奨事項** タイルをクリックして推奨事項の一覧を表示します。 各推奨事項をクリックすることで、追加の情報を表示したり、問題を解決するアクションを実行したりできます。

![Security recommendations in Azure Security Center][5]

### <a name="resource-health"></a>リソース ヘルス
**[リソース セキュリティの正常性]** タイルには、リソースの種類 (VM、Web アプリケーション、その他のリソース) ごとに環境の全体的なセキュリティの状態が表示されます。   

**[リソース セキュリティの正常性]** タイルでリソースの種類を選択すると、特定済みの潜在的なセキュリティ脆弱性の一覧など、詳しい情報が表示されます  (次の例では **[仮想マシン]** が選択されています)。

![Resources health tile][6]

### <a name="security-alerts"></a>セキュリティのアラート
 セキュリティ センターは、Azure リソース、ネットワーク、パートナー ソリューション (マルウェア対策プログラム、ファイアウォールなど) からログ データを自動的に収集、分析、統合します。 脅威が検出されると、セキュリティの警告が作成されます。 例には次の検出が含まれます。

* 既知の悪意のある IP アドレスと通信する、セキュリティ侵害された VM
* Windows エラー報告を使用して検出された高度なマルウェア
* VM に対するブルート フォース攻撃
* 統合されたマルウェア対策プログラムとファイアウォールからのセキュリティのアラート

**セキュリティ アラート** タイルをクリックすると、優先順位のついたアラートが一覧表示されます。

![セキュリティのアラート][7]

アラートを選択すると、攻撃や修復方法に関する提案について詳しく表示されます。

![Security alert details][8]

### <a name="partner-solutions"></a>パートナー ソリューション
**[パートナー ソリューション]** タイルでは、Azure サブスクリプションと統合されたパートナー ソリューションの正常性状態をひとめで監視できます。 セキュリティ センターには、ソリューションに由来する警告が表示されます。

**[パートナー ソリューション]** タイルを選択します。 接続されているパートナー ソリューションの一覧を示すブレードが開きます。

![パートナー ソリューション][9]

## <a name="get-started"></a>作業開始
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 セキュリティ センターは、Azure サブスクリプションがある場合に有効です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

 Security Center には [Azure ポータル](https://azure.microsoft.com/features/azure-portal/)からアクセスします。 詳細については、 [ポータルに関するドキュメント](https://azure.microsoft.com/documentation/services/azure-portal/) をご覧ください。

[Azure Security Center の概要](security-center-get-started.md) に関する記事では、Security Center のセキュリティ監視とポリシー管理のコンポーネントについて簡単に説明しています。

## <a name="see-also"></a>関連項目
このドキュメントでは、セキュリティ センターの概略のほか、その主な機能と使用を開始する方法を紹介しました。 詳細については、次の記事を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

