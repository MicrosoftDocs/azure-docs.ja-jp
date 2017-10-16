---
title: "Azure Security Center でのデータ収集 | Microsoft Docs"
description: " Azure Security Center のデータ収集を有効にする方法について説明します。 "
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
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-in-azure-security-center"></a>Azure Security Center でのデータ収集
Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM) と非 Azure コンピューターからデータを収集します。 データは、Microsoft Monitoring Agent を使用して収集されます。Microsoft Monitoring Agent は、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システムのログ (Windows イベント ログ)、実行中のプロセス、コンピューター名、IP アドレス、ログイン ユーザー、テナント ID などがあります。 また、クラッシュ ダンプ ファイルもワークスペースにコピーします。

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Microsoft Monitoring Agent の自動プロビジョニングの有効化     
自動プロビジョニングを有効にすると、Security Center は、サポートされているすべての Azure VM と新しく作成される VM に Microsoft Monitoring Agent をプロビジョニングします。 自動プロビジョニングを使用することを強くお勧めします。自動プロビジョニングは、Security Center の Standard レベルのサブスクリプションでは必須です。

> [!NOTE]
> 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。 詳細については、この記事の「[自動プロビジョニングの無効化](security-center-enable-data-collection.md#disable-automatic-provisioning)」をご覧ください。 自動プロビジョニングが無効になっていても、VM ディスクのスナップショットとアーティファクトの収集は有効です。
>
>

Microsoft Monitoring Agent の自動プロビジョニングを有効にするには、次の手順に従います。
1. Security Center のメイン メニューで、**[セキュリティ ポリシー]** を選択します。
2. サブスクリプションを選択します。
3. **[セキュリティ ポリシー]** で **[データ収集]** を選択します。
4. **[オンボード]** の **[オン]** を選択して、自動プロビジョニングを有効にします。
5. [ **保存**] を選択します。

![自動プロビジョニングを有効にする][1]

## <a name="default-workspace-configuration"></a>既定のワークスペース構成
Security Center によって収集されたデータは、Log Analytics ワークスペースに保存されます。  Azure VM から収集したデータを、Security Center によって作成されたワークスペースと自分で作成した既存のワークスペースのどちらに保存するかを選択できます。

既存の Log Analytics ワークスペースを使用する場合、次の要件があります。
- 選択した Azure サブスクリプションにワークスペースを関連付ける必要があります。
- ワークスペースにアクセスするには、少なくとも読み取りアクセス許可が必要です。

既存の Log Analytics ワークスペースを選択するには、次の手順に従います。

1. **[セキュリティ ポリシー - データ収集]** で、**[Use another workspace]\(別のワークスペースを使用する\)** を選択します。

   ![既存のワークスペースを選択する][2]

2. プルダウン メニューから、収集したデータを保存するワークスペースを選択します。

> [!NOTE]
> プルダウン メニューには、Azure サブスクリプション内の、アクセスできるワークスペースだけが表示されます。
>
>

3. [ **保存**] を選択します。
4. **[保存]** をクリックすると、監視対象の VM を再構成するかどうかをたずねられます。

   - 新しいワークスペース設定を新しい VM にのみ適用する場合は、**[いいえ]** を選択します。 この場合、新しいワークスペース設定は、エージェントの新しいインストール (Microsoft Monitoring Agent がインストールされていない、新たに検出された VM) にのみ適用されます。
   - 新しいワークスペース設定をすべての VM に適用する場合は、**[はい]** を選択します。 この場合、Security Center によって作成されたワークスペースに接続されているすべての VM が、新しいターゲット ワークスペースに再接続されます。

   > [!NOTE]
   > [はい] を選択した場合、すべての VM が新しいターゲット ワークスペースに再接続されるまで、Security Center によって作成されたワークスペースを削除しないでください。 ワークスペースの削除が早すぎると、この操作は失敗します。
   >
   >

   - 操作を取り消すには、**[キャンセル]** をクリックします。

   ![既存のワークスペースを選択する][3]

## <a name="data-collection-tier"></a>データ収集レベル
Security Center では、調査、監査、脅威検出のための十分なイベントを保持しながら、イベントのボリュームを減らすことができます。 エージェントによって収集されるイベントの 4 つのセットの中から、サブスクリプションとワークスペースに最適なフィルタリング ポリシーを選択できます。

- **すべてのイベント** - すべてのイベントを収集する必要があるお客様向けです。 既定のプランです。
- **共通** - これは、ほとんどのお客様のニーズを満たすイベントのセットです。このセットでは、完全な監査証跡が可能です。
- **最小** - イベント ボリュームを最小限に抑える必要があるお客様向けの小さなイベント セットです。
- **なし** - セキュリティ ログと AppLocker ログからのセキュリティ イベントの収集を無効にします。 このオプションを選択した場合、セキュリティ ダッシュボードには、Windows ファイアウォールのログと、マルウェア対策、ベースライン、更新などの事前評価だけが表示されます。

> [!NOTE]
> これらのセットは、一般的なシナリオに対応するように設計されています。 実装前に、どのセットがニーズに合うかを必ず評価してください。
>
>

Microsoft では、**共通**イベント セットと**最小**イベント セットに属するイベントを決定するために、お客様や業界標準化団体と協力して、各イベントがフィルター処理されない頻度とイベントの使用方法を確認しました。 このプロセスでは次のガイドラインが使用されました。

- **最小** - このセットは、ボリュームが非常に小さく、侵害が成功したことを示すイベントと重要なイベントだけを対象とします。 たとえば、このセットにはユーザーの成功したログインと失敗したログイン (イベント ID 4624、4625) が含まれますが、監査には重要であっても検出には重要ではない、比較的ボリュームの大きなログアウトは含まれません。 このセットのデータ量のほとんどは、ログイン イベントとプロセス作成イベント (イベント ID 4688) です。
- **共通** - このセットでは、完全なユーザー監査証跡を提供します。 たとえば、このセットには、ユーザー ログインとユーザー ログオフ (イベント ID 4634) の両方が含まれます。 セキュリティ グループの変更などの監査アクション、ドメイン コントローラーの主要な Kerberos 操作、業界組織が推奨するその他のイベントが含まれます。

すべてのイベントではなく、共通セットを選択する主な動機は、特定のイベントを除外することではなく、ボリュームを減らすことであるため、共通セットにはボリュームが非常に小さいイベントも含まれています。

各セットのすべてのセキュリティ イベント ID と AppLocker イベント ID を次に示します。

   ![イベント ID][4]

フィルタリグ ポリシーを選択するには、次の手順に従います。
1. **[Security policy & settings]\(セキュリティ ポリシーと設定\)** ブレードの **[セキュリティ イベント]** で、フィルタリング ポリシーを選択します。
2. [ **保存**] を選択します。

   ![フィルタリング ポリシーを選択する][5]

## <a name="disable-automatic-provisioning"></a>自動プロビジョニングの無効化
リソースの自動プロビジョニングは、セキュリティ ポリシーでこの設定をオフにすることで、いつでも無効にすることができます。 セキュリティ アラートや、システムの更新プログラム、OS の脆弱性、Endpoint Protection に関する推奨事項を取得するために、自動プロビジョニングを使用することを強くお勧めします。

> [!NOTE]
> 自動プロビジョニングを無効しても、Microsoft Monitoring Agent がプロビジョニングされている Azure VM からエージェントは削除されません。
>
>

1. Security Center のメイン メニューに戻り、[セキュリティ ポリシー] を選択します。

   ![自動プロビジョニングを無効にする][6]

2. 自動プロビジョニングを無効にするサブスクリプションを選択します。
3. **[セキュリティ ポリシー - データ収集]** ブレードで、**[オンボード]** の **[オフ]** を選択して、自動プロビジョニングを無効にします。
4. [ **保存**] を選択します。  

## <a name="next-steps"></a>次のステップ
この記事では、Security Center のデータ収集と自動プロビジョニングのしくみについて説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」 -- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」 - Security Center でデータがどのように管理および保護されているかを説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
