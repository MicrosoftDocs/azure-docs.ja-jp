---
title: "Azure Security Center クイック スタート ガイド | Microsoft Docs"
description: "この記事では、Azure Security Center のセキュリティ監視およびポリシー管理コンポーネントと、次のステップへの展開を説明することで、この Azure Security Center をすぐに開始できるようにします。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 392c814b7d3ff6b4f0f7850a51960576775e0307
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-quick-start-guide"></a>Azure Security Center クイック スタート ガイド
この記事では、Azure Security Center のセキュリティ監視およびポリシー管理コンポーネントについて説明することで、この Azure Security Center をすぐに開始できるようにします。

> [!NOTE]
> 2017 年 6 月上旬以降、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行います。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>
>

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

Security Center の Free レベルはサブスクリプションによって自動的に有効になり、これにより、Azure リソースのセキュリティ状態が表示されます。 また、基本的なセキュリティ ポリシーの管理、セキュリティに関する推奨事項、および Azure パートナーのセキュリティ製品およびサービスとの統合が実現します。

Security Center には [Azure ポータル](https://azure.microsoft.com/features/azure-portal/)からアクセスします。 Azure Portal の詳細については、[ポータルに関するドキュメント](https://azure.microsoft.com/documentation/services/azure-portal/)をご覧ください。

## <a name="permissions"></a>アクセス許可
Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときに Azure リソースに関連した情報のみが表示されます。 Security Center のロールと許可されているアクションの詳細については、「[Permissions in Azure Security Center (Azure Security Center のアクセス許可)](security-center-permissions.md)」を参照してください。

## <a name="data-collection"></a>データ収集
Security Center では、仮想マシン (VM) のセキュリティ状態へのアクセス、セキュリティ推奨事項の提供、脅威についての警告を行うために、その仮想マシンからデータを収集します。 最初に Security Center にアクセスするときは、サブスクリプション内のすべての VM に対してデータ収集が有効になっています。 Security Center は、サポートされている既存の Azure VM と新しく作成される VM すべてに対して Microsoft Monitoring Agent をプロビジョニングします。 データ収集のしくみの詳細については、[データ収集の有効化](security-center-enable-data-collection.md)に関するページを参照してください。

データ収集を使用することをお勧めします。 Security Center の Free レベルを使用している場合は、セキュリティ ポリシーでデータ収集を無効にすることで、VM からのデータ収集を無効にできます。 データ収集は、Security Center の Standard レベルのサブスクリプションでは必須の機能です。 Free 価格レベルと Standard 価格レベルの詳細については、[Security Center の価格](security-center-pricing.md)に関するページを参照してください。

次の手順では、Security Center のコンポーネントにアクセスする方法と、そのコンポーネントの使用方法について説明します。 また、オプトアウトする場合に、データ収集をオフにする方法も示しています。

> [!NOTE]
> この記事では、サンプル デプロイを使用してサービスについて紹介します。 この記事はステップ バイ ステップ ガイドではありません。
>
>

## <a name="access-security-center"></a>セキュリティ センターへのアクセス
セキュリティ センターにアクセスするには、ポータルで次の手順を実行します。

1. **[Microsoft Azure]** メニューの **[セキュリティ センター]** を選択します。

   ![Azure メニュー][1]
2. Security Center に初めてアクセスする場合は、**[ようこそ]** ブレードが開きます。 **[Security Center の起動]** を選択して、**[Security Center]** ブレードを開き、データ収集を有効にします。
   ![[ようこそ] 画面][10]
3. [ようこそ] ブレードから Security Center を起動するか、[Microsoft Azure] メニューから Security Center を選択すると、**[セキュリティ センター]** ブレードが開きます。 後で **[セキュリティ センター]** ブレードにアクセスしやすいように、右上にある **[ダッシュボードにブレードをピン留め]** オプションを選択します。
   ![Pin blade to dashboard option][2]

## <a name="use-security-center"></a>セキュリティ センターの使用
Azure サブスクリプションとリソース グループのセキュリティ ポリシーを構成できます。 サブスクリプションのセキュリティ ポリシー を構成してみましょう。

1. **[セキュリティ センター]** ブレードで **[ポリシー]** タイルを選択します。
2. **[Security policy - Define policy per subscription]\(セキュリティ ポリシー - サブスクリプションごとにポリシーを定義する\)** ブレードで、サブスクリプションを選択します。
3. **[セキュリティ ポリシー]** ブレードでは、自動的にログを収集するために **[データ収集]** が有効になっています。 監視の拡張機能は、サブスクリプション内の既存の VM と新しい VM のすべてに対してプロビジョニングされます (Security Center の Free レベルでは、**[データ収集]** を **[オフ]** に設定することで、データ収集を無効にできます。 **[データ収集]** を **[オフ]** にすると、Security Center でセキュリティ警告と推奨事項を提示できなくなります)。
4. **[セキュリティ ポリシー]** ブレードで、**[防止ポリシー]** を選択します。 これにより、**[防止ポリシー]** ブレードが開きます。
5. **[防止ポリシー]** ブレードで、セキュリティ ポリシーの一部として表示する推奨事項をオンにします。 次に例を示します。

   * **[システムの更新プログラム]** を **[オン]** に設定すると、サポートされているすべての VM で不足している OS の更新プログラムがスキャンされます。
   * **[OS の脆弱性]** を **[オン]** に設定すると、サポートされているすべての VM がスキャンされ、攻撃に対する VM の脆弱性を高める可能性がある OS の構成が特定されます。

### <a name="view-recommendations"></a>推奨事項の表示
1. **[セキュリティ センター]** ブレードに戻り、**[推奨事項]** タイルを選択します。 セキュリティ センターは、Azure リソースのセキュリティの状態を定期的に分析します。 Security Center では、潜在的なセキュリティの脆弱性が特定されると、推奨事項が **[推奨事項]** ブレードに表示されます。
   ![Azure Security Center の推奨事項][5]
2. **[推奨事項]** ブレードで推奨事項を選択すると、詳細な情報を表示したり、問題を解決するためのアクションを実行したりできます。

### <a name="view-the-security-state-of-your-resources"></a>リソースのセキュリティの状態を表示する
1. **[セキュリティ センター]** ブレードに戻ります。 ダッシュボードの **[防止策]** セクションには、VM、ネットワーク、データ、アプリケーションのセキュリティ状態インジケーターが表示されます。
2. **[Compute (コンピューティング)]** を選択して、詳細を表示します。 **[Compute (コンピューティング)]** ブレードが開き、3 つのタブが表示されます。

  - **[概要]** - 監視と VM の推奨事項が表示されます。
  - **[仮想マシン]** - すべての VM と現在のセキュリティ状態の一覧が表示されます。
  - **[クラウド サービス]** - Security Center で監視されている Web ロールと worker ロールの一覧が表示されます。

    ![The Resources health tile in Azure Security Center][6]

3. **[概要]** タブの **[VIRTUAL MACHINES RECOMMENDATIONS (仮想マシンに関する推奨事項)]** の下にある推奨事項を選択して、詳細情報を表示したり、必要な制御を構成するためのアクションを実行したりします。
4. **[仮想マシン]** タブで、VM を選択して、詳細を表示します。

### <a name="view-security-alerts"></a>セキュリティの警告の表示
1. **[セキュリティ センター]** ブレードに戻り、**[セキュリティ通知]** タイルを選択します。 **[セキュリティ通知]** ブレードが開き、警告の一覧が表示されます。 こうした警告は、Security Center によるセキュリティ ログとネットワーク アクティビティの分析によって生成されます。 これには、統合されているパートナー ソリューションの警告も含まれます。
   ![Security alerts in Azure Security Center][7]

   > [!NOTE]
   > セキュリティの警告は、Security Center の Standard レベルが有効になっている場合にのみ使用できます。 Standard レベルでは 60 日間無料試用版を使用できます。 Standard レベルを入手する方法については、「[次のステップ](#next-steps)」を参照してください。
   >
   >
2. アラートを選択すると詳細な情報が表示されます。 この例では、**[Modified system binary discovered (システム バイナリの改変の検出)]** を選択します。 これによりブレードが開き、警告の詳細情報が表示されます。
   ![Azure Security Center のセキュリティ警告の詳細][8]

### <a name="view-the-health-of-your-partner-solutions"></a>パートナー ソリューションの正常性の表示
1. **[セキュリティ センター]** ブレードに戻ります。 **[パートナー ソリューション]** タイルでは、Azure サブスクリプションと統合されたパートナー ソリューションの正常性状態をひとめで監視できます。
2. **[パートナー ソリューション]** タイルを選択します。 ブレードが開き、Security Center に接続されているパートナー ソリューションの一覧が表示されます。
   ![[パートナー ソリューション]][9]
3. パートナー ソリューションを選択します。 この例では、**QualysVa1** ソリューションを選択します。  ブレードが開き、パートナー ソリューションとそのソリューションの関連リソースの状態が表示されます。 **[ソリューション コンソール]** を選択して、このソリューションのパートナー管理エクスペリエンスを開きます。

## <a name="next-steps"></a>次のステップ
この記事では、Security Center のセキュリティ監視とポリシー管理のコンポーネントについて説明しました。 Security Center について理解したら、次の手順をお試しください。

* Azure サブスクリプションのセキュリティ ポリシー を構成します。 詳細については、「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」を参照してください。
* Security Center の推奨事項を使用して、Azure リソースを保護します。 詳細については、「[Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。
* 現在のセキュリティの警告を確認して、管理します。 詳細については、「[Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」を参照してください。
- 「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」 - Security Center でデータがどのように管理および保護されているかを説明しています。
* Security Center の [Standard レベル](security-center-pricing.md)で利用できる[高度な脅威検出機能](security-center-detection-capabilities.md)について確認します。 Standard レベルは、最初の 60 日間は無料です。
* Security Center に関する質問がある場合は、[Azure Security Center の FAQ](security-center-faq.md) をご覧ください。

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

