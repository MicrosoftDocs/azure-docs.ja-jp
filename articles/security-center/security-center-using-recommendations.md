---
title: "Azure Security Center の推奨事項を使用してセキュリティを強化する |Microsoft Docs"
description: " セキュリティ ポリシーと Azure Security Center の推奨事項を使用してセキュリティ攻撃を軽減する方法を説明します. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 313801fc3b845cd30506b1a26918660e77d2a986
ms.openlocfilehash: 67095e0d8752fb3a2bca692f65e77cde5fd1a966


---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center の推奨事項を使用してセキュリティを強化する
セキュリティ ポリシーを構成し、Azure Security Center によって提供される推奨事項を実装することで、セキュリティに関する重大なイベントの可能性を低減できます。 この記事では、セキュリティ ポリシーと Security Center の推奨事項を使用してセキュリティ攻撃を軽減する方法を説明します。

> [!NOTE]
> この記事は、「Azure Security Center [計画および運用ガイド](security-center-planning-and-operations-guide.md)」で紹介されているロールと概念に基づいています。 続行する前に、この計画ガイドを確認することをお勧めします。
>
>

## <a name="managing-security-recommendations"></a>セキュリティ管理に関する推奨事項
セキュリティ ポリシーは、指定されたサブスクリプションまたはリソース グループ内のリソースに推奨されるコントロールのセットを定義します。 Security Center では、セキュリティに関する会社の要件に合わせてポリシーを定義できます。 詳細については、「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」を参照してください。

リソース グループに対するセキュリティ ポリシーは、サブスクリプション レベルから継承されます。

![セキュリティ ポリシーの継承][1]

特定のリソース グループにカスタム ポリシーが必要な場合は、リソース グループの継承を無効にできます。 無効にするには、セキュリティ ポリシーのブレードで [継承] を [一意] に設定し、Security Center が推奨事項を表示するコントロールをカスタマイズします。

たとえば、SQL Database Transparent Data Encryption (TDE) ポリシーを必要としないワークロードが混在している場合は、サブスクリプション レベルのポリシーを無効にして、SQL TDE が必要なリソース グループでのみ有効にします。

> [!NOTE]
> サブスクリプション レベルのポリシーとリソース グループ レベルのポリシーとの間に競合がある場合は、リソース グループ レベルのポリシーが優先されます。
>
>

セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 Security Center では、潜在的なセキュリティの脆弱性が特定されると、セキュリティ ポリシーに設定されているコントロールに基づいて推奨事項が作成されます。 推奨事項では、必要なセキュリティ コントロールを構成する手順を説明します。

Security Center の現在のポリシーの推奨事項は、システムの更新プログラム、OS の構成、サブネットと仮想マシン (VM) のネットワーク セキュリティ、SQL Database の監査、SQL Database TDE、Web アプリケーション ファイアウォールに重点を置いています。 Security Center の推奨事項の最新情報については、「[Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

## <a name="scenario"></a>シナリオ
このシナリオでは、Security Center の推奨事項を監視し対処することで、重大なセキュリティ インシデントの可能性を減らすために Security Center を使用する方法を示します。 シナリオでは、架空の会社 Contoso、および「Azure Security Center [計画および運用ガイド](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)」で説明されているロールを使用します。 ロールは、Security Center を使用してさまざまなセキュリティ関連のタスクを実行する個人やチームを表します。 これらのロールを次に示します。

![シナリオのロール][2]

Contoso では、最近、オンプレミスのリソースの一部を Azure に移行しました。 Contoso は、クラウド内のリソースのセキュリティ攻撃に対する脆弱性を軽減する保護を実装して維持したいと考えています。

## <a name="recommended-solution"></a>推奨される解決策
1 つの解決策は、セキュリティの脆弱性の防止と検出に Security Center を使用することです。 Contoso では、Azure のサブスクリプションを使用して Security Center にアクセスできます。 すべての Azure サブスクリプションで [Free レベル](security-center-pricing.md)の Security Center が自動的に有効、サブスクリプションのすべての VM 上でデータ収集が有効になっています。

David は Contoso の IT セキュリティで、Security Center を使用して**セキュリティ ポリシー**を設定します。 Security Center は、Contoso の Azure リソースのセキュリティの状態を分析します。 Security Center では、潜在的なセキュリティの脆弱性が特定されると、セキュリティ ポリシーに設定されているコントロールに基づいて**推奨事項**が作成されます。

Jeff はクラウド ワークロード所有者で、Contoso のセキュリティ ポリシーに従って保護を実装、管理する責任を負います。 Jeff は、Security Center によって作成される推奨事項を監視して、保護を適用します。 推奨事項では、必要なセキュリティ コントロールを構成する手順を Jeff に説明します。

Jeff が保護を実装して維持し、セキュリティの脆弱性を排除するためには、次のことを行う必要があります。

- Security Center によって提供されるセキュリティに関する推奨事項を管理する
- セキュリティに関する推奨事項を評価し、適用するか無視するか判断する
- セキュリティに関する推奨事項を適用する

Jeff の手順に従い、コントロールを構成する手順をガイドする Security Center の推奨事項を使用してセキュリティの脆弱性を排除する方法を確認しましょう。

## <a name="how-to-implement-this-solution"></a>この解決策を実行する方法
Jeff は [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインし、Security Center コンソールを開きます。 日常の監視活動の一環として、次の手順を実行することによってセキュリティに関する推奨事項があるかどうかチェックします。

1. Jeff は **[推奨事項]** タイルを選択して、**[推奨事項]** ブレードを開きます。
   ![[推奨事項] タイルを選択する][3]
2. Jeff は、推奨事項の一覧を確認します。 Security Center によって優先度順 (優先度最高から最低の順) に推奨事項の一覧が提供されていることを確認します。 そこで、一覧の優先度が一番高い推奨事項に対処することにします。 **[推奨事項]** ブレードで、**[Endpoint Protection のインストール ]** を選択します。
3. **[Endpoint Protection をインストールします]** ブレードが開き、マルウェア対策が有効になっていない VM の一覧が表示されます。 Jeff は VM の一覧を確認し、すべての VM を選択して、**[Install on 3 VMs]** (3 台の VM にインストール) を選択します。
   ![[Endpoint Protection のインストール]][4]
4. **[Select Endpoint Protection]** (Endpoint Protection の選択) ブレードに&2; つのマルウェア対策ソリューションが表示されます。 Jeff は **[Microsoft マルウェア対策]** ソリューションを選択します。
5. マルウェア対策ソリューションに関する追加情報が表示されます。 Jeff は **[作成]** を選択します。
   ![[Microsoft マルウェア対策]][5]
6. Jeff は **[インストール]** ブレードで必要な構成設定を入力して、**[OK]** を選択します。

[[Microsoft マルウェア対策]](../security/azure-security-antimalware.md) が、選択した VM でアクティブになりました。

Jeff は、高優先度と中優先度の推奨事項を確認し、実装について判断します。 Jeff は、[セキュリティに関する推奨事項の管理](security-center-recommendations.md)に関する記事を参照して、推奨事項とそれを適用した場合の影響を理解します。

Jeff は、[Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) では、Azure のネットワークとインフラストラクチャの選択的なセキュリティ監視を行い、第三者から脅威インテリジェンスと不正使用の報告を受け取っていることを学びます。 Jeff が Contoso の Azure サブスクリプションのセキュリティ連絡先の詳細を指定している場合に、MSRC が Contoso の顧客データが違法または承認されていないパーティによってアクセスされたことを検出すると、Microsoft は Contoso に連絡します。 Jeff が **[セキュリティ連絡先の詳細情報の指定]** という推奨事項 (前述の推奨事項一覧の中で重大度が中程度の推奨事項) を適用するところを追いかけてみましょう。

1. Jeff は、**[推奨事項]** ブレードで **[セキュリティ連絡先の詳細情報の指定]** を選択します。すると、**[セキュリティ連絡先の詳細情報の指定]** ブレードが表示されます。
2. Jeff は連絡先情報を指定する Azure サブスクリプションを選択します。 2 つ目の **[セキュリティ連絡先の詳細情報の指定]** ブレードが開きます。
   ![セキュリティ連絡先の詳細][6]
3. 2 つ目の **[セキュリティ連絡先の詳細情報の指定]** ブレードで、Jeff は次の項目を入力します。

  - コンマで区切ったセキュリティ連絡先の電子メール アドレス (入力できる電子メール アドレスの数に制限はありません)
  - セキュリティ連絡先の電話番号

4. Jeff は、重大度が高い警告に関する電子メールを受信するために、**[Send me emails about alerts]** (警告に関する電子メールを受け取る) オプションもオンにします。
5. Jeff は、**[OK]** を選択して、セキュリティ連絡先の情報を Contoso のサブスクリプションに適用します。

Jeff は最後に、優先度の低い推奨事項 **[Remediate OS vulnerabilities]** (OS の修復の脆弱性) を確認し、この推奨事項は適用可能でないと判断します。 推奨事項を無視したいと考えます。 Jeff は、右側に表示される&3; つのドットを選択し、**[閉じる]** を選択します。
   ![推奨事項を閉じる][7]

## <a name="conclusion"></a>まとめ
Security Center の推奨事項を監視することにより、攻撃が行われる前にセキュリティの脆弱性を排除できます。 Security Center のセキュリティ ポリシーで保護を実装および維持することによって、セキュリティ インシデントを回避できます。

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png



<!--HONumber=Jan17_HO2-->


