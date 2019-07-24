---
title: Azure Security Center での Just-In-Time 仮想マシン アクセス | Microsoft Docs
description: このドキュメントでは、Azure Security Center での Just-In-Time VM アクセスにより、Azure 仮想マシンへのアクセスを制御しやすくする方法を示します。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: monhaber
ms.openlocfilehash: 66a7171aff7b9bab5f320df1d71e9cab4ce0477d
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758298"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Just-In-Time を使用した仮想マシン アクセスの管理

Just-In-Time (JIT) 仮想マシン (VM) アクセスを使用すると、Azure VM への受信トラフィックをロックダウンすることができるので、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。

> [!NOTE]
> Just-In-Time 機能は、Security Center の Standard レベルで利用できます。  Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="attack-scenario"></a>攻撃シナリオ

ブルート フォース攻撃では、VM にアクセスする手段として一般に管理ポートがターゲットとされます。 アクセスに成功した場合、攻撃者は VM の制御を奪い、攻撃対象の環境への足掛かりを築くことができます。

ブルート フォース攻撃への露出を減らす方法の 1 つとして、ポートの開放時間を制限するという方法があります。 管理ポートを常に開放しておく必要はありません。 管理ポートを開放しておく必要があるのは、管理タスクやメンテナンス タスクを実行する場合など、VM に接続している間だけです。 Just-In-Time が有効になっている場合、Security Center では[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md#security-rules) (NSG) ルールが使用されます。このルールにより管理ポートへのアクセスが制限されるため、攻撃者は管理ポートをターゲットにすることができなくなります。

![Just-In-Time のシナリオ](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT アクセスはどのように機能しますか?

Just-In-Time が有効になっている場合、Security Center では NSG ルールの作成により Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックがロックダウンされるポートを選択します。 これらのポートは、Just-In-Time ソリューションによって制御されます。

ユーザーが VM へのアクセスを要求すると、VM へのアクセスを正常に要求できるように許可する[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) アクセス許可が、そのユーザーにあるかどうかが、Security Center によってチェックされます。 要求が承認されると、Security Center では、選択したポートおよび要求されたソース IP アドレスまたは範囲への受信トラフィックを指定された時間だけ許可するように、ネットワーク セキュリティ グループ (NSG) が自動的に構成されます。 指定された時間が経過すると、Security Center により NSG が以前の状態に復元されます。 既に確立されているこれらの接続は中断されません。ただし次のようになります。

> [!NOTE]
> Security Center の Just-In-Time VM アクセスでは、現在 Azure Resource Manager 経由でデプロイされた VM のみがサポートされています。 クラシック デプロイ モデルと Resource Manager デプロイ モデルの詳細については、[Azure Resource Manager とクラシック デプロイの比較](../azure-resource-manager/resource-manager-deployment-model.md)に関するページを参照してください。
>
>

以下の手順で JIT にアクセスできます。
- [Azure Security Center での JIT アクセスの使用](#jit-asc)
- [Azure VM ブレードでの JIT アクセスの使用](#jit-vm)

## Azure Security Center での JIT アクセスの使用<a name="jit-asc"></a>

1. **[Security Center]** ダッシュボードを開きます。

2. 左のウィンドウで、**[Just-in-time VM access] (Just-In-Time VM アクセス)** を選択します。

![[Just-in-time VM access] (Just-In-Time VM アクセス) タイル](./media/security-center-just-in-time/just-in-time.png)

**[Just-in-time VM access] (Just-In-Time VM アクセス)** ウィンドウが開きます。

![[Just-in-time VM access] (Just-In-Time VM アクセス) タイル](./media/security-center-just-in-time/just-in-time-access.png)

**[Just-in-time VM access] (Just-In-Time VM アクセス)** には、VM の状態に関する情報が表示されます。

- **[構成済み]** - Just-In-Time VM アクセスをサポートするように構成されている VM。 表示されるデータは 1 週間以内のものであり、VM ごとに承認済みの要求の数、最終アクセス日時、最後のユーザーの情報が含まれます。
- **[推奨]** - Just-In-Time VM アクセスをサポートできるが、そのように構成されてはいない VM。 これらの VM では Just-In-Time VM アクセス制御を有効にすることをお勧めします。 [Just-In-Time アクセス ポリシーの構成](#jit-config)に関するページを参照してください。
- **[推奨なし]** - 以下のような VM には、ジャスト イン タイム VM アクセスは推奨されない場合があります。
  - NSG がない - Just-In-Time ソリューションには設定済みの NSG が必要です。
  - クラシック VM - Security Center の Just-In-Time VM アクセスでは、現在 Azure Resource Manager 経由でデプロイされた VM のみがサポートされています。 Just-In-Time ソリューションではクラシック デプロイはサポートされていません。
  - その他 - VM がこのカテゴリに含まれるのは、サブスクリプションまたはリソース グループのセキュリティ ポリシー内で Just-In-Time ソリューションが無効になっている場合か、VM にパブリック IP と設定済みの NSG がない場合です。

### JIT アクセス ポリシーの構成<a name="jit-config"></a>

有効にする VM を選択するには、以下の手順に従います。

1. **[Just-in-time VM access] (Just-In-Time VM アクセス)** で **[推奨]** タブを選択します。

   ![Just In Time VM アクセスの有効化](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. **[仮想マシン]** で、有効にする VM を選択します。 VM の横にチェックマークが付きます。
3. **[<選択した VM 数> 台の VM で JIT を有効にする]** を選択します。
   1. このブレードには、Azure Security Center で推奨される既定のポートが表示されます。
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
   2. カスタム ポートを構成することもできます。 これを行うには、**[追加]** を選択します。 
   3. **[ポート構成の追加]** で、既定でもカスタムでも構成するポートごとに、以下の設定をカスタマイズできます。
      - **[プロトコルの種類]** - 要求が承認されたときにこのポートで許可されるプロトコル。
      - **[Allowed source IP addresses] (許可されるソース IP アドレス)** - 要求が承認されたときにこのポートで許可される IP 範囲。
      - **[最大要求時間]** - 特定のポートを開放しておくことができる最大時間枠。

4. **[保存]** を選択します。


> [!NOTE]
>VM に対して JIT VM アクセスが有効になっている場合、Azure Security Center では、関連付けられているネットワーク セキュリティ グループ内の選択されたポートについて、"すべての受信トラフィックを拒否" という規則が作成されます。 選択したポートに対して他の規則が作成されている場合は、既存の規則が新しい "すべての受信トラフィックを拒否" 規則よりも優先されます。 選択したポートに既存の規則がない場合は、新しい "すべての着信トラフィックを拒否する" 規則がネットワーク セキュリティ グループで最優先されます。
>

### <a name="request-jit-access-to-a-vm"></a>VM への JIT アクセスを要求する

VM へのアクセス権を要求するには、以下の手順に従います。
1.  **[Just-in-time VM access] (Just-In-Time VM アクセス)** で、**[構成済み]** を選択します。
2.  **[VM]** で、Just-In-Time アクセスを有効にする VM を確認します。
3.  **[アクセス権の要求]** を選択します。 
  ![VM へのアクセス権を要求する](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  **[アクセス権の要求]** では、開放するポート、ポートの開放先のソース IP アドレス、ポートを開放しておく時間枠を VM ごとに構成します。 Just-In-Time ポリシーで構成されているポートへのアクセス権のみ要求できます。 ポートごとに Just-In-Time ポリシーから派生した最大許容時間があります。
5.  **[Open ports]\(ポートを開く\)** を選択します。

> [!NOTE]
> アクセスを要求しているユーザーがプロキシの背後にいる場合は、**My IP** オプションが機能しない場合があります。 組織のすべての IP アドレス範囲を定義する必要がある場合があります。

### <a name="editing-a-jit-access-policy"></a>JIT アクセス ポリシーの編集

VM の既存の Just-In-Time ポリシーを変更する場合、その VM 用に保護する新しいポートを追加して構成するか、既に保護されているポートに関連するその他の設定を変更します。

VM の既存の Just-In-Time ポリシーを編集するには:
1. **[構成済み]** タブの **[VM]** で、ポートを追加する VM の行内にある 3 つの点をクリックしてその VM を選択します。 
2. **[編集]** を選択します。
3. **[JIT VM アクセス構成]** では、既に保護されているポートの既存の設定を編集するか、新しいカスタム ポートを追加できます。 詳しくは、[Just-In-Time アクセス ポリシーの構成](#jit-config)に関するページをご覧ください。 
  ![JIT VM アクセス](./media/security-center-just-in-time/edit-policy.png)

## Azure VM ブレードでの JIT アクセスの使用<a name="jit-vm"></a>

作業を容易にするため、Azure の VM ブレード内から直接 JIT を使用して VM に接続できます。

### <a name="configuring-a-just-in-time-access-policy"></a>Just-In-Time アクセス ポリシーの構成 
VM への Just-In-Time アクセスのロールアウトを容易にするには、VM 内からの直接的な Just-In-Time アクセスのみを許可するように VM を設定できます。

1. Azure portal で、**[仮想マシン]** を選択します。
2. Just-In-Time アクセスに制限する仮想マシンをクリックします。
3. メニューで **[構成]** をクリックします。
4. **[Just-In-Time アクセス]** で **[Just-In-Time ポリシーを有効にする]** をクリックします。 

これにより、以下の設定を使用する VM の Just-In-Time アクセスが有効になります。

- Windows サーバー:
    - RDP ポート 3389
    - 3 時間の最大許容アクセス
    - 許可されるソース IP アドレスは [すべて] に設定されます
- Linux サーバー:
    - SSH ポート 22
    - 3 時間の最大許容アクセス
    - 許可されるソース IP アドレスは [すべて] に設定されます
     
VM で Just-In-Time が既に有効になっている場合、VM の構成ページに移動すると、Just-In-Time が有効になっていることが示され、リンクを使用して Azure Security Center でポリシーを開き、設定を確認および変更できます。

![VM での JIT の構成](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>VM への JIT アクセスの要求

Azure portal では、VM に接続しようとすると、Azure は、Just-In-Time アクセス ポリシーをその VM 上で構成しているかどうかを確認します。

- VM 上で JIT を構成していない場合、JIT ポリシーを構成するように求められます。

  ![JIT プロンプト](./media/security-center-just-in-time/jit-prompt.png)

- VM 上で JIT を構成している場合、**[アクセスの要求]** をクリックして、その VM 用に設定された JIT ポリシーに従ってアクセス可能にすることができます。 アクセスは、次の規定のパラメーターを使用して要求します。
    - **接続元 IP アドレス**:"任意" (*) (変更できません)
    - **時間範囲**:3 時間 (変更できません)
    - **ポート番号**: Windows の場合は RDP ポート 3389/Linux の場合はポート 22 (ポート番号は、**[仮想マシンに接続する]** ダイアログ ボックスで変更できます。)


  >![JIT のアクセスの要求](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>JIT アクセス アクティビティの監査

ログ検索を使用して VM アクティビティについての情報が得ることができます。 ログを表示するには、以下の手順に従います。

1. **[Just-in-time VM access] (Just-In-Time VM アクセス)** で、**[構成済み]** タブを選択します。
2. **[VM]** で、情報を表示する VM の行内にある 3 つの点をクリックしてその VM を選択します。 メニューが開きます。
3. メニューで **[アクティビティ ログ]** を選択します。 **[アクティビティ ログ]** が開きます。

   ![[アクティビティ ログ] を選択する](./media/security-center-just-in-time/select-activity-log.png)

   **[アクティビティ ログ]** には、選択した VM の前回の操作、時間、日付、サブスクリプションがフィルター処理されて表示されます。

**[すべての項目を csv としてダウンロードするにはここをクリックしてください。]** を選択すると、ログ情報をダウンロードできます。

フィルターを変更し、**[適用]** を選択して検索ログを作成します。


## <a name="permissions-needed-to-configure-and-use-jit"></a>JIT を構成および使用するために必要なアクセス許可

ユーザーが VM 用に JIT ポリシーを構成したり編集したりできるように、これらの必要な特権を設定します。

これらの*アクション*をロールに割り当てます。 
- VM に関連付けられているサブスクリプションまたはリソース グループの範囲:
  - Microsoft.Security/locations/jitNetworkAccessPolicies/write
- VM のサブスクリプションまたはリソース グループの範囲:
  - Microsoft.Compute/virtualMachines/write 

ユーザーが VM への JIT アクセスを正常に要求できるようにこれらの特権を設定します。これらの*アクション*をユーザーに割り当てます。
- VM に関連付けられているサブスクリプションまたはリソース グループの範囲:
  - Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/ initiate/action
- VM のサブスクリプションまたはリソース グループの範囲:
  - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>プログラムで JIT を使用する
REST API および PowerShell から Just-In-Time を設定し使用できます。

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>REST API による Just-In-Time VM アクセスの使用

Just-In-Time VM アクセス機能は、Azure Security Center API から使用できます。 この API を使用すると、構成済みの VM に関する情報を取得したり、新しい VM を追加したり、VM へのアクセスを要求したりすることができます。 Just-In-Time REST API について詳しくは、「[Jit Network Access Policies (JIT ネットワーク アクセス ポリシー)](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)」をご覧ください。

### <a name="using-jit-vm-access-via-powershell"></a>PowerShell による JIT VM アクセスの使用 

PowerShell による Just-In-Time VM アクセス ソリューションを使用するには、公式の Azure Security Center の PowerShell コマンド (つまり、`Set-AzJitNetworkAccessPolicy`) を使用します。

次の例では、特定の VM 上で Just-In-Time VM アクセス ポリシーを設定し、以下のように設定します。
1.  ポート 22 と 3389 を閉じます。
2.  承認された要求ごとに開けるように、それぞれ最大 3 時間の時間枠を設定します。
3.  アクセス権を要求するユーザーがソース IP アドレスを制御できるようにし、承認された Just-In-Time アクセス要求時に正常なセッションを確立できるようにします。

これを実現するには、PowerShell で以下を実行します。

1.  VM に対する Just-In-Time VM アクセス ポリシーを保持する変数を割り当てます。

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  VM の Just-In-Time VM アクセス ポリシーを配列に挿入します。
    
        $JitPolicyArr=@($JitPolicy)

3.  選択した VM で Just-In-Time VM アクセス ポリシーを構成します。
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>VM へのアクセス権の要求

次の例では、特定の IP アドレスおよび特定の期間にポート 22 を開くことを要求する、特定の VM への Just-In-Time VM アクセス要求が示されています。

PowerShell で以下を実行します。
1.  VM 要求アクセス プロパティを構成します。

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  配列内に VM アクセス要求パラメーターを挿入します。

        $JitPolicyArr=@($JitPolicyVm1)
3.  要求アクセスを送信します (手順 1 で取得したリソース ID を使用)。

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

詳細については、PowerShell コマンドレットのドキュメントをご覧ください。


## <a name="next-steps"></a>次の手順
この記事では、Security Center の Just-In-Time VM アクセスを活用して Azure 仮想マシンへのアクセスを制御する方法について説明しました。

セキュリティ センターの詳細については、次を参照してください。

- [セキュリティ ポリシーの設定](tutorial-security-policy.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [セキュリティに関する推奨事項の管理](security-center-recommendations.md) -- 推奨事項に従って Azure リソースを保護する方法について説明します。
- [セキュリティ正常性の監視](security-center-monitoring.md) -- Azure リソースの正常性を監視する方法について説明します。
- [セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) -- セキュリティの警告の管理と対応の方法について説明します。
- [パートナー ソリューションの監視](security-center-partner-solutions.md) -- パートナー ソリューションの正常性状態を監視する方法について説明します。
- [Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) -- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](https://blogs.msdn.microsoft.com/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

