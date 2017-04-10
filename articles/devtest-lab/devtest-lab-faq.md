---
title: "Azure DevTest Labs に関する FAQ | Microsoft Docs"
description: "Azure DevTest Labs に関する一般的な質問に対する回答を見つける"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5c427ddbe408fc42403eb6738d1983c220e899a7
ms.lasthandoff: 03/29/2017


---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs に関する FAQ
この記事では、Azure DevTest Labs について特に多く寄せられる質問に回答しています。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>全般
* [ここに質問の答えがない場合はどうすればいいですか。](#what-if-my-question-isnt-answered-here)
* [なぜ Azure DevTest Labs を使用する必要があるのですか。](#why-should-i-use-azure-devtest-labs)
* [「worry free, self-service (心配無用のセルフ サービス)」とはどのような意味ですか。](#what-does-worry-free-self-service-mean)
* [Azure DevTest Labs を使用するにはどうすればよいですか。](#how-can-i-use-azure-devtest-labs)
* [Azure DevTest Labs の課金方法を教えてください。](#how-am-i-billed-for-azure-devtest-labs)

## <a name="security"></a>セキュリティ
* [Azure DevTest Labs の各種セキュリティ レベルはどのようなものですか。](#what-are-the-different-security-levels-in-azure-devtest-labs)
* [特定の 1 つのタスクの実行をユーザーに許可するようにロールを作成するにはどうすればよいですか。](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task)

## <a name="cicd-integration--automation"></a>CI/CD 統合と自動化
* [Azure DevTest Labs は、CI/CD ツールチェーンと統合されますか。](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain)

## <a name="virtual-machines"></a>Virtual Machines
* [Azure DevTest Labs で表示される特定の VM が Azure 仮想マシン ブレードに表示されないのはなぜですか。](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs)
* [カスタム イメージとフォーミュラの違いは何ですか。](#what-is-the-difference-between-custom-images-and-formulas)
* [同じテンプレートから複数の VM を一度に作成するにはどうすればよいですか。](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
* [既存の Azure VM を Azure DevTest Labs ラボに移動するにはどうすればよいですか。](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab)
* [複数のディスクを VM に接続できますか。](#can-i-attach-multiple-disks-to-my-vms)
* [テストに Windows OS イメージを使用する場合、MSDN サブスクリプションを購入する必要はありますか。](#if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription)
* [カスタム イメージを作成するために VHD ファイルをアップロードするプロセスを自動化するにはどうすればよいですか。](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
* [ラボ内の VM をすべて削除するプロセスを自動化するにはどうすればよいですか。](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)

## <a name="artifacts"></a>アーティファクト
* [アーティファクトとは何ですか。](#what-are-artifacts)

## <a name="lab-configuration"></a>ラボの構成
* [Azure Resource Manager テンプレートからラボを作成するにはどうすればよいですか。](#how-do-i-create-a-lab-from-an-azure-resource-manager-template)
* [別々のリソース グループに作成した VM が任意の名前を持つのはなぜですか。これらのリソース グループの名前や内容を変更できますか。](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
* [同じサブスクリプションにラボをいくつ作成できますか。](#how-many-labs-can-i-create-under-the-same-subscription)
* [ラボごとにいくつの VM を作成できますか。](#how-many-vms-can-i-create-per-lab)
* [ラボへの直接リンクを共有するにはどうすればよいですか。](#how-do-i-share-a-direct-link-to-my-lab)
* [Microsoft アカウントとは何ですか。](#what-is-a-microsoft-account)

## <a name="troubleshooting"></a>トラブルシューティング
* [VM の作成時にアーティファクトでエラーが発生しました。どのようにトラブルシューティングすればよいですか。](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
* [既存の仮想ネットワークが正しく保存されないのはなぜですか。](#why-isnt-my-existing-virtual-network-saving-properly)
* [PowerShell からプロビジョニングするときに、"親リソースが見つからない" というエラーが発生する理由を教えてください。](#why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell)  
* [VM のデプロイが失敗した場合、さらに詳しいエラー情報はどこで確認できますか。](#where-can-i-find-more-error-information-if-a-vm-deployment-fails)  

### <a name="what-if-my-question-isnt-answered-here"></a>ここに質問の答えがない場合はどうすればいいですか。
質問がここに表示されていない場合はご連絡ください。答えを見つけるお手伝いをします。

* この FAQ の最後に掲載されている [Disqus スレッド](#comments) に質問を投稿し、Azure Cache チームや他のコミュニティ メンバーとこの記事についてやり取りしてください。
* さらに多くの人と情報交換する場合は、 [Azure DevTest Labs MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)に質問を投稿して、Azure DevTest Labs チームや他のコミュニティ メンバーとやり取りします。
* 機能要求を作成する場合は、要求とアイデアを [Azure DevTest Labs のユーザーの声](https://feedback.azure.com/forums/320373-azure-devtest-labs)に送信します。

### <a name="why-should-i-use-azure-devtest-labs"></a>なぜ Azure DevTest Labs を使用する必要があるのですか。
Azure DevTest Labs を使用すると、チームの時間と費用を節約できます。 開発者は、複数のベースを使用して各自の環境を作成し、アーティファクトを使用してアプリケーションを迅速に展開および構成できます。 カスタム イメージとフォーミュラを使用して、仮想マシンをテンプレートとして保存して簡単に再現できます。 さらに、Labs では複数の構成可能なポリシーが提供されており、これによりラボ管理者は無駄を削減してチームの環境を管理することができます。 これらのポリシーには、自動シャットダウン、コストのしきい値、ユーザーごとの最大 VM 数、最大 VM サイズなどが含まれます。 Azure DevTest Labs の詳細については、[概要](devtest-lab-overview.md)を確認するか、[入門ビデオ](/documentation/videos/videos/what-is-azure-devtest-labs)をご覧ください。

### <a name="what-does-worry-free-self-service-mean"></a>「worry free, self-service (心配無用のセルフ サービス)」とはどのような意味ですか。
"心配無用のセルフ サービス” とは、開発者およびテスト担当者が必要に応じて各自の環境を作成し、管理者は Azure DevTest Labs によって無駄を最小化して管理コストを制御できる安心感を得られることを示します。 管理者は、許可される VM のサイズ、VM の最大数、VM の起動およびシャットダウン時間を指定できます。 Azure DevTest Labs では、ラボ内のリソースがどのように使用されているかを把握するために、コストを監視してアラートを設定することも容易になります。

### <a name="how-can-i-use-azure-devtest-labs"></a>Azure DevTest Labs を使用するにはどうすればよいですか。
Azure DevTest Labs は、開発環境やテスト環境が必要であり、迅速にこれを再現したり、コスト節約ポリシーで管理したりする場合に常に役立ちます。

お客様が Azure DevTest Labs を使用しているシナリオのいくつかを次に示します。

* 開発環境およびテスト環境を 1 か所で管理し、ポリシーを使用してコストを削減し、カスタム イメージを使用してチーム全体でビルドを共有します。
* 開発段階全体にわたってディスクの状態を保存するために、カスタム イメージを使用してアプリケーションを開発します。
* 進行状況に関連してコストを追跡します。
* 品質保証テスト用の大容量テスト環境を作成します。
* さまざまな環境でアプリケーションを容易に構成して再現するために、アーティファクトとフォーミュラを使用します。
* ハッカソン (共同開発またはテスト作業) 用の VM を配布し、イベントの終了時に容易にプロビジョニング解除します。

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Azure DevTest Labs の課金方法を教えてください。
Azure DevTest Labs は、無料サービスです。つまり、ラボの作成や、ポリシー、テンプレート、アーティファクトの構成は無料です。 仮想マシン、ストレージ アカウント、仮想ネットワークなど、ラボ内で使用する Azure リソースに対してのみ課金されます。 ラボ リソースのコストの詳細については、「[Azure DevTest Labs の価格](https://azure.microsoft.com/pricing/details/devtest-lab/)」をご覧ください。

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Azure DevTest Labs の各種セキュリティ レベルはどのようなものですか。
セキュリティ アクセスは、 [Azure のロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-built-in-roles.md)によって決定されます。 アクセスのしくみを理解するには、RBAC によって定義されているアクセス許可、ロール、およびスコープの違いを理解することが有用です。

* **アクセス許可** - アクセス許可とは、特定のアクションへのアクセスを定義したものです。 たとえば、すべての仮想マシンへの読み取りアクセス許可などがあります。
* **ロール** - ロールとは、グループ化してユーザーに割り当てることができる一連のアクセス許可です。 たとえば、"サブスクリプション所有者" ロールは、サブスクリプション内のすべてのリソースにアクセスできます。
* **スコープ** - スコープとは、Azure リソースの階層内のレベルのことです。 たとえば、リソース グループや単一のラボ、またはサブスクリプション全体などがスコープになります。

Azure DevTest Labs のスコープ内では、ユーザーのアクセス許可を定義する 2 種類のロールがあります。ラボ所有者とラボ ユーザーです。

* **ラボ所有者** - ラボ所有者は、ラボ内のすべてのリソースにアクセスできます。 そのため、ポリシーの変更、任意の VM の読み取りと書き込み、仮想ネットワークの変更などを行うことができます。
* **ラボ ユーザー**: ラボ ユーザーは VM、ポリシー、仮想ネットワークなど、すべてのラボ リソースを表示できますが、他のユーザーが作成したポリシーまたは VM を変更することはできません。 Azure DevTest Labs にカスタム ロールを作成することもできます。実施方法については、記事「[特定のラボ ポリシーに対するアクセス許可をユーザーに付与する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)」をご覧ください。

スコープは階層構造を持つため、ユーザーが特定のスコープのアクセス許可を持つ場合は、含まれているすべての下位のスコープでそのアクセス許可が自動的に付与されます。 たとえば、ユーザーにサブスクリプション所有者のロールが割り当てられている場合、ユーザーはサブスクリプションのすべてのリソースにアクセスできます。 これには、すべての仮想マシン、すべての仮想ネットワーク、およびすべてのラボが含まれます。てのリソースにアクセスできます。 このため、サブスクリプション所有者は、ラボ所有者のロールを自動的に継承します。 ただし、その逆は真ではありません。 ラボ所有者はラボにアクセスできます。これはサブスクリプション レベルよりも下位のスコープです。 そのため、ラボ所有者はラボの外にある仮想マシン、仮想ネットワーク、またはいずれのリソースも表示できません。

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>特定の 1 つのタスクの実行をユーザーに許可するようにロールを作成するにはどうすればよいですか。
カスタム ロールを作成し、そのロールにアクセス許可を割り当てる方法についての包括的な記事がこちらにあります。 ラボ内のすべての VM を開始および停止するアクセス許可を持つロール "DevTest Labs Advanced User" を作成するスクリプトの例を次に示します。

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advance User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  

### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs は、CI/CD ツールチェーンと統合されますか。
VSTS を使用している場合は、Azure DevTest Labs でのリリース パイプラインを自動化できる、 [Azure DevTest Labs タスク拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) があります。 この拡張機能の使用方法には次のようなものがあります。

* VM を自動で作成してデプロイし、Azure File Copy または PowerShell の VSTS タスクを使用して最新のビルドで構成します。
* テストの終了後、詳しい調査を目的として同じ VM 上でバグを再現するために、VM の状態を自動的にキャプチャします。
* 不要になったときに、リリース パイプラインの最後に VM を削除します。

次のブログ投稿では、VSTS 拡張機能の使用に関するガイダンスおよび情報を提供しています。

* [Azure DevTest Labs – VSTS extension (Azure DevTest Labs – VSTS 拡張機能)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Deploying a new VM in an existing AzureDevTestLab from VSTS (VSTS からの既存 AzureDevTestLab での新しい VM のデプロイ)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (VSTS のリリース管理を使用した AzureDevTestLabs への継続的なデプロイメント)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

その他の CI/CD ツールチェーンについては、VSTS のタスク拡張機能で実施可能な前述のシナリオすべてを、[Azure PowerShell コマンドレット](../azure-resource-manager/resource-group-template-deploy.md)と [.NET SDKs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) を使用して、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)をデプロイすることによって同様に実施できます。 [DevTest Labs 用の REST API](http://aka.ms/dtlrestapis) を使用して、お使いのツール チェーンと統合することもできます。  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Azure DevTest Labs で表示される特定の VM が Azure 仮想マシン ブレードに表示されないのはなぜですか。
Azure DevTest Labs で VM を作成すると、その VM にアクセスするためのアクセス許可が付与されます。 この VM は、Labs のブレードと **Virtual Machines** のブレードの両方に表示されます。 DevTest Labs ロールのユーザーは、ラボの **[All Virtual Machines (すべての仮想マシン)]** ブレードで、ラボで作成されたすべての仮想マシンを確認することができます。 ただし、DevTest Labs ロールのユーザーには、他のユーザーによって作成された VM リソースへの読み取りアクセス許可は付与されません。 そのため、これらの VM は **Virtual Machines** のブレードに表示されません。

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>カスタム イメージとフォーミュラの違いは何ですか。
カスタム イメージは VHD (仮想ハード ディスク) である一方、フォーミュラは保存して再現できる追加設定を構成可能なイメージです。 同じ基本的な不変のイメージで複数の環境をすばやく作成する場合は、カスタム イメージをお勧めします。 フォーミュラは、最新のビット、仮想ネットワーク/サブネット、または特定のサイズで VM の構成を再現する必要がある場合に適しています。 詳細については、記事「 [Comparing custom images and formulas in DevTest Labs (DevTest Labs のカスタム イメージとフォーミュラの比較)](devtest-lab-comparing-vm-base-image-types.md)」をご覧ください。

### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>同じテンプレートから複数の VM を一度に作成するにはどうすればよいですか。
VM の作成中に [VSTS タスク拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)または [Azure Resource Manager テンプレートの生成](devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template)を使用し、[Windows PowerShell から Azure Resource Manager テンプレートをデプロイ](../azure-resource-manager/resource-group-template-deploy.md)することができます。

### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>既存の Azure VM を Azure DevTest Labs ラボに移動するにはどうすればよいですか。
当社では、Azure DevTest Labs に VM を直接移動するソリューションを設計中ですが、現時点では、既存の VM を Azure DevTest Labs に次のようにコピーすることで対処できます。

1. この [Windows PowerShell スクリプト](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)
2. [カスタム イメージを作成](devtest-lab-create-template.md) します。
3. カスタム イメージからラボ内に VM を作成します。

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>複数のディスクを VM に接続できますか。
VM への複数ディスクの接続はサポートされています。  

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>テストに Windows OS イメージを使用する場合、MSDN サブスクリプションを購入する必要はありますか。
Azure での開発またはテストにクライアント OS イメージ (Windows 7 以降) を使用する場合は、購入する必要があります。次のいずれかを行ってください。

- [MSDN サブスクリプションを購入する](https://www.visualstudio.com/products/how-to-buy-vs)。
- エンタープライズ契約をご利用の場合は、[Enterprise Dev/Test プラン](https://azure.microsoft.com/en-us/offers/ms-azr-0148p)で Azure サブスクリプションを作成する。

各 MSDN サービスの Azure クレジットの詳細については、「[Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/)」を参照してください。

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>カスタム イメージを作成するために VHD ファイルをアップロードするプロセスを自動化するにはどうすればよいですか。
2 つのオプションがあります。

* [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) を使用して、ラボに関連付けられているストレージ アカウントに VHD ファイルをコピーまたはアップロードできます。
* [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md) は Windows、OSX、Linux で稼働するスタンドアロン アプリです。   

ラボに関連付けられているコピー先ストレージ アカウントを検索するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. 左側にあるパネルから **[リソース グループ]** を選択します。
3. ラボに関連付けられているリソース グループを見つけて選択します。
4. **[概要]** ブレードで、いずれかのストレージ アカウントを選択します。
5. **[BLOB]**を選択します。
6. 一覧内でアップロードを検索します。 存在しない場合は、手順 4 に戻り、別のストレージ アカウントを試します。
7. AzCopy コマンドで、コピー先としてこの **URL** を使用します。

### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>ラボ内の VM をすべて削除するプロセスを自動化するにはどうすればよいですか。
Azure Portal でラボから VM を削除できる以外に、PowerShell スクリプトを使用してラボ内の VM をすべて削除することもできます。 次の例で、**Values to change** コメントの下のパラメーター値を変更してください。 `subscriptionId`、`labResourceGroup`、および `labName` の値は、Azure Portal のラボのブレードから取得することができます。

    # Delete all the VMs in a lab

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>アーティファクトとは何ですか。
アーティファクトは、最新のビットまたは開発用ツールを VM 上にデプロイするために使用できるカスタマイズ可能な要素です。 いくつかの簡単なクリック操作で作成時に VM に接続され、VM がプロビジョニングされると、アーティファクトによって VM がデプロイおよび構成されます。 [パブリック GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)には、様々な既存のアーティファクトが存在しますが、[独自のアーティファクトの作成](devtest-lab-artifact-author.md)も簡単です。

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートからラボを作成するにはどうすればよいですか。
弊社が提供する[ラボの Azure Resource Manager テンプレートの GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)を、そのままの状態でデプロイしたり、修正してラボ用のカスタム テンプレートを作成したりできます。 各テンプレートにはリンクが含まれます。このリンクをクリックすると、Azure サブスクリプションにラボをそのままデプロイできます。また、テンプレートをカスタマイズし、[PowerShell や Azure CLI を使用してデプロイ](../azure-resource-manager/resource-group-template-deploy.md)することもできます。

### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>別々のリソース グループに作成した VM が任意の名前を持つのはなぜですか。 これらのリソース グループの名前や内容を変更できますか。
リソース グループは、Azure DevTest Labs がユーザーのアクセス許可と仮想マシンへのアクセスを管理するため、ご質問で述べられたように作成されます。 VM を別のリソース グループに移動してご希望の名前を使用することはできますが、この方法はお勧めではありません。 柔軟性の向上を目指して、機能の改善に取り組んでいます。   

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>同じサブスクリプションにラボをいくつ作成できますか。
サブスクリプションごとに作成できるラボの数に特定の制限はありません。 ただし、使用できるリソースの数にはサブスクリプションごとの制限があります。 [Azure サブスクリプションに適用される制限とクォータ](../azure-subscription-service-limits.md)および[これらの制限を増やす方法](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)に関する記事をご覧ください。

### <a name="how-many-vms-can-i-create-per-lab"></a>ラボごとにいくつの VM を作成できますか。
ラボごとに作成できる VM の数に特定の制限はありません。 ただし、現時点のラボでは、Standard Storage で同時に実行できる VM の数は 40 個程度、Premium Storage で同時に実行できる VM の数は 25 個に限られています。 現在、これらの制限値を増やす作業に取り組んでいます。

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>ラボへの直接リンクを共有するにはどうすればよいですか。
直接リンクをラボ ユーザーと共有するには、次の手順を実行できます。

1. Azure Portal でラボを参照します。
2. お使いのブラウザーからラボの URL をコピーし、ラボ ユーザーと共有します。

> [!NOTE]
> ラボ ユーザーが、[Microsoft アカウント](#what-is-a-microsoft-account)を持つ外部ユーザーであり、会社の Active Directory に属していない場合、指定されたリンクに移動するとエラーが表示される可能性があります。 エラーが表示された場合は、Azure Portal の右上隅に表示されている自分の名前をクリックし、メニューの **[ディレクトリ]** セクションからラボが存在するディレクトリを選択するように、ユーザーに指示してください。
>
>

### <a name="what-is-a-microsoft-account"></a>Microsoft アカウントとは何ですか。
Microsoft アカウントは、Microsoft のデバイスおよびサービスで行うほぼすべての操作に対して使用するものです。 電子メール アドレスとパスワードで構成され、Skype、Outlook.com、OneDrive、Windows Phone、および Xbox LIVE へのサインインに使用します。つまり、ファイル、写真、連絡先、および設定が、任意のデバイスで利用できることを意味します。

> [!NOTE]
> Microsoft アカウントは、以前は "Windows Live ID" と呼ばれていました。
>
>

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM の作成時にアーティファクトでエラーが発生しました。 どのようにトラブルシューティングすればよいですか。
障害が発生したアーティファクトに関するログを取得する方法については、MVP の 1 人によるブログ記事「[How to troubleshoot failing Artifacts in AzureDevTestLabs (AzureDevTestLabs 内のアーティファクトの障害をトラブルシューティングする方法)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)」をご覧ください。

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>既存の仮想ネットワークが正しく保存されないのはなぜですか。
仮想ネットワーク名にピリオドが含まれていることが原因となっている可能性があります。 その場合は、ピリオドを削除するかハイフンに置き換えてから、仮想ネットワークをもう一度保存してみてください。

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell"></a>PowerShell から VM をプロビジョニングするときに、"親リソースが見つからない" ことを示すエラーが発生する理由を教えてください。
あるリソースが別のリソースの親である場合、子リソースを作成する前に親リソースが存在する必要があります。 存在しない場合、**ParentResourceNotFound** エラーが発生します。 親リソースに依存関係を指定しなかった場合、子リソースは親の前にデプロイされる可能性があります。

VM は、リソース グループ内のラボの下の子リソースです。 PowerShell を介して Azure リソース テンプレートを使用してデプロイした場合、PowerShell スクリプトで指定されるリソース グループ名は、ラボのリソース グループ名である必要があります。 詳細については、[Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound)に関する記事をご覧ください。

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>VM のデプロイが失敗した場合、さらに詳しいエラー情報はどこで確認できますか。
VM デプロイのエラーは、アクティビティ ログに記録されます。 VM のアクティビティ ログは、ラボの VM ブレードにあるリソース メニューの **[監査ログ]** または **[Virtual machine diagnostics (仮想マシン診断)]** で確認できます。このブレードは、**[My virtual machines (マイ仮想マシン)]** の一覧から VM を選択すると表示されます。

VM デプロイの開始前、たとえば、VM で作成されたリソースのサブスクリプション制限を超えたときなどに、デプロイ エラーが発生することがあります。 この場合、エラーの詳細は、**[構成とポリシー]** 設定の下部にある、ラボ レベルの**アクティビティ ログ**に記録されます。 Azure でのアクティビティ ログ使用の詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit)」を参照してください。

