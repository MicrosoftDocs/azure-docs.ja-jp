---
title: Azure Security Center クイックスタート - Azure サブスクリプションでの Security Center Standard の利用開始 | Microsoft Docs
description: このクイックスタートでは、セキュリティを強化するために Security Center の Standard 価格レベルにアップグレードする方法を示します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 37f85afbdd55d3f14638f0833f69bb1992770449
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086073"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>クイック スタート:Azure サブスクリプションでの Security Center Standard の利用開始
Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と脅威保護を実現します。 Free レベルでは Azure リソースのみの制限付きセキュリティが提供されますが、Standard レベルではこれらの機能がオンプレミスと他のクラウドに拡張されます。 Security Center Standard は、セキュリティの脆弱性の検出と修正、悪意のあるアクティビティをブロックするためのアクセス制御とアプリケーション制御の適用、分析とインテリジェンスを使用した脅威の検出、攻撃を受けたときのすばやい対応を支援します。 Security Center Standard は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

この記事では、セキュリティを強化するために Standard レベルにアップグレードし、仮想マシンに Microsoft Monitoring Agent をインストールしてセキュリティの脆弱性と脅威を監視します。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

サブスクリプションを Standard レベルにアップグレードするには、サブスクリプションの所有者、サブスクリプションの共同作成者、またはセキュリティ管理者の役割が割り当てられている必要があります。

## <a name="enable-your-azure-subscription"></a>Azure サブスクリプションの有効化

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。
2. **[Microsoft Azure]** メニューの **[セキュリティ センター]** を選択します。 **[セキュリティ センター - 概要]** が開きます。

   ![Security Center の概要][2]

**[セキュリティ センター - 概要]** では、ハイブリッド クラウド ワークロードのセキュリティ対策の統合ビューが表示されるので、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。 Security Center は、ユーザーまたは別のサブスクリプション ユーザーによって以前に Free レベルにオンボードされていない Azure サブスクリプションを自動的に有効にします。

**[サブスクリプション]** メニュー項目をクリックして、サブスクリプションの一覧を表示およびフィルター処理できます。 Security Center は、これらのサブスクリプションのセキュリティの評価を開始して、セキュリティの脆弱性を識別します。 評価の種類をカスタマイズするには、セキュリティ ポリシーを変更します。 セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。

Security Center を初めて起動してから数分以内に、以下の項目が表示されます。

- Azure サブスクリプションのセキュリティを向上させる方法についての**推奨事項**。 **[推奨事項]** タイルをクリックすると優先順位が付けられた一覧が起動します。
- Security Center によって現在評価されている **[計算とアプリ]**、**[ネットワーク]**、**[データのセキュリティ]**、**[ID およびアクセス]** の各リソースとそれぞれのセキュリティ対策のインベントリ。

Security Center をフルに活用するには、次の手順に従って Standard レベルにアップグレードし、Microsoft Monitoring Agent をインストールする必要があります。

## <a name="upgrade-to-the-standard-tier"></a>Standard レベルにアップグレードする
Security Center のクイックスタートおよびチュートリアルの目的上、Standard レベルにアップグレードする必要があります。 Security Center Standard には無料試用版があります。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。 

1. Security Center メイン メニューの **[使用の開始]** を選択します。
 
   ![作業開始][4]

2. Security Center の **[アップグレード]** に、オンボードの対象となるサブスクリプションとワークスペースが一覧表示されます。 
   - 展開可能な **[Apply your trial] \(試用版を適用)** をクリックすると、すべてのサブスクリプションとワークスペースの一覧を各試用版の資格の状態と共に表示できます。
   -    試用版の対象にならないサブスクリプションとワークスペースをアップグレードできます。
   -    対象となるワークスペースとサブスクリプションを選択すると、ご自身の試用版を開始できます。
3. **[無料体験する]** をクリックして、選択したサブスクリプションでご自身の試用版を開始します。


  ![セキュリティのアラート][9]

## <a name="automate-data-collection"></a>自動データ収集
Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure VM と非 Azure コンピューターからデータを収集します。 データは、Microsoft Monitoring Agent を使用して収集されます。Microsoft Monitoring Agent は、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 既定では、Security Center によって新しいワークスペースが作成されます。

自動プロビジョニングを有効にすると、Security Center は、サポートされているすべての Azure VM と新しく作成される VM に Microsoft Monitoring Agent をインストールします。 自動プロビジョニングを強くお勧めします。

Microsoft Monitoring Agent の自動プロビジョニングを有効にするには、次の手順に従います。

1. Security Center のメイン メニューで、**[セキュリティ ポリシー]** を選択します。
2. サブスクリプションの行で、**[設定の編集>]** を選択します。
3. **[データ収集]** タブで、**[自動プロビジョニング]** を **[オン]** に設定します。
4. **[保存]** を選択します。
****
  ![自動プロビジョニングを有効にする][6]

Azure VM のこの新たな洞察により、Security Center は、システムの更新状態、OS セキュリティ構成、エンドポイント保護に関連する追加の推奨事項を提供し、追加のセキュリティ警告を生成できます。

  ![Recommendations][8]

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 引き続き次のクイックスタートとチュートリアルを行う予定の場合、Standard レベルの実行を継続して、自動プロビジョニングを有効のままにしてください。 続行しないまたは Free レベルに戻したい場合:

1. Security Center のメイン メニューに戻り、**[セキュリティ ポリシー]** を選択します。
2. Free に戻すサブスクリプションの行で、**[設定の編集>]** を選択します。
3. **[価格レベル]**、**[Free]** の順に選択して、Standard レベルから Free レベルにサブスクリプションを変更します。
5. **[保存]** を選択します。

自動プロビジョニングを無効にする場合:

1. Security Center のメイン メニューに戻り、**[セキュリティ ポリシー]** を選択します。
2. 自動プロビジョニングを無効にするサブスクリプションの行で、**[設定の編集>]** を選択します。
3. **[データ収集]** タブで、**[自動プロビジョニング]** を **[オフ]** に設定します。
4. **[保存]** を選択します。

>[!NOTE]
> 自動プロビジョニングを無効しても、Microsoft Monitoring Agent がプロビジョニングされている Azure VM からエージェントは削除されません。 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。
>

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Standard レベルにアップグレードし、ハイブリッド クラウド ワークロード全体での統合セキュリティの管理と脅威保護のために Microsoft Monitoring Agent をプロビジョニングしました。 Security Center の詳しい使用方法を学習するには、オンプレミスおよび他のクラウドの Windows コンピューターのオンボードに関するクイックスタートに進みます。

> [!div class="nextstepaction"]
> [クイック スタート:Windows コンピューターでの Azure Security Center の利用開始](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
