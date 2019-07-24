---
title: メトリックのためのコンテナーの Azure Monitor の更新方法 | Microsoft Docs
description: この記事では、集計したメトリックの探索とアラートをサポートするカスタム メトリック機能を有効にするために、コンテナーの Azure Monitor を更新する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 2f500ea127d3f2042e7c97eeace592b4da8d8d0e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995984"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>メトリックを有効にするためのコンテナーの Azure Monitor の更新方法
コンテナーの Azure Monitor では、Azure Kubernetes Services (AKS) クラスター ノードおよびポッドからのメトリックの収集と、Azure Monitor メトリック ストアへのそれらの書き込みのサポートを導入します。 この変更は、パフォーマンス グラフでの集計計算 (Avg、Count、Max、Min、Sum) を表示するときの適時性の改善、Azure portal ダッシュボードでのパフォーマンス グラフのピン留めのサポート、およびメトリック アラートのサポートを実現するためのものです。

この機能の一部として、次のメトリックが有効にされています。

| メトリック名前空間 | メトリック | 説明 |
|------------------|--------|-------------|
| insights.container/nodes | cpuUsageMillicores、cpuUsagePercentage、memoryRssBytes、memoryRssPercentage、memoryWorkingSetBytes、memoryWorkingSetPercentage、nodesCount | これらは、*ノード* メトリックであり、*ホスト*をディメンションとして含みます。また、<br> *ホスト* ディメンションの値として、ノードの名前も含みます。 |
| insights.container/pods | podCount | これらは、*ポッド* メトリックであり、ControllerName、Kubernetes 名前空間、名前、フェーズをディメンションとして含みます。 |

これらの新しい機能をサポートするための更新は、Azure portal、Azure PowerShell、または Azure CLI で実行できます。 Azure PowerShell と CLI を使用すると、サブスクリプション内のクラスターごとに、またはすべてのクラスターでこれを実行できます。 AKS の新しいデプロイでは、この構成の変更と機能が自動的に含まれます。

どちらのプロセスでも、エージェントが収集したデータをクラスター リソースにパブリッシュできるように、クラスターのサービス プリンシパルに対して**メトリックの発行元の監視**ロールが割り当てられます。 メトリックの発行元の監視は、メトリックをリソースにプッシュする権限のみを持ち、状態の変更、リソースの更新、およびデータの読み取りはできません。 このロールの詳細については、[メトリックの発行元の監視ロール](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件 
開始する前に、AKS クラスター リソースの**[所有者](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-role.md#owner)** ロールのメンバーであることを確認してください。このロールでは、ノードとポッドのカスタム パフォーマンス メトリックを収集できます。 

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.59 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Azure portal からクラスターをアップグレードする

コンテナーの Azure Monitor によって監視される既存の AKS クラスターの場合、クラスターを選択して Azure Monitor のマルチクラスター ビューから、または左側のウィンドウで **[Insights]** を選択することでクラスターから直接その正常性を表示すると、ポータルの上部にバナーが表示されます。

![Azure portal の AKS クラスター バナーのアップグレード](./media/container-insights-update-metrics/portal-banner-enable-01.png)

**[有効化]** をクリックすると、クラスターをアップグレードするためのプロセスが開始されます。 このプロセスには終了するまでに数秒かかる場合があり、メニューの [通知] の下で進行状況を追跡できます。

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Azure コマンド シェルで Bash を使用してすべてのクラスターをアップグレードする
Azure コマンド シェルで Bash を使用してサブスクリプション内のすべてのクラスターを更新するには、次の手順を実行します。

1. Azure CLI を使用して、次のコマンドを実行します。  AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId** の値を編集します。

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://git.io/aks-mdm-onboarding-atscale | bash -s subscriptionId   
    ```

    構成の変更が完了するまでに、数秒かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Azure CLI を使用してクラスターごとにアップグレードする
Azure CLI を使用してサブスクリプション内の特定のクラスターを更新するには、次の手順を実行します。

1. Azure CLI を使用して、次のコマンドを実行します。 AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId****resourceGroupName**、および **clusterName** の値を編集します。  **clientIdOfSPN** の値を取得する場合、次の例に示すように、コマンド `az aks show` を実行すると値が返されます。

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ``` 

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Azure PowerShell を使用してすべてのクラスターをアップグレードする
Azure PowerShell を使用してサブスクリプション内のすべてのクラスターを更新するには、次の手順を実行します。

1. 次のスクリプトをコピーして、ファイルに貼り付けます。

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the all AKS clusters in specified subscription        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    #>

    param(
    [Parameter(mandatory = $true)]
    [string]$SubscriptionId 
    )


    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ( $null -eq $azAksModule ) -or ($null -eq $azResourcesModule)) {

    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command if not installed already`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule)   {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }   
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule)   {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }    
            }     
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    try {
    Write-Host("")
    Write-Host("Trying to get the current Az login context...")
    $account = Get-AzContext -ErrorAction Stop
    Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
    Write-Host("")
    }
    catch {
    Write-Host("")
    Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
    Write-Host("")
    }

    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    } 

    #
    #   get all the AKS clusters in specified subscription
    #
    Write-Host("getting all aks clusters in specified subscription ...")
    $allClusters = Get-AzAks -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
    Write-Host("")
    Write-Host("Failed to get Aks clusters in specified subscription. Please make sure that you have access to the existing clusters") -ForegroundColor Red
    Write-Host("")
    Stop-Transcript
    exit
    }
    Write-Host("Successfully got all aks clusters ...") -ForegroundColor Green

    $clustersCount = $allClusters.Id.Length

    Write-Host("Adding role assignment for the clusters ...")

    for ($index = 0 ; $index -lt $clustersCount ; $index++) {  

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    $servicePrincipalClientId = $allClusters.ServicePrincipalProfile[$index].ClientId
    $clusterResourceId = $allClusters.Id[$index]
    $clusterName = $allClusters.Name[$index]


    Write-Host("Adding role assignment for the cluster: $clusterResourceId, servicePrincipalClientId: $servicePrincipalClientId ...")

  
    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

        $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     
        if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {
            Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
        }
        else { 
        
            Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
        }

    }
    else {

        Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }   

    Write-Host("Completed adding role assignment for the cluster: $clusterName ...")
        
    }

    Write-Host("Completed adding role assignment for the aks clusters in subscriptionId :$SubscriptionId")   
    ```

2. このファイルを **onboard_metrics_atscale.ps1** という名前でローカル フォルダーに保存します。
3. Azure PowerShell を使用して、次のコマンドを実行します。  AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId** の値を編集します。

    ```powershell
    .\onboard_metrics_atscale.ps1 subscriptionId
    ```
    構成の変更が完了するまでに、数秒かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Azure PowerShell を使用してクラスターごとにアップグレードする
Azure PowerShell を使用して特定のクラスターを更新するには、次の手順を実行します。

1. 次のスクリプトをコピーして、ファイルに貼り付けます。

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the specified AKS cluster        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    .PARAMETER resourceGroupName
        Resource Group name that the AKS cluster is in
            
     .PARAMETER clusterName
        Name of the AKS cluster.
    #>

    param(
       [Parameter(mandatory = $true)]
       [string]$SubscriptionId,
       [Parameter(mandatory = $true)]
       [string]$resourceGroupName,
       [Parameter(mandatory = $true)]
       [string] $clusterName
    )

    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ($null -eq $azAksModule) -or ($null -eq $azResourcesModule)) {


    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule) {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule) {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }   
            }      
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
     }
    }

    try {
       Write-Host("")
       Write-Host("Trying to get the current Az login context...")
       $account = Get-AzContext -ErrorAction Stop
       Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
       Write-Host("")
    }
    catch {
      Write-Host("")
      Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
      Write-Host("")
    }


    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    #
    #   Check AKS cluster existance and access check
    #
    Write-Host("Checking aks cluster exists...")
    $cluster = Get-AzAks -ResourceGroupName $resourceGroupName -Name $clusterName  -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
       Write-Host("")
       Write-Host("Could not find Aks cluster. Please make sure that specified cluster exists: '" + $clusterName + "'is correct and you have access to the cluster") -ForegroundColor Red
       Write-Host("")
       Stop-Transcript
       exit
    }
    Write-Host("Successfully checked specified cluster exists details...") -ForegroundColor Green

    $servicePrincipalClientId = $cluster.ServicePrincipalProfile.clientId
    $clusterResourceId = $cluster.Id

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

    $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     

    if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {           
        Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
    }
    else { 
        
        Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
    }

    }
    else {

    Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }
    ```

2. このファイルを **onboard_metrics.ps1** という名前でローカル フォルダーに保存します。
3. Azure PowerShell を使用して、次のコマンドを実行します。 AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId****resourceGroupName**、および **clusterName** の値を編集します。

    ```powershell
    .\onboard_metrics.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    構成の変更が完了するまでに、数秒かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>更新を確認する 
前に説明した方法のいずれかを使用して更新を開始したら、Azure Monitor メトリックス エクスプローラーを使用できます。また、**[メトリック名前空間]** から **[Insights]** が表示されていることを確認できます。 表示されている場合は、[メトリック アラート](../platform/alerts-metric.md)の設定または[ダッシュボード](../../azure-portal/azure-portal-dashboards.md)へのグラフのピン留めを開始できます。  
