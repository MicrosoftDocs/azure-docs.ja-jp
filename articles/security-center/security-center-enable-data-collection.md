---
title: Azure Security Center でのデータ収集 | Microsoft Docs
description: " Azure Security Center のデータ収集を有効にする方法について説明します。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2019
ms.author: v-mohabe
ms.openlocfilehash: 86d48360e37f26d19d15c62a8109c030d421c661
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551880"
---
# <a name="data-collection-in-azure-security-center"></a>Azure Security Center でのデータ収集
Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM)、仮想マシン スケール セット、IaaS コンテナー、非 Azure (オンプレミスを含む) コンピューターからデータを収集します。 データは、Microsoft Monitoring Agent を使用して収集されます。Microsoft Monitoring Agent は、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システム ログ (Windows イベント ログ)、実行中のプロセス、マシン名、IP アドレス、ログイン ユーザーなどがあります。 また、Microsoft Monitoring Agent エージェントでは、クラッシュ ダンプ ファイルもワークスペースにコピーします。

不足している更新プログラム、OS のセキュリティ設定ミス、エンドポイント保護の有効性、正常性と脅威の検出を可視化するためには、データ収集が欠かせません。 

この記事では、Microsoft Monitoring Agent をインストールする方法と、収集されたデータの格納先となる Log Analytics ワークスペースを設定する方法についてのガイダンスを提供します。 データ収集を有効にするためには、両方の操作が必要となります。 

> [!NOTE]
> - データ収集を必要とするのは、コンピューティング リソース (VM、仮想マシン スケール セット、IaaS コンテナー、非 Azure コンピューター) だけです。 Azure Security Center の機能は、エージェントをプロビジョニングしなくても利用することができます。ただしセキュリティは限られており、前述の機能はサポートされません。  
> - サポートされるプラットフォームの一覧については、「[Azure Security Center でサポートされているプラットフォーム](security-center-os-coverage.md)」を参照してください。
> - 新しいワークスペースと既存のワークスペースのどちらを使用する場合でも、Log Analytics にデータを格納すると、データ ストレージに対して追加料金が発生する可能性があります。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。

## Microsoft Monitoring Agent の自動プロビジョニングの有効化<a name="auto-provision-mma"></a>

マシンからデータを収集するには、Microsoft Monitoring Agent がインストールされている必要があります。  エージェントのインストールは自動で実行できますが (推奨)、手動でインストールすることもできます。  

>[!NOTE]
> 自動プロビジョニングは、既定ではオフです。 既定でインストールするように Security Center を設定するには、自動プロビジョニングを**オン**に設定してください。
>

自動プロビジョニングをオンにすると、Security Center は、サポートされているすべての Azure VM と新しく作成される VM に Microsoft Monitoring Agent をプロビジョニングします。 自動プロビジョニングを強くお勧めしますが、エージェントを手動でインストールすることもできます。 [Microsoft Monitoring Agent の拡張機能をインストールする方法を参照してください](#manualagent)。



Microsoft Monitoring Agent の自動プロビジョニングを有効にするには、次の手順に従います。
1. Security Center メイン メニューの **[Pricing & settings]\(価格と設定\)** を選択します。
2. 適切なサブスクリプションをクリックします。

   ![サブスクリプションを選択します。][7]

3. **[データ収集]** を選択します。
4. **[Auto Provisioning] (自動プロビジョニング)** で **[オン]** を選択して、自動プロビジョニングを有効にします。
5. **[保存]** を選択します。

   ![自動プロビジョニングを有効にする][1]

>[!NOTE]
> - 既存のインストール済み環境をプロビジョニングする手順については、「[既にインストールされているエージェントが存在する場合の自動プロビジョニング](#preexisting)」を参照してください。
> - 手動プロビジョニングの手順については、[Microsoft Monitoring Agent 拡張機能の手動インストール](#manualagent)に関するセクションを参照してください。
> - 自動プロビジョニングをオフにする手順については、「[自動プロビジョニングを無効にする](#offprovisioning)」を参照してください。
> - PowerShell を使用して Security Center をオンボードする方法については、「[Automate onboarding of Azure Security Center using PowerShell](security-center-powershell-onboarding.md)」 (PowerShell を使用して Azure Security Center のオンボードを自動化する) を参照してください。
>

## <a name="workspace-configuration"></a>ワークスペースの構成
Security Center によって収集されたデータは、Log Analytics ワークスペースに保存されます。  Azure VM から収集したデータを、Security Center によって作成されたワークスペースと自分で作成した既存のワークスペースのどちらに保存するかを選択できます。 

ワークスペースの構成はサブスクリプションごとに設定されますが、多数のサブスクリプションで同じワークスペースを使うこともできます。

### <a name="using-a-workspace-created-by-security-center"></a>Security Center によって作成されたワークスペースを使用する

データの格納先となる既定のワークスペースは、Security Center で自動的に作成することができます。 

Security Center によって作成されたワークスペースを選択するには、次の手順に従います。

1. **[既定のワークスペース構成]** で、[Security Center によって作成されたワークスペースを使用] を選択します。
   ![価格レベルの選択][10] 

1. **[Save]** をクリックします。<br>
    Security Center で新しいリソース グループとその位置情報の既定のワークスペースが作成され、エージェントがそのワークスペースに接続されます。 ワークスペースとリソース グループの名前付け規則は次のとおりです。<br>
   **ワークスペース: DefaultWorkspace-[subscription-ID]-[geo]<br> リソース グループ: DefaultResourceGroup-[geo]**

   複数の地理的位置情報からの VM がサブスクリプションに含まれている場合、Security Center では複数のワークスペースを作成します。 複数のワークスペースは、データのプライバシー ルールを維持するために作成されます。
1. サブスクリプションに設定された価格レベルに従い、ワークスペースで Security Center ソリューションが自動的に有効になります。 

> [!NOTE]
> Security Center で作成されたワークスペースの Log Analytics 価格レベルは、Security Center の課金に影響しません。 Security Center の課金は、常に Security Center セキュリティ ポリシーとワークスペースにインストールされているソリューションに基づいています。 Free レベルの場合、既定のワークスペースで *SecurityCenterFree* ソリューションが有効化されます。 Standard レベルの場合、既定のワークスペースで *Security* ソリューションが有効化されます。
> Log Analytics にデータを格納すると、データ ストレージに対して追加料金が発生する可能性があります。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。

既存の Log Analytics アカウントの詳細については、「[既存の Log Analytics ユーザー](security-center-faq.md#existingloganalyticscust)」を参照してください。

### <a name="using-an-existing-workspace"></a>既存のワークスペースの使用

既に Log Analytics ワークスペースがある場合は、同じワークスペースを使用することをお勧めします。

既存の Log Analytics ワークスペースを使用するには、ワークスペースに対する読み取りアクセス許可と書き込みアクセス許可が必要です。

> [!NOTE]
> 既存のワークスペースに接続した Azure VM には、そのワークスペースで有効になっているソリューションが適用されます。 そのため有料ソリューションの場合、追加料金が発生することがあります。 データのプライバシー上、選択したワークスペースが適切な地理的領域に存在することを確認してください。
> Log Analytics にデータを格納すると、データ ストレージに対して追加料金が発生する可能性があります。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。

既存の Log Analytics ワークスペースを選択するには、次の手順に従います。

1. **[Default workspace configuration] (既定のワークスペース構成)** で **[Use another workspace]\(別のワークスペースを使用する\)** を選択します。

   ![既存のワークスペースを選択する][2]

2. プルダウン メニューから、収集したデータを保存するワークスペースを選択します。

   > [!NOTE]
   > プルダウン メニューでは、ご利用のすべてのサブスクリプションにわたって、すべてのワークスペースを選択できます。 詳細については、「[クロス サブスクリプションのワークスペースの選択](security-center-enable-data-collection.md#cross-subscription-workspace-selection)」を参照してください。 ワークスペースへのアクセス許可が必要となります。
   >
   >

3. **[保存]** を選択します。
4. **[保存]** を選択すると、過去に既定のワークスペースに接続されていた監視対象の VM を再構成するかどうかをたずねられます。

   - 新しいワークスペース設定を新しい VM にのみ適用する場合は、 **[いいえ]** を選択します。 この場合、新しいワークスペース設定は、エージェントの新しいインストール (Microsoft Monitoring Agent がインストールされていない、新たに検出された VM) にのみ適用されます。
   - 新しいワークスペース設定をすべての VM に適用する場合は、 **[はい]** を選択します。 この場合、Security Center によって作成されたワークスペースに接続されているすべての VM が、新しいターゲット ワークスペースに再接続されます。

   > [!NOTE]
   > [はい] を選択した場合、すべての VM が新しいターゲット ワークスペースに再接続されるまで、Security Center によって作成されたワークスペースを削除しないでください。 ワークスペースの削除が早すぎると、この操作は失敗します。
   >
   >

   - 操作を取り消すには、 **[キャンセル]** をクリックします。

     ![既存のワークスペースを選択する][3]

5. Microsoft Monitoring Agent を設定するワークスペースの価格レベルを選択します。 <br>既存のワークスペースを使用するには、ワークスペースの価格レベルを設定します。 これにより、Security Center ソリューションがワークスペースにインストールされます (まだ存在しない場合)。

    a.  Security Center メイン メニューの **[Pricing & settings]\(価格と設定\)** を選択します。
     
    b.  エージェントを接続するワークスペースを選択します。
        ![ワークスペースを選択][7] c. 価格レベルを設定します。
        ![価格レベルの選択][9]
   
   >[!NOTE]
   >対象のワークスペースで **Security** ソリューションまたは **SecurityCenterFree** ソリューションが既に有効になっている場合、価格は自動的に設定されます。 

## <a name="cross-subscription-workspace-selection"></a>クロス サブスクリプションのワークスペースの選択
データの格納先となるワークスペースを選択するときには、ご利用のすべてのサブスクリプションにわたって、すべてのワークスペースを選択できます。 クロス サブスクリプションのワークスペースの選択を行うと、異なるサブスクリプションで実行されている仮想マシンからデータを収集し、選択したワークスペースに格納できます。 この選択は、社内で一元化されたワークスペースを使用していて、それをセキュリティ データの収集に使いたい場合に適しています。 ワークスペースを管理する方法について詳しくは、[ワークスペースのアクセス管理](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)に関するページをご覧ください。


## <a name="data-collection-tier"></a>データ収集レベル
Azure Security Center 内でデータ収集レベルを選択した場合、その影響を受けるのは Log Analytics ワークスペース内のセキュリティ イベントのストレージのみです。 Log Analytics エージェントでは、ご利用の Log Analytics ワークスペース (存在する場合) に格納するように選択したセキュリティ イベントのレベルに関係なく、Azure Security Center での脅威検出に必要なセキュリティ イベントが引き続き収集され分析されます。 ご利用のワークスペースにセキュリティ イベントを格納するように選択すると、そのワークスペース内のセキュリティ イベントの調査、検索、および監査が有効にされます。 
> [!NOTE]
> Log Analytics にデータを格納すると、データ ストレージに対して追加料金が発生する可能性があります。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。
> 
> ご利用のワークスペースに格納される 4 つのイベント セットの中から、サブスクリプションとワークスペースに最適なフィルタリング ポリシーを選択できます。 

- **なし** – セキュリティ イベントのストレージを無効にします。 これは、既定の設定です。
- **最小** - イベント ボリュームを最小限に抑える必要があるお客様向けの小さなイベント セットです。
- **共通** - これは、ほとんどのお客様のニーズを満たすイベントのセットです。このセットでは、完全な監査証跡が可能です。
- **すべてのイベント** - すべてのイベントを格納する必要があるお客様向けです。


> [!NOTE]
> これらのセキュリティ イベントは、Security Center の Standard レベルでのみ使用できます。 Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
これらのセットは、一般的なシナリオに対応するように設計されています。 実装前に、どのセットがニーズに合うかを必ず評価してください。
>
>

Microsoft では、**共通**イベント セットと**最小**イベント セットに属するイベントを決定するために、お客様や業界標準化団体と協力して、各イベントがフィルター処理されない頻度とイベントの使用方法を確認しました。 このプロセスでは次のガイドラインが使用されました。

- **最小** - このセットは、ボリュームが非常に小さく、侵害が成功したことを示すイベントと重要なイベントだけを対象とします。 たとえば、このセットにはユーザーの成功したログインと失敗したログイン (イベント ID 4624、4625) が含まれますが、監査には重要であっても検出には重要ではない、比較的ボリュームの大きいサインアウトは含まれません。 このセットのデータ量のほとんどは、ログイン イベントとプロセス作成イベント (イベント ID 4688) です。
- **共通** - このセットでは、完全なユーザー監査証跡を提供します。 たとえば、このセットには、ユーザー ログインとユーザー サインアウトの両方 (イベント ID 4634) が含まれます。 セキュリティ グループの変更などの監査アクション、ドメイン コントローラーの主要な Kerberos 操作、業界組織が推奨するその他のイベントが含まれます。

すべてのイベントではなく、共通セットを選択する主な動機は、特定のイベントを除外することではなく、ボリュームを減らすことであるため、共通セットにはボリュームが非常に小さいイベントも含まれています。

各セットのすべてのセキュリティ イベント ID と AppLocker イベント ID を次に示します。

| データ層 | 収集したイベント インジケーター |
| --- | --- |
| 最小限 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 一般 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - グループ ポリシー オブジェクト (GPO) を使用している場合は、プロセス作成イベント 4688 の監査ポリシーと、イベント 4688 内の *CommandLine* フィールドを有効にすることをお勧めします。 プロセス作成イベント 4688 の詳細については、Security Center の [FAQ](security-center-faq.md#what-happens-when-data-collection-is-enabled) を参照してください。 これらの監査ポリシーの詳細については、「[Audit Policy Recommendations](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)」(監査ポリシーの推奨事項) を参照してください。
> -  [適応型アプリケーション制御](security-center-adaptive-application.md)のデータ収集を有効にするために、Security Center は、ローカル AppLocker ポリシーを監査モードで構成して、すべてのアプリケーションを許可します。 これをきっかけとして AppLocker がイベントを生成するようになり、そのイベントが Security Center によって収集されて活用されます。 ただし、既に AppLocker ポリシーが構成されているマシンでは、このポリシーが構成されないので注意してください。 
> - Windows フィルタリング プラットフォーム イベント ([イベント ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)) を収集するには、[[フィルタリング プラットフォームの接続の監査]](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) を有効にする必要があります (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)。
>

フィルタリグ ポリシーを選択するには、次の手順に従います。
1. **[データ収集]** ページの **[セキュリティ イベント]** で、フィルタリング ポリシーを選択します。
2. **[保存]** を選択します。

   ![フィルタリング ポリシーを選択する][5]

### 既にインストールされているエージェントが存在する場合の自動プロビジョニング<a name="preexisting"></a> 

以下のユース ケースは、エージェントまたは拡張機能が既にインストールされていた場合の自動プロビジョニングの動作について記述したものです。 

- マシンに Microsoft Monitoring Agent がインストールされているものの、拡張機能としてはインストールされていない (ダイレクト エージェント)<br>
Microsoft Monitoring Agent が VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Microsoft Monitoring Agent 拡張機能がインストールされ、Microsoft Monitoring Agent が最新バージョンにアップグレードされる可能性があります。
インストールされているエージェントでは、既に構成されているワークスペースに引き続きレポートし、さらに Security Center で構成されたワークスペースにもレポートします (Windows マシンでは、マルチ ホームがサポートされています)。
構成されたワークスペースがユーザーのワークスペース (Security Center の既定のワークスペースではない) の場合、Security Center でそのワークスペースにレポートする VM とコンピューターからイベントの処理を開始するために、"security/"securityFree" ソリューションをインストールする必要があります。<br>
<br>
Linux マシンでは、エージェントのマルチ ホームはまだサポートされていません。そのため、既存のエージェントのインストールが検出されても、自動プロビジョニングは行われず、マシンの構成は変更されません。
<br>
2019 年 3 月 17 日より前に Security Center にオンボードされたサブスクリプションの既存のマシンでは、既存のエージェントが検出されると、Microsoft Monitoring Agent 拡張機能はインストールされず、マシンに影響はありません。 これらのマシンについては、マシンでのエージェントのインストールに関する問題を解決するために、"マシンの監視エージェント正常性の問題を解決する" の推奨事項を参照してください。

  
- マシンに System Center Operations Manager エージェントがインストールされている<br>
Security Center によって、Microsoft Monitoring Agent 拡張機能は、既存の Operations Manager と並行してインストールされます。 通常、既存の Operations Manager エージェントは引き続き Operations Manager サーバーに報告します。 Operations Manager エージェントと Microsoft Monitoring Agent は、このプロセス中に最新バージョンに更新される、共通のランタイム ライブラリを共有することに注意してください。
注 - Operations Manager エージェント バージョン 2012 がインストールされている場合は、自動プロビジョニングを有効に**しないでください**。<br>

- 既存の VM 拡張機能が存在する<br>
    - Monitoring Agent が拡張機能としてインストールされると、拡張機能の構成で 1 つのワークスペースのみにレポートできます。 Security Center は、ユーザー ワークスペースへの既存の接続をオーバーライドしません。 Security Center では、"security" または "securityFree" ソリューションがインストールされているという条件で、既に接続されているワークスペース内の VM からのセキュリティ データを保存します。 Security Center では、このプロセスで拡張機能のバージョンを最新バージョンにアップグレードする可能性があります。  
    - 既存の拡張機能がどのワークスペースにデータを送信しているのかを調べるには、[Azure Security Center との接続を確認](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/)するテストを実行します。 または、Log Analytics ワークスペースを開いてワークスペースを選択し、対象の VM を選択して、Log Analytics エージェント接続を調べることもできます。 
    - Log Analytics エージェントがクライアント ワークステーションにインストールされ、既存の Log Analytics ワークスペースにレポートする環境が整っている場合は、[Azure Security Center でサポートされるオペレーティング システム](security-center-os-coverage.md)の一覧で、ご利用のオペレーティング システムがサポートされているかどうかを確認します。詳しくは、「[既存の Log Analytics ユーザー](security-center-faq.md#existingloganalyticscust)」をご覧ください。
 
### 自動プロビジョニングを無効にする<a name="offprovisioning"></a>
リソースの自動プロビジョニングは、セキュリティ ポリシーでこの設定をオフにすることで、いつでも無効にすることができます。 


1. Security Center のメイン メニューに戻り、[セキュリティ ポリシー] を選択します。
2. 自動プロビジョニングを無効にするサブスクリプションの行で、 **[設定の編集]** をクリックします。
3. **[セキュリティ ポリシー - データ収集]** ブレードの **[Auto provisioning] (自動プロビジョニング)** で、 **[オフ]** を選択します。
4. **[保存]** を選択します。

   ![自動プロビジョニングの無効化][6]

自動プロビジョニングを無効 (オフ) にすると、既定のワークスペース構成セクションは表示されません。

オンになっていた自動プロビジョニングをオフにした場合の動作は次のとおりです。
-   新しい VM にはエージェントがプロビジョニングされなくなります。
-   Security Center は既定のワークスペースからのデータ収集を停止します。
 
> [!NOTE]
>  自動プロビジョニングを無効しても、Microsoft Monitoring Agent がプロビジョニングされている Azure VM からエージェントは削除されません。 OMS 拡張機能の削除については、「[Security Center にインストールされている OMS 拡張機能を削除するにはどうすればよいですか](security-center-faq.md#remove-oms)」をご覧ください。
>
    
## エージェントの手動プロビジョニング <a name="manualagent"></a>
 
Microsoft Monitoring Agent の手動インストールには、いくつかの方法があります。 手動インストールを行うときは、必ず自動プロビジョニングを無効にしてください。

### <a name="operations-management-suite-vm-extension-deployment"></a>Operations Management Suite VM 拡張機能のデプロイ 

Security Center がお使いの VM からセキュリティ データを収集して、推奨や通知を提示できるように、Microsoft Monitoring Agent を手動でインストールすることができます。
1. 自動プロビジョニングのオフを選択します。
2. ワークスペースを作成し、Microsoft Monitoring Agent を設定するワークスペースの価格レベルを設定します。

   a.  Security Center のメイン メニューで、 **[セキュリティ ポリシー]** を選択します。
     
   b.  エージェントを接続するワークスペースを選択します。 ワークスペースが存在するサブスクリプションが Security Center で使用しているサブスクリプションと同じであること、またそのワークスペースに対する読み取り/書き込みのアクセス許可があることを確認します。
       ![ワークスペースを選択][8]
3. 価格レベルを設定します。
   ![価格レベルの選択][9] 
   >[!NOTE]
   >対象のワークスペースで **Security** ソリューションまたは **SecurityCenterFree** ソリューションが既に有効になっている場合、価格は自動的に設定されます。 
   > 

4. Resource Manager テンプレートを使用して新しい VM にエージェントをデプロイする場合は、OMS 仮想マシン拡張機能をインストールします。

   a.  [Windows 用の OMS 仮想マシン拡張機能のインストール](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Linux 用の OMS 仮想マシン拡張機能のインストール](../virtual-machines/extensions/oms-linux.md)
5. 既存の VM に拡張機能をデプロイする場合は、「[Azure Virtual Machines に関するデータの収集](../azure-monitor/learn/quick-collect-azurevm.md)」の手順に従います。

   > [!NOTE]
   > 「**イベントとパフォーマンス データを収集する**」セクションは省略してもかまいません。
   >
6. PowerShell を使用して拡張機能をデプロイするには、PowerShell の次の例を使用します。
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. **[Log Analytics]** に移動し、 **[詳細設定]** をクリックします。
    
      ![ログ分析の設定][11]

   2. **ワークスペース ID** と**プライマリ キー**の値をコピーします。
  
      ![値をコピーする][12]

   3. public config と private config に次の値を設定します。
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Windows VM にインストールする場合:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Linux VM にインストールする場合:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> PowerShell を使用して Security Center をオンボードする方法については、「[Automate onboarding of Azure Security Center using PowerShell](security-center-powershell-onboarding.md)」 (PowerShell を使用して Azure Security Center のオンボードを自動化する) を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

-   自動プロビジョニングのインストールの問題を特定するには、「[エージェントの正常性の問題の監視](security-center-troubleshooting-guide.md#mon-agent)」を参照してください。

-  監視エージェントのネットワーク要件を確認するには、「[監視エージェントのネットワーク要件のトラブルシューティング](security-center-troubleshooting-guide.md#mon-network-req)」を参照してください。
-   手動によるオンボーディングの問題を特定するには、「[操作の管理スイートの契約時の問題をトラブルシューティングする方法 ](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)」を参照してください。

- 監視対象外の VM とコンピューターの問題を特定するには、「[監視対象外の VM およびコンピューター](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers)」を参照してください。

## <a name="next-steps"></a>次の手順
この記事では、Security Center のデータ収集と自動プロビジョニングのしくみについて説明しました。 セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* 「[Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
