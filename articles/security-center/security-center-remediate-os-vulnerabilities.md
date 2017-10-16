---
title: "Azure Security Center での OS の脆弱性の修復 | Microsoft Docs"
description: "このドキュメントでは、\"OS の脆弱性の修復\" という Azure Security Center の推奨事項を実装する方法について説明します。"
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
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 39879c22278a55f841e294cda5a89bec2bdf6988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Azure Security Center での OS の脆弱性の修復
Azure Security Center では、仮想マシン (VM) とコンピューターのオペレーティング システム (OS) を日々分析して、VM とコンピューターの攻撃に対する脆弱性を高める可能性のある構成を特定します。 Security Center では、OS 構成が推奨される構成規則に適合していない場合に脆弱性を解決するよう推奨し、これらの脆弱性に対処するための構成変更を推奨します。

> [!NOTE]
> 監視対象の具体的な構成の詳細については、[推奨される構成規則の一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)をご覧ください。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
OS の脆弱性の修復は、Security Center に推奨事項として表示されます。 この推奨事項は、**[推奨事項]** と **[コンピューティング]** に表示されます。

この例では、**[コンピューティング]** の **[OS の脆弱性の修復 (Microsoft による)]** を確認します。
1. Security Center のメイン メニューの **[コンピューティング]** を選択します。

   ![OS の脆弱性の修復][1]

2. **[コンピューティング]** で、**[OS の脆弱性の修復 (Microsoft による)]** を選択します。 **[OS の脆弱性 (Microsoft による) の不一致]** ダッシュボードが開きます。

   ![OS の脆弱性の修復][2]

  ダッシュボードの上部には、次の情報が表示されます。

  - VM とコンピューターの OS 構成が失敗した規則の総数 (重要度別)。
  - VM とコンピューターの OS 構成が失敗した規則の総数 (種類別)。
  - Windows OS 構成および Linux OS 構成での失敗した規則の総数。

  ダッシュボードの下部には、VM とコンピューターの失敗したすべての規則と、インストールされていない更新プログラムの重要度が一覧表示されます。 一覧には次の項目が含まれています。

  - **[CCEID]**: 規則の一意の CCE 識別子。 Security Center では、Common Configuration Enumeration (CCE) を使用して構成規則に一意の識別子を割り当てます。
  - **[名前]**: 失敗した規則の名前。
  - **[規則の種類]**: レジストリ キー、セキュリティ ポリシー、または監査ポリシー。
  - **[VM とコンピューターの数]**: 失敗した規則が適用されている VM とコンピューターの総数。
  - **[規則の重要度]**: CCE の重要度の値 (重大、重要、または警告)。
  - **[状態]**: 推奨事項の現在の状態。

    - **[オープン]**: 推奨事項への対処がまだ行われていない。
    - **[処理中]**: 現在、リソースへの推奨事項の適用を進めており、ユーザーのアクションは不要。
    - **解決**: 推奨事項が既に完了済み  (問題が解決されると、エントリが淡色表示になります)。

3. 一覧で失敗した規則を選択して詳細を表示します。

   ![失敗した構成規則][3]

  このブレードでは次の情報が提供されます。

  - [名前] -- 規則の名前
  - [CCIED] -- 規則の一意の CCE 識別子
  - [OS バージョン] -- VM またはコンピューターの OS バージョン
  - [規則の重要度] -- CCE の重要度の値 (重大、重要、または警告)。
  - [詳細な説明] -- 規則の説明
  - [脆弱性] -- 脆弱性、または規則が適用されていない場合のリスクの説明
  - [潜在的な影響] -- 規則を適用したときのビジネスへの影響
  - [対応手段] -- 修復手順
  - [期待される値] -- Security Center が規則と照らし合わせて VM の OS 構成を分析したときに求められる値
  - [実際の値] -- 規則と照らし合わせた VM の OS 構成の分析後に返された値
  - [規則の操作] -- 規則と照らし合わせた VM の OS 構成の分析中に Security Center で使用された規則の操作

4. 上部のリボンの **[検索]** アイコンをクリックします。 [検索] が開き、選択した OS の脆弱性を持つ VM とコンピューターがあるワークスペースが一覧表示されます。 このワークスペース選択ブレードは、選択した規則が、異なるワークスペースに接続された複数の VM に適用されている場合にのみ表示されます。

  ![一覧表示されたワークスペース][4]

5. ワークスペースを選択します。 Log Analytics 検索クエリが開き、OS の脆弱性が存在するワークスペースでフィルター処理されます。

  ![OS の脆弱性が存在するワークスペース][5]

6. 一覧でコンピューターを選択して詳細を確認します。 そのコンピューターだけを対象にフィルター処理された情報が含まれた別の検索結果が表示されます。

  ![対象のコンピューター用にフィルター処理された情報][6]

## <a name="next-steps"></a>次のステップ
この記事では、"OS の脆弱性の修復" という Security Center の推奨事項を実装する方法について説明しました。 構成規則のセットは [こちら](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)で確認できます。 Security Center では、CCE (Common Configuration Enumeration) を使用して構成規則に一意の識別子を割り当てます。 詳細については、 [CCE](https://nvd.nist.gov/cce/index.cfm) サイトをご覧ください。

Security Center の詳細については、次のリソースを参照してください。

* 「[Azure Security Center でサポートされているプラットフォーム](security-center-os-coverage.md)」 - サポートされている Windows VM と Linux VM の一覧を示します。
* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」 - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 - 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 - セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」 - パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」 - このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) - Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
