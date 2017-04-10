---
title: "Site Recovery での復旧計画に Azure Automation Runbook を追加する | Microsoft Docs"
description: "この記事では、Azure Site Recovery において、Azure Automation を使用して復旧計画を拡張し、Azure への復旧中に複雑なタスクを実行可能にする方法について説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: ac56273cf85aff550febecf0d75ec87d5c6dbbca
ms.openlocfilehash: 26547135548dde96e9da601f2e0ccfe96c626880
ms.lasthandoff: 02/23/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>復旧計画への Azure Automation Runbook の追加
このチュートリアルでは、復旧計画に拡張性を持たせるために Azure Site Recovery と Azure Automation を統合する方法について説明します。 復旧計画では、セカンダリ クラウドへのレプリケーションと Azure へのレプリケーションという両方のシナリオで、Azure Site Recovery を使用して保護された仮想マシンの復旧を調整できます。 復旧計画により、復旧を**常に正確**、**反復可能**で、**自動化**されるようにすることもできます。 仮想マシンを Azure にフェールオーバーする場合は、Azure Automation と統合して復旧計画を拡張し、Runbook を実行する機能を使用できます。そのため、強力な自動タスクを実行できます。

Azure Automation について聞いたことがない場合は、[こちら](https://azure.microsoft.com/services/automation/)でサインアップして、[こちら](https://azure.microsoft.com/documentation/scripts/)からサンプル スクリプトをダウンロードしてください。 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) の詳細と、復旧計画を使用した Azure への復旧の調整方法については、[こちら](https://azure.microsoft.com/blog/?p=166264)を参照してください。

このチュートリアルでは、Azure Automation Runbook を復旧計画に統合する方法を説明します。 以前は手動での介入を必要とした単純なタスクを自動化し、複数の手順の復旧を&1; 回のクリックの復旧アクションに変換する方法を説明します。

## <a name="customize-the-recovery-plan"></a>復旧計画のカスタマイズ
1. はじめに、復旧計画のリソース ブレードを開きます。 復旧計画に、復旧用に&2; つの仮想マシンが追加されています。

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. カスタマイズ ボタンをクリックして、Runbook の追加を開始します。 

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 開始グループ 1 を右クリックし、[事後アクションの追加] を選択して追加します。
2. 新しいブレードでスクリプトをクリックして選択します。
3. スクリプトに "Hello World" という名前を付けます。
4. Automation アカウント名を選択します。 
    >[!NOTE]
    > Automation アカウントは、Azure のいずれの geo のものでもかまいませんが、Site Recovery コンテナーと同じサブスクリプションのものである必要があります。
    
5. Automation アカウントから Runbook を選択します。 この Runbook は、最初のグループの復旧後、復旧計画の実行中に実行されるスクリプトです。

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. [OK] を選択してスクリプトを保存します。 スクリプトが [グループ 1: 開始] の事後アクション グループに追加されます。

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>スクリプト追加の要点
1. スクリプトを右クリックし、"手順を削除" するか、"スクリプトを更新" します。
2. スクリプトはオンプレミスから Azure へのフェールオーバー時に Azure で実行でき、また、シャットダウン前にプライマリ側スクリプトとして、Azure からオンプレミスへのフェールオーバー時に Azure で実行できます。
3. スクリプトの実行時に、復旧計画のコンテキストが挿入されます。

以下に、コンテキスト変数がどのようになっているかについて例を示します。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
                
                "ResourceGroupName":"ContosoRG",
                
                "CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test",
                
                "RecoveryPointId":"TimeStamp"}

                }

        }


以下の表には、コンテキスト内の各変数の名前と説明が示されています。

| **変数名** | **説明** |
| --- | --- |
| RecoveryPlanName |実行される計画の名前です。 この変数を使うことで、同じスクリプトで名前に応じて異なるアクションを実行することができます。 |
| FailoverType |テスト用のフェールオーバーか、計画されたフェールオーバーか、それとも計画外のフェールオーバーかを指定します。 |
| FailoverDirection |プライマリへの復旧か、それともセカンダリへの復旧かを指定します。 |
| GroupID |復旧計画が実行される場合に、その計画内のグループ番号を識別します。 |
| VmMap |グループ内のすべての仮想マシンの配列です。 |
| VMMap キー |VM ごとの一意のキー (GUID) です。 これは、該当する仮想マシンの VMM ID と同じです。 |
| SubscriptionId |VM の作成先となる Azure サブスクリプション ID です。 |
| RoleName |復元される Azure VM の名前です。 |
| CloudServiceName |仮想マシンが作成される Azure Cloud Service の名前です。 |
| ResourceGroupName|仮想マシンが作成される Azure リソース グループの名前です。 |
| RecoveryPointId|VM をどの時点 (タイムスタンプ) まで復元するかを示します。 |

Automation アカウントに、以下のモジュールが追加されていることを確認する必要があります。 すべてのモジュールは互換性のあるバージョンでなければなりません。 すべて最新バージョンのモジュールを使用するのが簡単です。
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>VmMap のすべての VM にループでアクセスする
VmMap のすべての VM にループでアクセスするには、次のスニペットを使用します。

```
    $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
    $vmMap = $RecoveryPlanContext.VmMap
     foreach($VMID in $VMinfo)
     {
         $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
             #this check is to ensure that we skip when some data is not available else it will fail
         Write-output "Resource group name ", $VM.ResourceGroupName
         Write-output "Rolename " = $VM.RoleName
         }
     }

```

> [!NOTE]
> このスクリプトが、ブート グループの事前アクションであるときは、ResourceGroupName と RoleName の値が空になります。 値が反映されるのは、そのグループの VM がフェールオーバーに成功し、かつスクリプトがブート グループの事後アクションである場合だけです。

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>複数の復旧計画で同じ Automation Runbook を使用する

外部変数を使用すると、1 つのスクリプトを複数の復旧計画で使用することができます。 復旧計画の実行に関して渡すことができるパラメーターは、[Azure Automation の変数](../automation/automation-variables.md)を使用して格納できます。 変数の先頭に復旧計画の名前を追加することによって、復旧計画ごとに異なる変数を作成し、それらをパラメーターとして使用することができます。 スクリプトに変更を加えることなくパラメーターを変更し、スクリプトの働きを変えることができます。

### <a name="using-simple-string-variables-in-runbook-script"></a>Runbook スクリプトにおける単純な文字列変数の使用

NSG を入力として受け取り、復旧計画の VM に適用するスクリプトを考えてみましょう。

どの復旧計画が実行されているかは、復旧計画のコンテキストを使ってスクリプトに伝えることができます。

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

既にある NSG を適用するには、NSG の名前と NSG のリソース グループが必要です。 それらの変数を復旧計画スクリプトの入力として与えることになります。 具体的には、Automation アカウント アセットに&2; つの変数を作成し、そのプレフィックスとして、パラメーターの作成対象となる復旧計画の名前を追加します。

1. NSG の名前を格納するための変数を作成します。 そのプレフィックスとして復旧計画の名前を追加します。
    ![NSG 名の変数を作成](media/site-recovery-runbook-automation-new/var1.png)

2. NSG の RG 名を格納するための変数を作成します。 そのプレフィックスとして復旧計画の名前を追加します。
    ![NSG RG 名を作成](media/site-recovery-runbook-automation-new/var2.png)


スクリプトでは、次のリファレンス コードを使用して変数の値を取得します。

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue 
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

次に、これらの変数を Runbook の中で使用して、それに該当する NSG を、フェールオーバーされた仮想マシンのネットワーク インターフェイスに適用することができます。

```
     InlineScript { 
         if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
```

スクリプトを再利用して、復旧計画の名前を変数のプレフィックスとして追加できるよう、それぞれの復旧計画について個別に変数を作成します。 この例のスクリプト全体は、[こちら](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)に掲載しています。


### <a name="using-complex-variable-to-store-more-information"></a>複合変数を使ってより多くの情報を格納する

たった&1; つのスクリプトで、特定の VM のパブリック IP を有効にするにはどうすればよいのでしょうか。また、仮想マシン全体ではなく一部の仮想マシンについてのみ、それぞれ異なる NSG を適用するにはどうすればよいのでしょうか。 そのスクリプトは、他の復旧計画にも再利用できなければなりません。 仮想マシンの台数は復旧計画ごとに異なる場合があります (SharePoint の復旧の場合はフロント エンドが&2; 台、単純な LOB アプリケーションの場合はフロント エンドが&1; 台だけでよいなど)。 復旧計画ごとに個別に変数を作成する方法では、このようなことはできません。 ここでは新たな手法として、Azure Automation アカウントのアセットに複数の値を指定して、[複合変数](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)を作成します。 以降の手順を実行するためには、Azure PowerShell が必要です。

1. Azure PowerShell で自分のサブスクリプションにログインします。

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. パラメーターを格納するために、復旧計画と同じ名前の複合変数を作成します。

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    この複合変数の **VMDetails* は、保護対象となる仮想マシンの VM ID です。 この ID は、ポータルから仮想マシンのプロパティで確認できます。 ここでは、2 台の仮想マシンの詳細情報を格納する変数を作成しました。

    ![GUID として使用する VM の ID](media/site-recovery-runbook-automation-new/vmguid.png)

3. この変数を Runbook で使用し、指定された VMGUID が復旧計画のコンテキストに見つかった場合、仮想マシンに NSG を適用します。

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName 
    ```

4. Runbook で復旧計画のコンテキストの VM を反復処理しながら、その VM が **$VMDetailsObj** にも存在するかどうかをチェックします。 存在する場合は、変数のプロパティにアクセスして NSG を適用します。
    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap
           
        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID
            
            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

各復旧計画に対応する値をそれぞれ異なる変数に格納することで、同じスクリプトを別の復旧計画で使用し、異なるパラメーターを指定することができます。

## <a name="sample-scripts"></a>サンプルのスクリプト
Automation アカウントに直接インポートできるスクリプトのリポジトリについては、[Kristian Nese のスクリプトの OMS リポジトリ](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)に関する記事を参照してください。

このスクリプトは Azure Resource Manager テンプレートで、以下のすべてのスクリプトをデプロイします。

* NSG

NSG Runbook は、Public IP Addresses を復旧計画内のすべての VM に割り当てて、その仮想ネットワーク アダプターを、既定の通信を許可するネットワーク セキュリティ グループにアタッチします。

* PublicIP

Public IP Runbook は Public IP Addresses を復旧計画内のすべての VM に割り当てます。 マシンおよびアプリケーションへのアクセスは、各ゲスト内のファイアウォール設定に基づきます。

* CustomScript

CustomScript Runbook は、Public IP Addresses を復旧計画内のすべての VM に割り当て、テンプレートのデプロイ中に参照するスクリプトをプルするカスタム スクリプト拡張機能をインストールします。

* NSGwithCustomScript

NSGwithCustomScript Runbook は Public IP Addresses を復旧計画内のすべての VM に割り当て、拡張機能を使用してカスタム スクリプトをインストールし、仮想ネットワーク アダプターを NSG に接続して、リモート アクセス用の受信と送信の通信を可能にします。

## <a name="additional-resources"></a>その他のリソース
[Azure Automation サービスの実行アカウント](../automation/automation-sec-configure-azure-runas-account.md)

[Azure Automation Overview (Azure Automation の概要) (Azure Automation の概要) (Azure Automation の概要)](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure Automation Overview (Azure Automation の概要) (Azure Automation の概要)")

[Sample Azure Automation Scripts (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト)](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Sample Azure Automation Scripts (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト)")

