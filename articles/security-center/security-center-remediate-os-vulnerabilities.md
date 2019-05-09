---
title: Azure Security Center でのセキュリティ構成の修復 | Microsoft Docs
description: このドキュメントでは、"セキュリティ構成の修復" という Azure Security Center の推奨事項を実装する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: f4558c6fdb1e5e4f0ffb7a4b4fdb1ab62eb4cfa9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121283"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Azure Security Center でのセキュリティ構成の修復
Azure Security Center では、仮想マシン (VM) とコンピューターのオペレーティング システム (OS) を日々分析して、VM とコンピューターの攻撃に対する脆弱性を高める可能性のある構成を特定します。 Security Center では、OS 構成が推奨されるセキュリティ構成規則に適合していない場合に脆弱性を解決するよう推奨し、これらの脆弱性に対処するための構成変更を推奨します。

監視対象の具体的な構成の詳細については、[推奨される構成規則の一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)をご覧ください。 セキュリティの構成の評価をカスタマイズする方法については、「[Azure Security Center での OS セキュリティ構成のカスタマイズ [プレビュー]](security-center-customize-os-security-config.md)」をご覧ください。

## <a name="implement-the-recommendation"></a>推奨事項の実装
"セキュリティ構成の修復" は、Security Center に推奨事項として表示されます。 推奨事項は、**[推奨事項]** > **[計算とアプリ]** に表示されます。

この例では、**[計算とアプリ]** の [Remediate security configurations]\(セキュリティ構成の修復\) の推奨事項について説明します。
1. Security Center の左側のウィンドウで **[計算とアプリ]** を選択します。  
   **[計算とアプリ]** ウィンドウが開きます。

   ![セキュリティ構成の修復][1]

2. **[Remediate security configurations]\(セキュリティ構成の修復\)** を選択します。  
   **[セキュリティ構成]** ウィンドウが開きます。

   ![[セキュリティ構成] ウィンドウ][2]

   ダッシュボード上部には次の項目が表示されます。

   - **失敗した規則 (重要度別)**: VM とコンピューターの OS 構成が失敗した規則の総数 (発生した重要度別)。
   - **[失敗した規則 (種類別)]**: VM とコンピューターの OS 構成が失敗した規則の総数 (発生した種類別)。
   - **[失敗した Windows ルール]**: Windows OS 構成での失敗した規則の総数。
   - **[失敗した Linux ルール]**: Linux OS 構成での失敗した規則の総数。

   ダッシュボードの下方のセクションには、VM とコンピューターの失敗したすべての規則と、インストールされていない更新プログラムの重要度が一覧表示されます。 一覧には、次の要素が含まれます。

   - **[CCEID]**: 規則の一意の CCE 識別子。 Security Center では、Common Configuration Enumeration (CCE) を使用して構成規則に一意の識別子を割り当てます。
   - **[名前]**: 失敗した規則の名前。
   - **[規則の種類]**: "*レジストリ キー*"、"*セキュリティ ポリシー*"、"*監査ポリシー*"、または "*IIS*" の規則の種類。
   - **[VM とコンピューターの数]**: 失敗した規則が適用されている VM とコンピューターの総数。
   - **[規則の重要度]**: CCE の値 ("*重大*"、"*重要*"、または "*警告*")。
   - **[状態]**: 推奨事項の現在の状態。

     - **[オープン]**:推奨事項への対処がまだ行われていない。
     - **[処理中]**: 現在、リソースへの推奨事項の適用を進めており、ユーザーのアクションは不要です。
     - **[解決済み]**: 推奨事項が適用されています。 問題が解決されると、エントリが淡色表示になります。

3. 失敗した規則の詳細を表示するには、一覧から選択します。

   ![失敗した構成規則の詳細表示][3]

   詳細表示には次の情報が表示されます。

   - **[名前]**: 規則の名前。
   - **[CCIED]**: 規則の一意の CCE 識別子。
   - **[OS バージョン]**: VM またはコンピューターの OS バージョン
   - **[規則の重要度]**: CCE の値 ("*重大*"、"*重要*"、または "*警告*")。
   - **[詳細な説明]**: 規則の説明。
   - **[脆弱性]**: 脆弱性、または規則が適用されていない場合のリスクの説明。
   - **[潜在的な影響]**: 規則を適用したときのビジネスへの影響。
   - **[対応手段]**: 修復手順。
   - **[期待される値]**: Security Center が規則と照らし合わせて VM の OS 構成を分析したときに求められる値。
   - **[実際の値]**: 規則と照らし合わせた VM の OS 構成の分析後に返された値。
   - **[規則の操作]**: 規則と照らし合わせた VM の OS 構成の分析中に Security Center で使用された規則の操作。

4. [詳細ビュー] ウィンドウの上部で、**[検索]** を選択します。  
   [検索] が開き、選択したセキュリティ構成の不一致が存在する VM とコンピューターがあるワークスペースの一覧が表示されます。 このワークスペースの選択は、選択した規則が、異なるワークスペースに接続された複数の VM に適用されている場合にのみ表示されます。

   ![一覧表示されたワークスペース][4]

5. ワークスペースを選択します。  
   Azure Monitor ログ検索クエリが開き、セキュリティ構成の不一致が存在するワークスペースでフィルター処理されます。

   ![OS の脆弱性が存在するワークスペース][5]

6. 一覧からコンピューターを選択します。  
   そのコンピューターだけを対象にフィルター処理された情報が含まれた、新しい検索結果が表示されます。

   ![選択したコンピューターに関する詳細な情報][6]

## <a name="next-steps"></a>次の手順
この記事では、"セキュリティ構成の修復" という Security Center の推奨事項を実装する方法について説明しました。 セキュリティの構成の評価をカスタマイズする方法については、「[Azure Security Center での OS セキュリティ構成のカスタマイズ [プレビュー]](security-center-customize-os-security-config.md)」をご覧ください。

監視対象の具体的な構成を確認するには、[推奨される構成規則の一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)をご覧ください。 Security Center では、Common Configuration Enumeration (CCE) を使用して構成規則に一意の識別子を割り当てます。 詳細については、[CCE](https://nvd.nist.gov/cce/index.cfm) のサイトをご覧ください。

Security Center の詳細については、次のリソースを参照してください。

* サポートされている Windows VM と Linux VM の一覧については、「[Azure Security Center でサポートされているプラットフォーム](security-center-os-coverage.md)」をご覧ください。
* Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法については、「[Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md)」をご覧ください。
* Azure リソースの保護に推奨事項がいかに役立つかについては、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」をご覧ください。
* Azure リソースの正常性を監視する方法については、「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」をご覧ください。
* セキュリティの警告の管理と対応の方法については、「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」をご覧ください。
* パートナー ソリューションの正常性状態を監視する方法については、[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)に関するページをご覧ください。
* このサービスの使用に関してよく寄せられる質問に対する回答については、「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」をご覧ください。
* Azure のセキュリティとコンプライアンスについてのブログ記事は、[Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)をご覧ください。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
