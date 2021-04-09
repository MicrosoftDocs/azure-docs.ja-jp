---
title: Azure Security Center での Just-In-Time 仮想マシン アクセス | Microsoft Docs
description: このドキュメントでは、Azure Security Center での Just-In-Time (JIT) VM アクセスにより、Azure 仮想マシンへのアクセスを制御しやすくする方法を示します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 60ae36d80e34f27ed68c679f47edacf3e402417c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916152"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Just-In-Time アクセスを使用して管理ポートをセキュリティで保護する

Azure Security Center の Just-In-Time (JIT) 仮想マシン (VM) アクセス機能を使用して、Azure Virtual Machines への受信トラフィックをロックダウンします。 これにより、必要に応じて簡単に VM に接続できるようになる一方で、攻撃に晒される危険性は低くなります。

JIT の動作と基になるロジックの詳細については、[Just-In-Time の説明](just-in-time-explained.md)に関するページを参照してください。

このページでは、セキュリティ プログラムに JIT を組み込む方法について説明します。 学習内容は次のとおりです。 

- **VM で JIT を有効にする** - Security Center、PowerShell、または REST API を使用して、独自のカスタム オプションで 1 つ以上の VM に対して JIT を有効にすることができます。 または、Azure 仮想マシンから、ハードコーディングされた既定のパラメーターで JIT を有効にすることもできます。 有効にすると、JIT により、ネットワーク セキュリティ グループに規則が作成されて、Azure VM への受信トラフィックがロックダウンされます。
- **JIT が有効になっている VM へのアクセス権を要求する** - JIT の目的は、受信トラフィックがロックダウンされていても、必要に応じて Security Center で VM に簡単に接続できることを確実にすることです。 Security Center、Azure 仮想マシン、PowerShell、または REST API から、JIT が有効な VM へのアクセス権を要求できます。
- **アクティビティを監査する** - VM が適切にセキュリティで保護されていることを確実にするため、通常のセキュリティ チェックの一環として、JIT 対応の VM へのアクセスを確認します。   



## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|[サーバー用 Azure Defender](defender-for-servers-introduction.md) が必要|
|サポートされている VM:|![はい](./media/icons/yes-icon.png) Azure Resource Manager を介してデプロイされた VM。<br>![いいえ](./media/icons/no-icon.png) クラシック デプロイ モデルを使用してデプロイされた VM。 [これらのデプロイ モデルの詳細](../azure-resource-manager/management/deployment-models.md)。<br>![いいえ](./media/icons/no-icon.png) [Azure Firewall Manager](../firewall-manager/overview.md) によって制御される Azure Firewall によって保護されている VM。|
|必要なロールとアクセス許可:|**閲覧者** と **セキュリティ閲覧者** ロールは、JIT の状態とパラメーターの両方を表示できます。<br>JIT で動作するカスタム ロールを作成するには、「[JIT を構成して使用するために必要なアクセス許可は何ですか?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)」を参照してください。<br>VM への JIT アクセスを要求する必要があり、他の JIT 操作を実行しないユーザーに対して、最小特権のロールを作成するには、Security Center GitHub コミュニティ ページにある [Set-JitLeastPrivilegedRole スクリプト](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role)を使用します。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||


## <a name="enable-jit-vm-access"></a>JIT VM アクセスを有効にする <a name="jit-configure"></a>

Security Center またはプログラムを使用して、独自のカスタム オプションで、1 つ以上の VM に対して JIT VM アクセスを有効にすることができます。 

または、Azure 仮想マシンから、ハードコーディングされた既定のパラメーターで JIT を有効にすることもできます。

これらの各オプションについては、以下の個別のタブで説明します。

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Azure Security Center から VM で JIT を有効にする <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Azure Security Center での JIT VM アクセスの構成":::

Security Center から、JIT VM アクセスを有効にして構成することができます。

1. Azure Defender ダッシュボードを開き、高度な保護領域から **[Just In Time VM アクセス]** を選択します。

    **[Just-In-Time VM アクセス]** ページが開き、VM が次のタブにグループ化されます。

    - **[構成済み]** - Just-In-Time VM アクセスをサポートするように既に構成されている VM。 [構成済み] タブには VM ごとに以下が表示されます。
        - 過去 7 日間に承認された JIT 要求の数
        - 最終アクセス日時
        - 構成されている接続の詳細
        - 最後のユーザー
    - **[未構成]** - JIT は有効になっていないが、JIT をサポートできる VM。 これらの VM では JIT を有効にすることをお勧めします。
    - **[サポート外]** - JIT が有効になっておらず、機能をサポートしていない VM。 VM は次の理由によりこのタブに表示される場合があります。
      - ネットワーク セキュリティ グループ (NSG) がない - JIT には NSG を構成する必要があります
      - クラシック VM - Azure Resource Manager を使用してデプロイされた VM は JIT でサポートされますが、"クラシック デプロイ" はサポートされません。 [クラシックと Azure Resource Manager のデプロイ モデルの詳細](../azure-resource-manager/management/deployment-models.md)。
      - その他 - サブスクリプションまたはリソース グループのセキュリティ ポリシーで JIT ソリューションが無効になっている場合は、VM がこのタブに表示されることがあります。

1. **[未構成]** タブで、JIT で保護する VM をマークし、 **[VM で JIT を有効にする]** を選択します。 

    JIT VM アクセス ページが開き、Security Center で保護が推奨されるポートの一覧が表示されます。
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    既定の設定をそのまま使用するには、 **[保存]** を選択します。

1. JIT オプションをカスタマイズするには:

    - **[追加]** ボタンを使用してカスタム ポートを追加します。 
    - 一覧から既定のポートのいずれかを選択して変更します。

    ポート (カスタムおよび既定) ごとに、 **[ポート構成の追加]** ペインで次のオプションが提供されます。

    - **[プロトコル]** - 要求が承認されたときにこのポートで許可されるプロトコル
    - **[許可されているソース IP]** - 要求が承認されたときにこのポートで許可される IP 範囲
    - **[最大要求時間]** - 特定のポートを開放しておくことができる最大時間枠

     1. 必要に応じてポートのセキュリティを設定します。

     1. **[OK]** を選択します。

1. **[保存]** を選択します。



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Security Center を使用して JIT 対応の VM で JIT の構成を編集する <a name="jit-modify"></a>

VM に対して保護する新しいポートを追加して構成するか、既に保護されているポートに関連する他の設定を変更すると、VM の Just-In-Time の構成を変更できます。

VM の既存の JIT 規則を編集するには:

1. Azure Defender ダッシュボードを開き、高度な保護領域から **[適応型アプリケーション制御]** を選択します。

1. **[構成済み]** タブで、ポートを追加する VM を右クリックして、[編集] を選択します。 

    ![Azure Security Center での JIT VM アクセス構成の編集](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. **[JIT VM アクセス構成]** では、既に保護されているポートの既存の設定を編集するか、新しいカスタム ポートを追加できます。

1. ポートの編集が終わったら、 **[保存]** を選択します。
 


### <a name="azure-virtual-machines"></a>[**Azure 仮想マシン**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Azure 仮想マシンから VM で JIT を有効にする

Azure portal の Azure 仮想マシンページから、VM での JIT を有効にすることができます。

![Azure 仮想マシンでの JIT VM アクセスの構成](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> VM で Just-In-Time が既に有効になっている場合、その構成ページに移動すると、Just-In-Time が有効になっていることが示されます。リンクを使用して Security Center で [Just-In-Time VM アクセス] ページを開き、設定を表示して変更することができます。

1. [Azure portal](https://ms.portal.azure.com) から、 **[仮想マシン]** を検索して選択します。 

1. JIT で保護する仮想マシンを選択します。

1. メニューで **[構成]** を選択します。

1. **[Just-In-Time アクセス]** で、 **[Just-In-Time を有効にする]** を選択します。 

    これにより、以下の既定の設定を使用する VM の Just-In-Time アクセスが有効になります。

    - Windows マシン:
        - RDP ポート 3389
        - 3 時間の最大許容アクセス
        - 許可されるソース IP アドレスは [すべて] に設定されます
    - Linux マシン:
        - SSH ポート 22
        - 3 時間の最大許容アクセス
        - 許可されるソース IP アドレスは [すべて] に設定されます

1. これらの値のいずれかを編集するか、JIT 構成にポートを追加するには、Azure Security Center の Just-In-Time ページを使用します。

    1. Security Center のメニューから、 **[Just-In-Time VM アクセス]** を選択します。

    1. **[構成済み]** タブで、ポートを追加する VM を右クリックして、[編集] を選択します。 

        ![Azure Security Center での JIT VM アクセス構成の編集](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. **[JIT VM アクセス構成]** では、既に保護されているポートの既存の設定を編集するか、新しいカスタム ポートを追加できます。

    1. ポートの編集が終わったら、 **[保存]** を選択します。


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>PowerShell を使用して VM で JIT を有効にする

PowerShell を使用して Just-In-Time VM アクセスを有効にするには、公式の Azure Security Center PowerShell コマンドレット `Set-AzJitNetworkAccessPolicy` を使用します。

**例** - 次の規則を使用して、特定の VM で Just-In-Time VM アクセスを有効にします。

* ポート 22 と 3389 を閉じる
* 承認された要求ごとに開けるように、それぞれ最大 3 時間の時間枠を設定する
* アクセス権を要求しているユーザーがソース IP アドレスを制御するのを許可する
* アクセス権を要求しているユーザーが、承認された Just-In-Time アクセス要求時に正常なセッションを確立するのを許可する

以下の PowerShell コマンドで、この JIT 構成が作成されます。

1. VM に対する Just-In-Time VM アクセス規則を保持する変数を割り当てます。

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
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
    ```

1. VM の Just-In-Time VM アクセス規則を配列に挿入します。
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 選択した VM で Just-In-Time VM アクセス規則を構成します。
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    -Name パラメーターを使用して VM を指定します。 たとえば、2 つの異なる VM (VM1 と VM2) の JIT 構成を確立するには、```Set-AzJitNetworkAccessPolicy -Name VM1``` と ```Set-AzJitNetworkAccessPolicy -Name VM2``` を使用します。


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>REST API を使用して VM で JIT を有効にする

Just-In-Time VM アクセス機能は、Azure Security Center API から使用できます。 構成済みの VM に関する情報を取得したり、新しい VM を追加したり、VM へのアクセス権を要求したりするには、この API を使用します。 

詳細については、「[JIT ネットワーク アクセス ポリシー](/rest/api/securitycenter/jitnetworkaccesspolicies)」を参照してください。


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>JIT が有効な VM へのアクセス権を要求する

Azure portal (Security Center または Azure 仮想マシン) またはプログラムから、JIT が有効な VM へのアクセス権を要求できます。

これらの各オプションについては、以下の個別のタブで説明します。

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Azure Security Center から JIT が有効な VM へのアクセス権を要求する 

VM で JIT が有効になっている場合は、それに接続するためのアクセス権を要求する必要があります。 JIT を有効にした方法に関係なく、サポートされているいずれかの方法でアクセス権を要求できます。

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Security Center からの JIT アクセス権の要求":::

1. **[Just In Time VM アクセス]** ページで、 **[構成済み]** タブを選択します。

1. アクセスしたい VM をマークします。

    - **[接続の詳細]** 列のアイコンによって、ネットワーク セキュリティ グループまたはファイアウォールで JIT が有効になっているかどうかが示されています。 両方で有効になっている場合は、ファイアウォールのアイコンだけが表示されます。

    - **[接続の詳細]** 列では、VM を接続するために必要な情報と、その開かれているポートが示されます。

1. **[アクセス権の要求]** を選択します。 **[アクセス権の要求]** ウィンドウが開きます。

1. **[アクセス権の要求]** では、開放するポート、ポートの開放先のソース IP アドレス、ポートを開放しておく時間枠を VM ごとに構成します。 構成済みのポートへのアクセス権だけを要求することができます。 ポートごとに、作成した JIT 構成から派生した最大許容時間があります。

1. **[Open ports]\(ポートを開く\)** を選択します。

> [!NOTE]
> アクセスを要求しているユーザーがプロキシの背後にいる場合は、**My IP** オプションが機能しない場合があります。 組織のすべての IP アドレス範囲を定義する必要がある場合があります。



### <a name="azure-virtual-machines"></a>[**Azure 仮想マシン**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Azure 仮想マシンの接続ページから JIT が有効な VM へのアクセス権を要求する

VM で JIT が有効になっている場合は、それに接続するためのアクセス権を要求する必要があります。 JIT を有効にした方法に関係なく、サポートされているいずれかの方法でアクセス権を要求できます。

  >![jit Just-In-Time 要求](./media/security-center-just-in-time/jit-request-vm.png)


Azure 仮想マシンからアクセス権を要求するには:

1. Azure portal で仮想マシンのページを開きます。

1. 接続先の VM を選択し、 **[接続]** ページを開きます。

    Azure により、その VM で JIT が有効になっているかどうかが確認されます。

    - VM に対して JIT が有効になっていない場合は、有効にするように求められます。

    - JIT が有効になっている場合は、 **[アクセス権の要求]** を選択し、要求元の IP、時間の範囲、その VM 用に構成されているポートを指定して、アクセス要求を渡します。

> [!NOTE]
> Azure Firewall によって保護されている VM に対する要求が承認されると、Security Center からユーザーに対して、VM に接続するために使用する適切な接続情報 (DNAT テーブルのポート マッピング) が提供されます。



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>PowerShell を使用して JIT が有効な VM へのアクセス権を要求する

次の例では、特定の IP アドレスおよび特定の期間にポート 22 を開くことを要求する、特定の VM への Just-In-Time VM アクセス要求が示されています。

PowerShell で以下を実行します。

1. VM 要求アクセス プロパティを構成します。

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. 配列内に VM アクセス要求パラメーターを挿入します。

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. 要求アクセスを送信します (ステップ 1 のリソース ID を使用)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

詳細については、[PowerShell コマンドレットに関するドキュメント](/powershell/scripting/developer/cmdlet/cmdlet-overview)を参照してください。



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>REST API を使用して JIT が有効な VM へのアクセス権を要求する

Just-In-Time VM アクセス機能は、Azure Security Center API から使用できます。 構成済みの VM に関する情報を取得したり、新しい VM を追加したり、VM へのアクセス権を要求したりするには、この API を使用します。 

詳細については、「[JIT ネットワーク アクセス ポリシー](/rest/api/securitycenter/jitnetworkaccesspolicies)」を参照してください。

---








## <a name="audit-jit-access-activity-in-security-center"></a>Security Center から JIT アクセス アクティビティを監査する

ログ検索を使用して VM アクティビティについての情報が得ることができます。 ログを表示するには:

1. **[Just-In-Time VM アクセス]** で、 **[構成済み]** タブを選択します。

1. 監査対象の VM で、行の最後にある省略記号メニューを開きます。
 
1. メニューから **[アクティビティ ログ]** を選択します。

   ![Just-In-Time JIT アクティビティ ログの選択](./media/security-center-just-in-time/jit-select-activity-log.png)

   アクティビティ ログによって、その VM に対する前回の操作、時刻、日付、サブスクリプションがフィルター処理されて表示されます。

1. ログ情報をダウンロードするには、 **[CSV としてダウンロードする]** を選択します。








## <a name="next-steps"></a>次の手順

この記事では、Just-In-Time VM アクセスを設定して使用する方法について説明しました。 JIT を使用する必要がある理由については、防御対象の脅威について説明されている概念記事を参照してください。

> [!div class="nextstepaction"]
> [JIT の説明](just-in-time-explained.md)