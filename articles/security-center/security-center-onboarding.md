---
title: "セキュリティ強化のための Azure Security Center Standard へのオンボード | Microsoft Docs"
description: " セキュリティを強化するために、Azure Security Center Standard にオンボードする方法について説明します。 "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: a6394b1b02ebfe518dc2f2b7f65eb677bb0ba5f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>セキュリティ強化のための Azure Security Center Standard へのオンボード
ハイブリッド クラウドのワークロードで強化されたセキュリティ管理と脅威保護を利用するには、Security Center Standard にアップグレードします。  Standard は 60 日間無料で試用できます。 詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください。

Security Center Standard は次の機能を備えています。

- **ハイブリッド セキュリティ** - オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 セキュリティ ポリシーを適用し、ハイブリッド クラウドのワークロードのセキュリティを継続的に評価することで、セキュリティ標準に確実に準拠できます。 ファイアウォールやその他のパートナー ソリューションなどのさまざまなソースから、セキュリティ データを収集、検索、分析します。
- **高度な脅威検出** - 高度な分析と Microsoft インテリジェント セキュリティ グラフを使用して、巧妙化していくサイバー攻撃に対応します。  組み込みの行動分析と機械学習を活用して、各種攻撃やゼロデイ攻撃を特定します。 ネットワーク、マシン、クラウド サービスに対する攻撃や侵害後のアクティビティを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、調査を効率化します。
- **アクセスとアプリケーションの制御** - 特定のワークロードに適応し、機械学習を活用したホワイトリスト登録の推奨事項を適用することで、マルウェアや他の望ましくないアプリケーションをブロックします。 Azure VM の管理ポートへの制御された Just-In-Time アクセスによってネットワーク攻撃対象領域を縮小することで、ブルート フォース攻撃などのネットワーク攻撃にさらされる機会を大幅に減らします。

## <a name="detecting-unprotected-resources"></a>保護されていないリソースの検出     
Security Center では、Security Center Standard に対応していない Azure サブスクリプションまたはワークスペースが自動的に検出されます。 これには、Security Center Free を使用する Azure サブスクリプションと、セキュリティ ソリューションが有効になっていないワークスペースが含まれます。

Azure サブスクリプション全体を Standard レベルにアップグレードできます。この場合、サブスクリプション内のすべてのリソースが Standard レベルを継承します。また、特定のリソース グループだけをアップグレードする固有のポリシーを定義することもできます。 リソース グループのポリシー設定が固有の場合、サブスクリプションを Standard レベルにアップグレードしたときに価格ポリシーは上書きされません。 Standard レベルをサブスクリプションに適用すると、Security Center によって作成されたワークスペースに報告する、サブスクリプション内の VM にのみ適用されます。 Standard レベルをワークスペースに適用すると、ワークスペースに報告するすべてのリソースに適用されます。

> [!NOTE]
> ソリューションを特定のエージェント セットに制限することで、コストを管理し、ソリューション用に収集されるデータの量を制限することもできます。 [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用すると、ソリューションにスコープを適用し、ワークスペース内のコンピューターのサブセットをターゲットにすることができます。  ソリューションのターゲット設定を使用している場合、Security Center はソリューションがないものとしてワークスペースを表示します。
>
>

## <a name="upgrade-an-azure-subscription"></a>Azure サブスクリプションのアップグレード
すべてのサブスクリプションを Standard にアップグレードするには、次の手順に従います。
1. Security Center のメイン メニューの **[オンボード]** を選択します。
2. **[高度なセキュリティへのオンボード]** に、オンボードの対象となるサブスクリプションが一覧表示されます。 **[Standard プランを適用]** をクリックすると、表示されているすべてのサブスクリプションをアップグレードできます。

  ![すべてのサブスクリプションのアップグレード][1]

個々のサブスクリプションをアップグレードする場合: **[Standard レベルを適用]** を選択することで、**[オンボード]** からサブスクリプションをアップグレードできます。 サブスクリプションのリソース グループを Standard にアップグレードするには、サブスクリプションを選択します。
1. サブスクリプションを選択します。  **[セキュリティ ポリシー]** に、そのサブスクリプションに含まれるリソース グループの情報が表示されます。
2. サブスクリプションまたはリソース グループを選択します。

  ![すべてのサブスクリプションのアップグレード][2]

3. **[Standard]** を選択して、Free から Standard にアップグレードします。
4. [ **保存**] を選択します。

> [!NOTE]
> サブスクリプションを Standard にアップグレードすると、[自動プロビジョニング](security-center-enable-data-collection.md)が有効になります (これまで無効になっていた場合)。 監視エージェントの自動プロビジョニングを使用することをお勧めします。
>
>

## <a name="upgrade-a-workspace"></a>ワークスペースのアップグレード
ワークスペースに Standard を適用すると、ワークスペースに報告するすべてのリソースに適用されます。

1. **[オンボード]** ブレードに戻ります。
2. ワークスペースを選択します。

  ![ワークスペースのアップグレード][8]

3. **[Standard]** を選択してアップグレードします。  
4. [ **保存**] を選択します。

   > [!NOTE]
   > ワークスペースに Free も Standard も適用されていない場合があります。 Free を選択した場合、Security Center の Free レベルの機能が Azure VM にのみ適用されます。 Free レベルの機能は、Azure 以外のコンピューターには適用されません。 Standard を選択した場合、ワークスペースに報告するすべての Azure VM と Azure 以外のコンピューターに Standard レベルの機能が適用されます。 Azure リソースと Azure 以外のリソースに高度なセキュリティを提供するために、Standard を適用することをお勧めします。
   >
   >

## <a name="onboard-non-azure-computers"></a>Azure 以外のコンピューターのオンボード
Security Center では、Azure 以外のコンピューターのセキュリティの状況を監視できますが、これらのリソースをオンボードしておく必要があります。 Azure 以外のコンピューターは、**[オンボード]** ブレードまたは **[コンピューティング]** ブレードから追加できます。 ここでは両方の方法を紹介します。

### <a name="add-new-non-azure-computers-from-onboarding"></a>[オンボード] から新しい Azure 以外のコンピューターを追加する

1. **[オンボード]** に戻ります。   
2. **[Do you want to add new non-Azure computers]\(新しい Azure 以外のコンピューターを追加しますか\)** を選択します。

  ![Azure 以外のコンピューターの追加][3]

既存のワークスペースがある場合は、**[Add new Non-Azure computers]\(新しい Azure 以外のコンピューターの追加\)** に一覧表示されます。 コンピューターを既存のワークスペースに追加することも、新しいワークスペースを作成することもできます。 新しいワークスペースを作成するには、**[add a new workspace]\(新しいワークスペースを追加\)** リンクをクリックします。

ここでは、次の両方の方法を紹介します。

- 新しいワークスペースを作成してコンピューターを追加する
- 既存のワークスペースを選択してコンピューターを追加する

**新しいワークスペースを作成してコンピューターを追加する**

1. **[Add new non-Azure computers]\(新しい Azure 以外のコンピューターの追加\)** で、**[add a new workspace]\(新しいワークスペースを追加\)** をクリックします。

   ![新しいワークスペースの追加][4]

2. **[セキュリティおよび監査]** で、**[OMS ワークスペース]** を選択して新しいワークスペースを作成します。
3. **[OMS ワークスペース]** で、ワークスペースの情報を入力します。
4. **[OMS ワークスペース]** で **[OK]** をクリックします。  [OK] をクリックすると、Windows または Linux エージェントをダウンロードするためのリンクと、エージェントの構成時に使用するワークスペース ID のキーが提供されます。
5. **[セキュリティおよび監査]** で **[OK]** をクリックします。

**既存のワークスペースを選択してコンピューターを追加する**

前述の **[オンボード]** からのワークフローに従ってコンピューターを追加できます。 また、**[コンピューティング]** からのワークフローに従ってコンピューターを追加することもできます。 この例では、**[コンピューティング]** を使用します。

1. Security Center のメイン メニューに戻り、**[概要]** ダッシュボードに移動します。

   ![概要][5]

2. **[コンピューティング]** タイルを選択します。
3. **[コンピューティング]** で、**[コンピューターの追加]** を選択します。

   ![[コンピューティング] ブレード][6]

4. **[Add new non-Azure computers]\(新しい Azure 以外のコンピューターの追加\)** で、コンピューターの接続先となるワークスペースを選択し、**[コンピューターの追加]** をクリックします。

   ![コンピューターの追加][7]

 **[ダイレクト エージェント]** ブレードに、Windows または Linux エージェントをダウンロードするためのリンクと、エージェントの構成時に使用するワークスペース ID のキーが表示されます。   

## <a name="next-steps"></a>次のステップ
この記事では、Security Center の高度なセキュリティのメリットを得るために、Azure リソースと Azure 以外のリソースをオンボードする方法について説明しました。  オンボードされたリソースを活用するには、以下を参照してください。

- [データ収集を有効にする](security-center-enable-data-collection.md)
- [脅威インテリジェンス レポート](security-center-threat-report.md)
- [Just In Time VM アクセス](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
