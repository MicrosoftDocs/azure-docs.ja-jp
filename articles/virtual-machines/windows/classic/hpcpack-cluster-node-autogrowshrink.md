---
title: "HPC Pack クラスター ノードの自動スケール | Microsoft Docs"
description: "Azure で HPC Pack クラスターコンピューティング ノードの数を自動的に拡大縮小する "
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: 
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 0dc0d15c64d8951c3c457df73588c37418a3c8a4
ms.lasthandoff: 03/25/2017


---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>クラスターのワークロードに合わせて Azure の HPC Pack クラスター リソースを自動的に拡大縮小する
HPC Pack クラスターで Azure の "バースト" ノードをデプロイする場合、あるいは Azure VM で HPC Pack クラスターを作成する場合、クラスターのワークロードに合わせて、ノードやコアなどのクラスター リソースを自動的に増減できれば便利です。 この方法でクラスター リソースをスケーリングすると、Azure リソースをさらに効率的に利用し、そのコストを制御できます。

この記事では、HPC Pack が提供する、コンピューティング リソースの自動スケールの 2 つの方法を紹介します。

* HPC Pack クラスター プロパティ **AutoGrowShrink**

* HPC PowerShell スクリプト **AzureAutoGrowShrink.ps1**

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

現在自動的に拡大縮小できるのは、Windows Server オペレーティング システムを実行している HPC Pack コンピューティング ノードのみです。


## <a name="set-the-autogrowshrink-cluster-property"></a>AutoGrowShrink クラスター プロパティを設定する
### <a name="prerequisites"></a>前提条件

* **HPC Pack 2012 R2 Update 2 以降のクラスター** - クラスターのヘッド ノードは、オンプレミスでも Azure VM でもデプロイできます。 Azure の「バースト」ノードでオンプレミス ヘッド ノードを使用するには、「 [HPC Pack でハイブリッド クラスターをセットアップする](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) 」を参照してください。 Azure VM で HPC Pack クラスターをすばやくデプロイするには、[HPC Pack IaaS デプロイ スクリプト](hpcpack-cluster-powershell-script.md)に関するページを参照してください。

* **Azure にヘッド ノードがあるクラスターの場合 (Resource Manager デプロイメント モデル)** - HPC Pack 2016 以降では、Azure Resource Manager を使用してデプロイされたクラスター VM を自動的に拡大縮小するために、Azure Active Directory アプリケーションの証明書認証が使用されます。 証明書の構成手順は次のとおりです。

  1. クラスターのデプロイ後に、1 つのヘッド ノードにリモート デスクトップで接続します。

  2. 各ヘッド ノードに証明書 (秘密キー付きの PFX 形式) をアップロードして、Cert:\LocalMachine\My および Cert:\LocalMachine\Root にインストールします。

  3. Azure PowerShell を管理者として起動し、1 つのヘッド ノードで次のコマンドを実行します。

    ```powershell
        cd $env:CCP_HOME\bin

        Login-AzureRmAccount
    ```
        
    自分のアカウントが複数の Azure Active Directory テナントまたは Azure サブスクリプションにある場合は、次のコマンドを実行することで、適切なテナントとサブスクリプションを選択できます。
  
    ```powershell
        Login-AzureRMAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    現在選択されているテナントとサブスクリプションを表示するには、次のコマンドを実行します。
    
    ```powershell
        Get-AzureRMContext
    ```

  4. 次のスクリプトを実行します。

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    各値の説明:

    **DisplayName** - Azure のアクティブなアプリケーションの表示名。 アプリケーションが存在しない場合は、Azure Active Directory に作成されます。

    **HomePage** - アプリケーションのホーム ページ。 上記の例のように、ダミーの URL を構成できます。

    **IdentifierUri** - アプリケーションの ID。 上記の例のように、ダミーの URL を構成できます。

    **CertificateThumbprint** - 手順 1 でヘッド ノードにインストールした証明書の拇印。

    **TenantId** - Azure Active Directory のテナント ID。 テナント ID は、Azure Active Directory ポータルの **[プロパティ]** ページから取得できます。

    **ConfigARMAutoGrowShrinkCert.ps1** の詳細については、`Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed` を実行してください。


* **Azure にヘッド ノードがあるクラスターの場合 (クラシック デプロイメント モデル)** - HPC Pack IaaS デプロイ スクリプトを使用してクラシック デプロイメント モデルでクラスターを作成する場合は、クラスター構成ファイルで AutoGrowShrink オプションを設定して、**AutoGrowShrink** クラスター プロパティを有効にします。 詳細については、 [スクリプトのダウンロード](https://www.microsoft.com/download/details.aspx?id=44949)に付属のドキュメントを参照してください。

    または、クラスターをデプロイした後に、次のセクションで説明する HPC PowerShell コマンドを使用して、 **AutoGrowShrink** クラスター プロパティを有効にします。 準備のために、まず次の手順を実行します。

  1. ヘッド ノードおよび Azure サブスクリプションで Azure 管理証明書を構成します。 テスト デプロイでは、HPC Pack でヘッド ノードにインストールされる既定の Microsoft HPC Azure 自己署名証明書を使用し、Azure サブスクリプションにその証明書をアップロードします。 オプションと手順については、 [TechNet ライブラリのガイダンス](https://technet.microsoft.com/library/gg481759.aspx)を参照してください。

  2. ヘッド ノードで **regedit** を実行し、HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo に移動して、新しい文字列値を追加します。 値の名前は "ThumbPrint" に、値のデータは手順 1 の証明書の拇印に設定します。

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>AutoGrowShrink プロパティの設定用 HPC PowerShell コマンド
以下に、 **AutoGrowShrink** を設定し、追加パラメーターでその動作を調整するためのサンプル HPC PowerShell コマンドを示します。 設定の完全な一覧については、この記事の後半の「 [AutoGrowShrink パラメーター](#AutoGrowShrink-parameters) 」を参照してください。

これらのコマンドを実行するには、管理者としてクラスターのヘッド ノードで HPC PowerShell を起動します。

**AutoGrowShrink プロパティを有効にするには**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**AutoGrowShrink プロパティを無効にするには**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**分単位で拡大間隔を変更するには**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**分単位で縮小間隔を変更するには**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**AutoGrowShrink の現在の構成を表示するには**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**AutoGrowShrink からノード グループを除外するには**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> このパラメーターは、HPC Pack 2016 以降でサポートされます。
>

### <a name="autogrowshrink-parameters"></a>AutoGrowShrink パラメーター
以下に、 **Set-HpcClusterProperty** コマンドを使用して変更できる AutoGrowShrink パラメーターを示します。

* **EnableGrowShrink** - **AutoGrowShrink** プロパティの有効/無効を切り替えます。
* **ParamSweepTasksPerCore** - 1 つのコアを拡大するパラメーター スイープ タスクの数。 既定では、タスクごとに 1 つのコアを拡大します。

  > [!NOTE]
  > HPC Pack QFE KB3134307 では、**ParamSweepTasksPerCore** が **TasksPerResourceUnit** に変更されました。 このコマンドはジョブ リソースの種類に基づいており、ノード、ソケット、またはコアに設定することができます。
  >
  >
* **GrowThreshold** - 自動拡大をトリガーする、キューに登録されたタスク数のしきい値。 既定値は 1 です。つまり、キューに登録された状態のタスクが 1 つ以上ある場合、ノードを自動拡大します。
* **GrowInterval** - 自動拡大をトリガーする分単位の間隔。 既定の間隔は 5 分です。
* **GrowInterval** - 自動縮小をトリガーする分単位の間隔。 既定の間隔は 5 分です。
* **ShrinkIdleTimes** - ノードがアイドル状態であることを確認する継続的縮小チェックの回数。 既定値は 3 回です。 たとえば、 **ShrinkInterval** が 5 分である場合、HPC Pack は 5 分ごとにノードがアイドル状態であるかどうかを確認します。 継続的チェックを 3 回 (15 分) してもノードがアイドル状態である場合、HPC Pack はそのノードを縮小します。
* **ExtraNodesGrowRatio** - Message Passing Interface (MPI) ジョブで拡大する追加ノードの割合。 既定値は 1 です。つまり、HPC Pack は MPI ジョブでノードを 1% 拡大します。
* **GrowByMin** - 自動拡大ポリシーが、ジョブに必要な最小リソースに基づいているかどうかを示すスイッチ。 既定値は false です。つまり、HPC Pack はジョブに必要な最大リソースに基づいてジョブのノードを拡大します。
* **SoaJobGrowThreshold** - 自動拡大プロセスをトリガーする受信 SOA 要求のしきい値。 既定値は 50000 です。

  > [!NOTE]
  > このパラメーターは、HPC Pack 2012 R2 Update 3 以降でサポートされます。
  >
  >
* **SoaRequestsPerCore** - 1 つのコアを拡大する受信 SOA 要求の数。 既定値は 20000 です。

  > [!NOTE]
  > このパラメーターは、HPC Pack 2012 R2 Update 3 以降でサポートされます。
  >
* **ExcludeNodeGroups** - 指定したノード グループ内のノードは自動的に拡大縮小されません。
  
  > [!NOTE]
  > このパラメーターは、HPC Pack 2016 以降でサポートされます。
  >

### <a name="mpi-example"></a>MPI の例
既定では、HPC Pack は MPI ジョブの追加ノードを 1% 拡大します (**ExtraNodesGrowRatio** は 1 に設定されています)。 その理由は、MPI には複数のノードが必要な場合があり、すべてのノードの準備が完了した場合に限りジョブを実行できるからです。 Azure によるノードの起動時に、場合によっては 1 つのノードが他のノードより起動に時間がかかるため、そのノードの準備が完了するまで他のノードはアイドル状態で待機する可能性があります。 追加ノードを拡大することにより、HPC Pack は、このリソースの待機時間を縮小し、潜在的にコストを削減します。 MPI ジョブの追加ノードの割合を (たとえば 10% に) 増やすには、次のようなコマンドを実行します。

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>SOA の例
既定では、**SoaJobGrowThreshold** は 50000 に設定され、**SoaRequestsPerCore** は 200000 に設定されています。 要求が 70000 件ある 1 つの SOA ジョブを送信する場合、キューに登録されるタスクは 1 つであり、受信要求数は 70000 件です。 この場合、HPC Pack はキューに登録されたタスクで 1 コアを拡大し、受信要求で (70000 - 50000)/20000 = 1 コアを拡大します。そのため、この SOA ジョブでは合計 2 コアを拡大します。

## <a name="run-the-azureautogrowshrinkps1-script"></a>AzureAutoGrowShrink.ps1 スクリプトを実行する
### <a name="prerequisites"></a>前提条件

* **HPC Pack 2012 R2 更新プログラム 1 以降のクラスター** - **AzureAutoGrowShrink.ps1** スクリプトが %CCP_HOME%bin フォルダーにインストールされています。 クラスターのヘッド ノードはオンプレミスにするか、Azure VM に配置できます。 Azure の「バースト」ノードでオンプレミス ヘッド ノードを使用するには、「 [HPC Pack でハイブリッド クラスターをセットアップする](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) 」を参照してください。 Azure VM で HPC Pack クラスターをすばやくデプロイするには、[HPC Pack IaaS デプロイ スクリプト](hpcpack-cluster-powershell-script.md)に関するページをご覧ください。または、[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)を使用してください。
* **Azure PowerShell 1.4.0** - 現時点で、スクリプトはこのバージョンの Azure PowerShell を使用しています。
* **Azure バースト ノードを含むクラスターの場合** -HPC Pack がインストールされているクライアント コンピューターで、またはヘッド ノードではスクリプトを実行します。 クライアント コンピューターで実行する場合、ヘッド ノードをポイントするように $env:CCP_SCHEDULER 変数を設定します。 Azure の "バースト" ノードをクラスターに追加する必要がありますが、"未デプロイ" の状態になっている場合があります。
* **Azure VM にデプロイされているクラスター (Resource Manager デプロイメント モデル)** - Resource Manager デプロイメント モデルにデプロイされている Azure VM のクラスターの場合、スクリプトは 2 つの Azure 認証方法をサポートします (Azure アカウントにサインインして毎回スクリプト (`Login-AzureRmAccount`) を実行するか、または証明書を使用して認証するようにサービス プリンシパルを構成します)。 HPC Pack は、証明書を使用してサービス プリンシパルを作成するための **ConfigARMAutoGrowShrinkCert.ps** スクリプトを提供します。 このスクリプトでは、Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成し、サービス プリンシパルに共同作成者ロールを割り当てます。 スクリプトを実行するには、Azure PowerShell を管理者として起動し、次のコマンドを実行します。

    ```powershell
    cd $env:CCP_HOME\bin

    Login-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    **ConfigARMAutoGrowShrinkCert.ps1** の詳細については、`Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed` を実行してください。

* **Azure VM にデプロイされているクラスター (クラシック デプロイメント モデル)** - ヘッド ノード VM でスクリプトを実行します。そこでインストールされている **Start-HpcIaaSNode.ps1** スクリプトと **Stop-HpcIaaSNode.ps1** スクリプトに依存するためです。 これらのスクリプトは追加で Azure 管理証明書を必要とするか、設定ファイルを発行します (「[Azure で HPC Pack クラスターのコンピューティング ノードを管理する](hpcpack-cluster-node-manage.md)」を参照してください)。 必要なコンピューティング ノード VM がすべてクラスターに追加されていることを確認します。 これらの VM は "停止" 状態になっている場合があります。



### <a name="syntax"></a>構文
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>パラメーター
* **NodeTemplates** - ノードを拡大縮小するための範囲を定義するノード テンプレートの名前。 指定されていない場合 (既定値は @() です)、**NodeType** の値が AzureNodes のとき、**AzureNodes** ノード グループのすべてのノードが範囲内となり、**NodeType** の値が ComputeNodes のとき、**ComputeNodes** ノード グループのすべてのノードが範囲内になります。
* **JobTemplates** - ノードを拡大する範囲を定義するジョブ テンプレートの名前。
* **NodeType** - 拡大縮小するノードの型。 サポートされる値は次のとおりです。

  * **AzureNodes** – オンプレミスまたは Azure IaaS クラスターの Azure PaaS (バースト) ノード用。
  * **ComputeNodes** - Azure IaaS クラスターにのみ存在するコンピューティング ノード VM 用。

* **NumOfQueuedJobsPerNodeToGrow** - 1 つのノードを拡大するために必要で、キューに登録されたジョブの数。
* **NumOfQueuedJobsToGrowThreshold** - 拡大プロセスを始めるためにキューに登録されたジョブのしきい値数。
* **NumOfActiveQueuedTasksPerNodeToGrow** - 1 つのノードを拡大するために必要で、キューに登録されたアクティブなタスクの数。 **NumOfQueuedJobsPerNodeToGrow** に 0 より大きい値が指定されている場合、このパラメーターは無視されます。
* **NumOfActiveQueuedTasksToGrowThreshold** - 拡大プロセスの開始を決定する、キューに登録されたアクティブなタスク数のしきい値。
* **NumOfInitialNodesToGrow** - 範囲内の全ノードが "**未デプロイ**" または "**停止 (割り当て解除)**" の場合、拡大するノードの初回最小数。
* **GrowCheckIntervalMins** - 拡大チェック間の間隔 (分単位)。
* **ShrinkCheckIntervalMins** - 縮小チェック間の間隔 (分単位)。
* **ShrinkCheckIdleTimes** - ノードがアイドル状態であることを確認する継続的縮小チェックの数 (**ShrinkCheckIntervalMins** で分割)。
* **UseLastConfigurations** - 引数ファイルに保存された以前の構成。
* **ArgFile**- スクリプトを実行する構成を保存し、更新するための引数ファイルの名前。
* **LogFilePrefix** - ログ ファイルのプレフィックス名。 パスを指定できます。 既定では、ログは現在の作業ディレクトリに書き込まれます。

### <a name="example-1"></a>例 1
次の例では、Azure バースト ノードがデプロイされ、規定の AzureNode テンプレートで自動的に拡大縮小します。 最初にすべてのノードが「 **未デプロイ** 」状態の場合、少なくとも 3 つのノードが開始されます。 キューに登録されたジョブの数が 8 を超える場合、その数がキューに登録されたジョブと **NumOfQueuedJobsPerNodeToGrow**の比率を超えるまでスクリプトはノードを起動します。 ノードは 3 回連続してアイドル状態になると停止します。

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>例 2
次の例では、Azure コンピューティング ノード VM がデプロイされ、規定の ComputeNode テンプレートで自動的に拡大縮小します。
規定のジョブ テンプレートにより構成されたジョブによりクラスターのワークロード範囲が定義されます。 最初にすべてのノードが「停止」状態の場合、少なくとも 5 つのノードが開始されます。 キューに登録されたアクティブ タスクの数が 15 を超える場合、その数がキューに登録されたアクティブ タスクと **NumOfActiveQueuedTasksPerNodeToGrow**の比率を超えるまでスクリプトはノードを起動します。 ノードは 10 回連続してアイドル状態になると停止します。

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

