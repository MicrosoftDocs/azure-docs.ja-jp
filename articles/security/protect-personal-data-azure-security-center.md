---
title: "Azure Security Center を使って個人データを保護する | Microsoft Docs"
description: "Azure Security Center を使って個人データを保護する"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>個人データを侵害や攻撃から保護する: Azure Security Center

この記事は、Azure Security Center を使って個人データを侵害や攻撃から保護する方法を理解するのに役立ちます。

## <a name="scenario"></a>シナリオ 

米国に本社を置く大規模なクルーズ会社が、地中海とバルト海だけでなくイギリス諸島での旅程を提案して、その事業を拡大しています。 こうした取り組みを進めるために、イタリア、ドイツ、デンマーク、英国に拠点を置く、小規模なクルーズ会社をいくつか買収しました。

この会社は、Microsoft Azure を使用して、クラウドに会社のデータを保存しています。 これには、名前や住所、電話番号、クレジット カード情報など、個人を特定できる情報が含まれます。 また、次のような人事情報も含まれます。

- アドレス
- 電話番号
- 納税者番号
- 医療情報

そのクルーズ会社では、報酬や得意客向けプログラムのメンバーの大きなデータベースも保持されています。 この企業の従業員は、会社の支社からネットワークにアクセスし、世界各地に存在する旅行代理店の社員は、一部の会社リソースにアクセスできます。
個人データは、これらの場所と Microsoft のデータ センターとの間をネットワーク経由で行き来します。

## <a name="problem-statement"></a>問題の説明

この会社は、所有している Azure リソースへの攻撃の脅威を心配しています。 権限のないユーザーに、顧客や従業員の個人データが公開されないようにしたいと思っています。 防止と対応/修復の両方、およびそれらのクラウド リソースの現行のセキュリティを監視する効果的な方法について、ガイダンスを求めています。
今日の高度で計画的な攻撃者に対する強力な防衛線を必要としています。

## <a name="company-goal"></a>会社の目標

会社の目標の 1 つは、顧客および従業員の個人データを脅威から保護して、プライバシーを確保することです。 目標の 1 つは、侵害の兆候にすぐに対応して、影響を軽減することです。 それには、セキュリティの現在の状態を評価し、攻撃を受けやすい構成を特定し、それらを修復する方法が必要です。

## <a name="solutions"></a>解決方法

Microsoft Azure Security Center (ASC) は、統合されたセキュリティ監視とポリシー管理のソリューションを提供します。 脅威の回避、検出、対応を容易かつ効果的に行うことができる機能が用意されています。

### <a name="prevention"></a>防止

ASC を使用すると、セキュリティ ポリシーの設定、ジャストインタイム アクセスの提供、セキュリティに関する推奨事項の実装ができるようになり、侵害の防止に役立ちます。

セキュリティ ポリシーは、指定されたサブスクリプション内のリソースに対して推奨されるコントロールのセットを定義します。 Azure VM への受信トラフィックをロックダウンして、攻撃への露出を減らすために、ジャスト イン タイム アクセスを使うことができます。 セキュリティに関する推奨事項は、Azure リソースのセキュリティ状態を分析した後、ASC に作成されます。

#### <a name="how-do-i-set-security-policies-in-asc"></a>ASC でのセキュリティ ポリシーの設定方法

セキュリティ ポリシーは、サブスクリプションごとに構成できます。 セキュリティ ポリシーを変更するには、そのサブスクリプションの所有者または共同作業者である必要があります。 Azure Portal で、次の操作を行います。

1. ASC ダッシュボードで **[ポリシー]** を選びます。

2. ポリシーを有効にするサブスクリプションを選びます。

3. **[防止ポリシー]** を選び、サブスクリプションごとにポリシーを構成します。 **[仮想マシンからデータを収集する]** を **[オン]** に設定する必要があります。

4. このサブスクリプションに関連するセキュリティ推奨事項を有効にするには、**[防止ポリシー]** オプションで **[オン]** を選びます。

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

有効にできる各ポリシー推奨事項の詳細については、「[Azure Security Center でのセキュリティ ポリシーの設定](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies)」をご覧ください。

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>ジャスト イン タイム アクセス (JIT) の構成方法

JIT が有効になっている場合、Security Center では、NSG ルールを作成して Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックがロックダウンされるポートを選択します。 JIT アクセスを使うには、次の操作を行います。

1. ASC ブレードで **[Just in time VM access]\(ジャスト イン タイムでの VM へのアクセス\) タイル**を選びます。

2. **[推奨]** タブを選びます。

3. **[VM]** で有効にする VM を選択します。 VM の横にチェックマークが付きます。 
4. VM に対して **[JIT の有効化]** を選びます。
5. [ **保存**] を選択します。

JIT を有効にすることが ASC で推奨されている、既定のポートを確認できるようになります。 ジャスト イン タイム ソリューションを有効にする新しいポートを追加して構成することもできます。 Security Center 内の **[Just in time VM access]\(ジャスト イン タイムでの VM へのアクセス\)** タイルに、JIT アクセス用に構成されている VM の数が表示されます。 過去 1 週間に行われた承認済みアクセス要求の数も表示されます。

![](media/protect-personal-data-azure-security-center/jit-vm.png)

この方法の手順、およびジャスト イン タイム アクセスに関する追加情報については、「[ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)」をご覧ください。

#### <a name="how-do-i-implement-asc-security-recommendations"></a>ASC のセキュリティに関する推奨事項の実装方法

セキュリティ センターでは、潜在的なセキュリティの脆弱性が特定されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成する手順を説明します。 
1. ASC ダッシュボードで **[推奨事項]** タイルを選びます。

2. 推奨事項が表示されます。これは表形式で表示され、表の行はそれぞれ 1 つの推奨事項になります。

3. 推奨事項をフィルター処理するには、**[フィルター]** を選び、表示する重大度および状態の値を選びます。

4. 適用できない推奨事項を消去するには、右クリックして **[閉じる]** を選びます。

5. どの推奨事項を最初に適用する必要があるかを評価します。

6. 優先順位に従って推奨事項を適用します。

考えられる推奨事項の一覧、およびそれぞれの適用方法の手引きについては、「[Azure Security Center でのセキュリティに関する推奨事項の管理](https://docs.microsoft.com/azure/security-center/security-center-recommendations)」をご覧ください。

### <a name="detection-and-response"></a>検出と対応

脅威が検出された後はできるだけ早く対応する必要があるため、検出と対応は一緒に行います。
ASC の脅威検出は、Azure のリソースやネットワーク、接続されているパートナー ソリューションからセキュリティ情報を自動的に収集することで機能します。 ASC では、攻撃者が新たにより高度な攻撃を仕掛けてくるたびに検出アルゴリズムを迅速に更新できます。 ASC の脅威検出がどのように機能するかの詳細については、「[Azure Security Center の検出機能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)」をご覧ください。

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>セキュリティ警告の管理方法と対応方法

Security Center には、優先順位付きセキュリティ警告の一覧、および問題をすぐに調査する必要がある情報が表示されます。 Security Center には、攻撃を受けたものを修復する方法に関する推奨事項も含まれています。 セキュリティ警告を管理するには、次の操作を行います。

1. ASC ダッシュボードで **[セキュリティ警告]** タイルを選びます。 各警告についての詳細が表示されます。

2. 日付、状態、重大度に基づいて警告をフィルター処理するには、**[フィルター]** を選び、表示する値を選びます。

3. 警告に対応するには、その警告を選んで情報を確認してから、攻撃されたリソースを選びます。

4. **[説明]** フィールドに、推奨される修復などの詳細が表示されます。

![](media/protect-personal-data-azure-security-center/security-alerts.png)

セキュリティ警告への対応の詳細については、「[Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)」をご覧ください。

セキュリティ警告の調査にさらに役立つように、この会社は、[Azure Log Integration](https://aka.ms/AzLog) を使って、ASC の警告をその独自の SIEM ソリューションと統合できます。

#### <a name="how-do-i-manage-security-incidents"></a>セキュリティ インシデントの管理方法

ASC では、セキュリティ インシデントとは、キル チェーンのパターンと一致するリソースに関するすべての警告を集約したものです。 インシデントには関連する警告の一覧が表示されるため、それぞれの警告の発生について詳細情報を得ることができます。 インシデントは、[セキュリティ警告] タイルおよびブレードに表示されます。

セキュリティ インシデントを確認および管理するには、次の操作を行います。

1. **[セキュリティ警告]** タイルを選びます。 セキュリティ インシデントが検出されている場合は、セキュリティ警告グラフの下に表示されます。 それには、その他の警告とは異なるアイコンがあります。

2. このセキュリティ インシデントに関する詳細を表示するには、そのインシデントを選びます。 追加詳細には、詳細な説明、その重要度、その現在の状態、攻撃されたリソース、インシデントの修復手順、このインシデントに含まれていた警告などがあります。

フィルター処理をして、**インシデントのみ**、**警告のみ**、または**両方**を表示できます。

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>脅威インテリジェンス レポートのアクセス方法

ASC では、複数のソースからの情報を分析して脅威が識別されます。 インシデント対応を担当するチームが脅威の調査と修復を行うときに役立つように、Security Center には、検出された脅威に関する情報を含む脅威インテリジェンス レポートが用意されています。

Security Center には、攻撃の種類によって異なる、3 種類の脅威レポートがあります。
具体的には、以下のとおりです。

- 活動グループ レポート: 攻撃者、その目的、戦術に関する詳しい情報を記載したレポートです。

- キャンペーン レポート: 主に特定の攻撃キャンペーンの詳細を取り扱うレポートです。

- 脅威概要レポート: 前の 2 つのレポートの全項目を確認できるレポートです。

この種の情報は、インシデント対応プロセスにおいて継続的に調査を実施し、攻撃元、攻撃者の動機、この問題の影響を今後軽減するための方策を把握するために非常に便利です。

1. 脅威インテリジェンス レポートにアクセスするには、次の操作を行います。

2. ASC ダッシュボードで **[セキュリティ警告]** タイルを選びます。

3. 詳細情報を得るセキュリティ警告を選びます。

4. **[レポート]** フィールドで、脅威インテリジェンス レポートへのリンクをクリックします。

5. PDF ファイルが開きます。これはダウンロードできます。

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

ASC の脅威インテリジェンス レポートの詳細については、「[Azure Security Center の脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)」をご覧ください。

### <a name="assessment"></a>評価

セキュリティに対する姿勢のテストと評価を支援するために、ASC では、その仮想マシン推奨事項コンポーネントの一部として、Qualys クラウド エージェントとの統合された脆弱性評価を提供します。

Qualys エージェントは、Qualys 管理プラットフォームに脆弱性のデータを報告します。その後、脆弱性と正常性の監視データを ASC に送り返します。 ASC ダッシュ ボードの **[推奨事項]** ブレードに、脆弱性評価ソリューションを追加するための推奨事項が表示されます。

対象 VM に脆弱性評価ソリューションがインストールされると、Security Center では、VM がスキャンされ、システムとアプリケーションの脆弱性が検出、特定されます。 検出された問題は、**[Virtual Machines Recommendations]\(仮想マシンに関する推奨事項\)** オプションで表示されます。

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>脆弱性評価ソリューションの実装方法 

統合された脆弱性評価ソリューションが仮想マシンにまだデプロイされていない場合、Security Center は、それをインストールすることを推奨します。

1. ASC ダッシュボードの **[推奨事項]** ブレードで、**[脆弱性評価ソリューションの追加]** を選びます。

2. 脆弱性評価ソリューションをインストールする VM を選びます。

3. **[Install on [number of] VMs]\([数] 台の VM にインストール\)** をクリックします。

4. Azure Marketplace でパートナー ソリューションを選ぶか、**[既存のソリューションの使用]** の下で **[Qualys]\(Qualys\)** を選びます。

5. **[パートナー ソリューション]** ブレードで、自動更新設定をオンまたはオフにできます。

脆弱性評価ソリューションを実装する方法の詳細については、「[Azure Security Center での脆弱性評価](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Security Center クイック スタート ガイド](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Azure Security Center 入門](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Azure Security Center の警告と Azure ログの統合](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Boost Azure Security Center with Integrated Vulnerability Assessment (統合された脆弱性評価による Azure Security Center の機能向上)](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
