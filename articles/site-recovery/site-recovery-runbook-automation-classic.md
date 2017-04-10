---
title: "クラシック ポータルで復旧計画に Azure Automation Runbook を追加する | Microsoft Docs"
description: "この記事では、Azure Site Recovery において、Azure Automation を使用して復旧計画を拡張し、Azure への復旧中に複雑なタスクを実行可能にする方法について説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: f24eaa62-9dea-4fce-92e1-a72513ca0289
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/06/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 44b6ff6e588d529fd833a4a7fdd61df7e933ddd8
ms.openlocfilehash: b4105e98323b5161a22fa65707d376a7155611d6
ms.lasthandoff: 01/30/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans-in-the-classic-portal"></a>クラシック ポータルで復旧計画に Azure Automation Runbook を追加する
このチュートリアルでは、復旧計画に拡張性を持たせるために Azure Site Recovery と Azure Automation を統合する方法について説明します。 復旧計画では、セカンダリ クラウドへのレプリケーションと Azure へのレプリケーションという両方のシナリオで、Azure Site Recovery を使用して保護された仮想マシンの復旧を調整できます。 復旧計画により、復旧を**常に正確**、**反復可能**で、**自動化**されるようにすることもできます。 仮想マシンを Azure にフェールオーバーする場合は、Azure Automation と統合して復旧計画を拡張し、Runbook を実行する機能を使用できます。そのため、強力な自動タスクを実行できます。

Azure Automation について聞いたことがない場合は、[こちら](https://azure.microsoft.com/services/automation/)でサインアップして、[こちら](https://azure.microsoft.com/documentation/scripts/)からサンプル スクリプトをダウンロードしてください。 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) の詳細と、復旧計画を使用した Azure への復旧の調整方法については、[こちら](https://azure.microsoft.com/blog/?p=166264)を参照してください。

この短いチュートリアルでは、Azure Automation Runbook を復旧計画に統合する方法を説明します。 以前は手動での介入を必要とした単純なタスクを自動化し、複数の手順の復旧を&1; 回のクリックの復旧アクションに変換する方法を説明します。 また、単純なスクリプトで問題が発生する場合に、トラブルシューティングする方法も説明します。

## <a name="protect-the-application-to-azure"></a>Azure でのアプリケーションの保護
はじめに、2 台の仮想マシンで構成される単純なアプリケーションについて説明します。 ここでは、Fabrikam の HRweb アプリケーションがあります。 Fabrikam-HRweb-frontend と Fabrikam-Hrweb-backend は、Azure Site Recovery を使用して Azure で保護されている&2; 台の仮想マシンです。 Azure Site Recovery を使用して仮想マシンを保護するには、次の手順に従います。

1. 仮想マシンの保護を有効にします。
2. 仮想マシンが初期レプリケーションを完了し、レプリケートしていることを確認します。
3. 初期レプリケーションが完了して、レプリケーションの状態が [保護済み] になるまで待機します。

## ![](media/site-recovery-runbook-automation/01.png)
このチュートリアルでは、Fabrikam HRweb アプリケーションの復旧計画を作成し、Azure にアプリケーションがフェールオーバーするようにします。 次に、この復旧計画を、フェールオーバーした Azure 仮想マシンにエンドポイントを作成する Runbook と統合し、ポート 80 で Web ページを提供します。

まず、アプリケーションの復旧計画を作成します。

## <a name="create-the-recovery-plan"></a>復旧計画の作成
Azure にアプリケーションを復旧するには、復旧計画を作成する必要があります。
復旧計画を使用して、仮想マシンの復旧の順序を指定できます。 グループ 1 に配置された仮想マシンが復旧され、最初に起動します。その後グループ 2 の仮想マシンが続きます。

以下のような復旧計画を作成します。

![](media/site-recovery-runbook-automation/12.png)

復旧計画の詳細については、 [ここ](https://msdn.microsoft.com/library/azure/dn788799.aspx "ここ")からサンプル スクリプトをダウンロードしてください。

次に、Azure Automation で必要なアーティファクトを作成します。

## <a name="create-the-automation-account-and-its-assets"></a>オートメーション アカウントとアセットの作成
Runbook を作成するには、Azure Automation アカウントが必要です。 まだアカウントがない場合は、 ![](media/site-recovery-runbook-automation/02.png)で示された [Azure Automation] タブに移動して、新しいアカウントを作成します。

1. アカウントに識別する名前を付けます。
2. アカウントを配置する地理的なリージョンを指定します。

ASR コンテナーと同じリージョンに、アカウントを配置することをお勧めします。

![](media/site-recovery-runbook-automation/03.png)

次に、以下のアセットをアカウントに作成します。

### <a name="add-a-subscription-name-as-asset"></a>アセットとしてのサブスクリプション名の追加
1. Azure Automation アセットに新しい設定![](media/site-recovery-runbook-automation/04.png)を追加し、![](media/site-recovery-runbook-automation/05.png)を選択します。
2. 変数の型として **[String]**
3. 変数名に **AzureSubscriptionName**

   ![](media/site-recovery-runbook-automation/06.png)
4. 変数値として、実際の Azure サブスクリプション名を指定します。

   ![](media/site-recovery-runbook-automation/07_1.png)

Azure ポータルでアカウントの [設定] ページからサブスクリプションの名前を識別できます。

### <a name="add-an-azure-login-credential-as-asset"></a>アセットとしての Azure ログイン資格情報の追加
Azure Automation は Azure PowerShell を使用して、サブスクリプションに接続して、サブスクリプションのアーティファクト上で稼働します。 そのためには、Microsoft アカウントか、職場または学校のアカウントを使用して認証する必要があります。
アカウントの資格情報はアセットに格納して、Runbook で安全に使用できます。

1. Azure Automation アセットに新しい設定![](media/site-recovery-runbook-automation/04.png)を追加し、![](media/site-recovery-runbook-automation/09.png)を選択します。
2. [資格情報の種類] には、 **[Windows PowerShell 資格情報]**
3. 名前に **AzureCredential**

   ![](media/site-recovery-runbook-automation/10.png)
4. サインインで使用するユーザー名とパスワードを指定します。

以上で、これらの両方の設定が、アセットで使用できます。

![](media/site-recovery-runbook-automation/11.png)

PowerShell を使用してサブスクリプションに接続する方法の詳細については、 [ここ](/powershell/azureps-cmdlets-docs)を参照してください。

次に Azure Automation で、フェールオーバー後にフロントエンドの仮想マシンのエンドポイントを追加できる Runbook を作成します。

## <a name="azure-automation-context"></a>Azure Automation コンテキスト
ASR は、コンテキスト変数を Runbook に渡して、確定的なスクリプトを作成できるようにします。 クラウド サービスと仮想マシンの名前は予測可能ともいえますが、常にそうであるとは限りません。これは、シナリオによっては、Azure でサポートされていない文字が原因で、仮想マシン名の名前が変更されている可能性がある場合などが考えられるためです。 したがって、この情報が "*コンテキスト*" の一部として、ASR 復旧計画に渡されます。

以下に、コンテキスト変数がどのようになっているかについて、例を示します。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


以下の表には、コンテキスト内の各変数の名前と説明が示されています。

| **変数名** | **説明** |
| --- | --- |
| RecoveryPlanName |実行される計画の名前です。 同じスクリプトを使用して名前を基にアクションを実行するのに役に立ちます。 |
| FailoverType |テスト用のフェールオーバーか、計画されたフェールオーバーか、それとも計画外のフェールオーバーかを指定します。 |
| FailoverDirection |プライマリへの復旧か、それともセカンダリへの復旧かを指定します。 |
| GroupID |復旧計画が実行される場合に、その計画内のグループ番号を識別します。 |
| VmMap |グループ内のすべての仮想マシンの配列です。 |
| VMMap キー |VM ごとの一意のキー (GUID) です。 これは、該当する仮想マシンの VMM ID と同じです。 |
| RoleName |復元される Azure VM の名前です。 |
| CloudServiceName |仮想マシンが作成される Azure Cloud Service の名前です。 |

コンテキストで VmMap キーを識別するには、ASR の [VM のプロパティ] ページに移動し、VM GUID プロパティを参照することもできます。

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Automation Runbook の作成
次に、フロントエンドの仮想マシンでポート 80 を開く、Runbook を作成します。

1. Azure Automation アカウントに、 **OpenPort80**

   ![](media/site-recovery-runbook-automation/14.png)
2. Runbook の [作成者] ビューに移動し、ドラフト モードに移行します。
3. まず、復旧計画のコンテキストとして使用する変数を指定します。

   ```
       param (
           [Object]$RecoveryPlanContext
       )

   ```
4. 次に、資格情報とサブスクリプション名を使用して、サブスクリプションに接続します。

   ```
       $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

       # Connect to Azure
       $AzureAccount = Add-AzureAccount -Credential $Cred
       $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
       Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
   ```

   ここで、**AzureCredential** と **AzureSubscriptionName** という Azure 資産を使用することに注意してください。
5. ここで、エンドポイントの詳細と、エンドポイントを公開する仮想マシンの GUID を指定します。 この場合は、フロントエンドの仮想マシンです。

   ```
       # Specify the parameters to be used by the script
       $AEProtocol = "TCP"
       $AELocalPort = 80
       $AEPublicPort = 80
       $AEName = "Port 80 for HTTP"
       $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
   ```

   これにより、Azure エンドポイント プロトコル、VM のローカル ポート、およびそのマップされたパブリック ポートが指定されます。 これらの変数は、VM にエンドポイントを追加する Azure のコマンドに必要なパラメーターです。 VMGUID には、稼働に必要な仮想マシンの GUID が保持されます。
6. ここで、このスクリプトは、特定の VM GUID のコンテキストを抽出し、VM GUID で参照される仮想マシンにエンドポイントを作成します。

   ```
       #Read the VM GUID from the context
       $VM = $RecoveryPlanContext.VmMap.$VMGUID

       if ($VM -ne $null)
       {
           # Invoke pipeline commands within an InlineScript

           $EndpointStatus = InlineScript {
               # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
               # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

               $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                   Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                   Update-AzureVM
               Write-Output $Status
           }
       }
   ```
7. この処理が完了したら、[発行] ![](media/site-recovery-runbook-automation/20.png) をクリックし、スクリプトが実行で利用できるようにします。

以下に、完全なスクリプトを参考として示します。

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
スクリプトの準備ができたら、先ほど作成した復旧計画に追加できます。

1. 作成した復旧計画で、グループ 2 の後ろにスクリプトを追加します。 ![](media/site-recovery-runbook-automation/15.png)
2. スクリプト名を指定します。 これは、復旧計画内で表示するだけの目的で使用する、このスクリプトのフレンドリ名です。
3. [Azure へのフェールオーバー スクリプト] で、Azure Automation アカウント名を選択します。
4. Azure Runbook で、作成した Runbook を選択します。

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>プライマリ側スクリプト
Azure へのフェールオーバーを実行するときに、プライマリ側スクリプトを実行することもできます。 これらのスクリプトは、フェールオーバー中に、VMM サーバーで実行されます。
プライマリ側スクリプトはプレシャットダウンおよびシャットダウン後のステージでのみ使用可能です。 これは、プライマリ サイトが、障害の発生時には通常使用できなくなると思われるためです。
計画外のフェールオーバー中に、プライマリ サイトの操作で選択した場合にのみ、プライマリ側スクリプトを実行を試みます。 到達できないか、またはタイムアウトが発生した場合、フェールオーバーは仮想マシンの復旧を続行します。
プライマリ側スクリプトは、Azure へのフェールオーバー中に、Azure の VMM 保護がない VMware/物理/Hyper-V の各サイトで使用できなくなります。
ただし、Azure からオンプレミスにフェールバックすると、VMware を除くすべてのターゲットでプライマリ側スクリプト (Runbook) を使用することができます。

## <a name="test-the-recovery-plan"></a>復旧計画のテスト
Runbook を計画に追加したら、テスト フェールオーバーを開始して、動作することを確認できます。 いつでも、エラーがないことを確認するために、テスト フェールオーバーを実行してアプリケーションと復旧計画をテストすることが推奨されます。

1. 復旧計画を選択し、テスト フェールオーバーを開始します。
2. 計画の実行中には、Runbook が実行されたかどうかを、その状態から確認できます。

   ![](media/site-recovery-runbook-automation/17.png)
3. 詳細な Runbook の実行状態は、Runbook の [Azure Automation ジョブ] ページでも確認できます。

   ![](media/site-recovery-runbook-automation/18.png)
4. フェールオーバーが完了したら、Runbook の実行結果とは別に、[Azure 仮想マシン] ページにアクセスし、エンドポイントを参照すると、実行が成功したかどうかを確認できます。

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>サンプルのスクリプト
このチュートリアルでは、一般的に利用される、Azure 仮想マシンにエンドポイントを追加するためにタスクの自動化を、手順を追って説明しました。また、Azure Automation を使用して、その他の強力な自動化タスクを行うことができます。 Microsoft および Azure Automation コミュニティが提供するサンプル Runbook を使うと、独自のソリューションを作成できます。また、ユーティリティ Runbook は大きめのオートメーション タスクの構成ブロックとして使用できます。 これらをギャラリーから入手して使用を開始して、Azure Site Recovery を使用して&1; 回のクリックによる強力な復旧プランをアプリケーションに対して作成してください。

## <a name="additional-resources"></a>その他のリソース
[Azure Automation Overview (Azure Automation の概要) (Azure Automation の概要) (Azure Automation の概要)](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure Automation Overview (Azure Automation の概要) (Azure Automation の概要)")

[Sample Azure Automation Scripts (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト)](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Sample Azure Automation Scripts (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト)")

