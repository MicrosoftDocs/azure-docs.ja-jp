---
title: Azure DevTest Labs に関する FAQ | Microsoft Docs
description: Azure DevTest Labs についてよく寄せられる質問の回答を示します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: d8fc929b21bedcb3e7e2bd3f5ed1d6c867bca3c8
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803376"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs に関する FAQ
Azure DevTest Labs について特に多く寄せられる質問にお答えします。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**全般**

## <a name="blog-post"></a>ブログ記事
DevTest Labs チームのブログは 2019 年 3 月 20 日時点で廃止されました。 

### <a name="where-can-i-track-feature-updates-going-forward"></a>今後の機能更新はどこで追跡できるのでしょうか。
今後、Azure ブログと Azure の更新情報で、機能更新や情報ブログ記事を投稿していきます。 これらのブログ記事は、必要に応じて、Microsoft のドキュメントにもリンクされます。

[DevTest Labs Azure ブログ](https://azure.microsoft.com/blog/tag/azure-devtest-labs/)と [DevTest Labs Azure の更新情報](https://azure.microsoft.com/updates/?product=devtest-lab)をご覧になり、DevTest Labs の新機能に関する情報を入手するようにしてください。

### <a name="what-happens-to-the-existing-blog-posts"></a>既存のブログ記事はどうなりますか。
現在、既存のブログ記事 (障害更新情報を除く) の [DevTest Labs ドキュメント](devtest-lab-overview.md)への移行を進めています。 MSDN ブログが非推奨になると、DevTest Labs のドキュメントの概要にリダイレクトされます。 リダイレクトされたら、タイトルの 'フィルター条件' で探している記事を検索できます。 まだすべての記事を移行していませんが、今月末までに完了するはずです。 


### <a name="where-do-i-see-outage-updates"></a>障害更新情報はどこで確認できますか。
今後、Twitter ハンドルを使用して、障害更新情報を投稿する予定です。 Twitter でフォローして、障害と既知のバグに関する最新の更新情報を取得してください。

### <a name="twitter"></a>Twitter 
Twitter ハンドル: [@azlabservices](https://twitter.com/azlabservices)

## <a name="what-if-my-question-isnt-answered-here"></a>ここに質問の答えがない場合はどうすればいいですか。
質問がここに記載されていない場合はご連絡ください。答えを見つけるお手伝いをいたします。

* この FAQ の末尾で質問を投稿してください。 この記事について、Azure Cache チームや他のコミュニティ メンバーと情報交換できます。
* さらに多くの人々と交流するには、[Azure DevTest Labs MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)に質問を投稿してください。 Azure DevTest Labs チームや他のコミュニティ メンバーと情報交換できます。
* 機能を要求する場合は、要求とアイデアを [Azure DevTest Labs のユーザーの声](https://feedback.azure.com/forums/320373-azure-devtest-labs)に送信してください。

## <a name="why-should-i-use-azure-devtest-labs"></a>なぜ Azure DevTest Labs を使用する必要があるのですか。
Azure DevTest Labs を使用すると、チームの時間と費用を節約できます。 開発者は、複数の異なるベースを使用して独自の環境を作成できます。 また、アーティファクトを使用してアプリケーションを速やかにデプロイし、構成することもできます。 カスタム イメージと数式を使用して、仮想マシン (VM) をテンプレートとして保存し、チームで簡単に再現できます。 さらに、DevTest Labs には複数の構成可能なポリシーも用意されています。ラボ管理者は、これらのポリシーを使用して無駄を削減し、チームの環境を管理できます。 これらのポリシーには、自動シャットダウン、コストのしきい値、ユーザーごとの最大 VM 数、最大 VM サイズなどが含まれます。 DevTest Labs の詳細については、[概要](devtest-lab-overview.md)を確認するか、[入門ビデオ](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)をご覧ください。

## <a name="what-does-worry-free-self-service-mean"></a>"心配無用のセルフ サービス" とはどういう意味ですか。
"心配無用のセルフ サービス" とは、開発者とテスト担当者が、必要に応じて独自の環境を作成することを意味します。 管理者は、DevTest Labs を使用することで無駄を最小限に抑え、コストを管理できるという安心感が得られます。 管理者は、許可される VM のサイズ、VM の最大数、VM の起動およびシャットダウン時間を指定できます。 また、DevTest Labs では、ラボ リソースの使用状況を常に把握できるように、コストの監視とアラートの設定も簡単に行うことできます。

## <a name="how-can-i-use-devtest-labs"></a>DevTest Labs を使用するにはどうすればよいですか。
DevTest Labs は、開発環境やテスト環境が必要であり、これらを迅速に再現したり、コスト節約ポリシーを使用して管理したりする場合に常に役立ちます。

お客様は、次のようなシナリオで DevTest Labs を使用しています。

* 開発環境とテスト環境を 1 か所で管理する。 ポリシーを使用してコストを削減し、カスタム イメージを作成してチーム全体でビルドを共有する。
* 開発段階全体にわたってディスクの状態を保存するために、カスタム イメージを使用してアプリケーションを開発する。
* 進行状況に関連してコストを追跡する。
* 品質保証テスト用の大容量テスト環境を作成する。
* アーティファクトと数式を使用して、さまざまな環境でアプリケーションを簡単に構成し、再現する。
* ハッカソン (共同開発またはテスト作業) 用の VM を配布し、イベントの終了時に簡単にプロビジョニング解除する。

## <a name="how-am-i-billed-for-devtest-labs"></a>DevTest Labs の課金方法を教えてください。
DevTest Labs は無料サービスです。 DevTest Labs でのラボの作成や、ポリシー、テンプレート、アーティファクトの構成は無料です。 VM、ストレージ アカウント、仮想ネットワークなど、ラボ内で使用する Azure リソースに対してのみ課金されます。 ラボ リソースのコストの詳細については、「[Azure DevTest Labs の価格](https://azure.microsoft.com/pricing/details/devtest-lab/)」をご覧ください。


**セキュリティ**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>DevTest Labs の各種セキュリティ レベルはどのようなものですか。
セキュリティ アクセスは、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/built-in-roles.md) によって決定されます。 アクセスのしくみを理解するには、RBAC で定義されているアクセス許可、ロール、スコープの違いを理解することが有用です。

* **アクセス許可**: アクセス許可とは、特定のアクションへのアクセスを定義したものです。 たとえば、すべての VM への読み取りアクセス許可などがあります。
* **ロール**: ロールとは、グループ化してユーザーに割り当てることができる一連のアクセス許可です。 たとえば、サブスクリプション所有者ロールが割り当てられたユーザーは、サブスクリプション内のすべてのリソースにアクセスできます。
* **[スコープ]**: スコープとは、Azure リソースの階層内のレベルです。 たとえば、リソース グループ、単一のラボ、またはサブスクリプション全体をスコープとして指定できます。

DevTest Labs のスコープ内には、ユーザーのアクセス許可を定義する次の 2 種類のロールがあります。

* **ラボ所有者**:ラボ所有者は、ラボ内のすべてのリソースにアクセスできます。 ポリシーの変更、任意の VM に対する読み取りと書き込み、仮想ネットワークの変更などを行うことができます。
* **ラボ ユーザー**:ラボ ユーザーは、VM、ポリシー、仮想ネットワークなど、すべてのラボ リソースを表示できます。 ただし、他のユーザーが作成したポリシーや VM を変更することはできません。 

DevTest Labs にカスタム ロールを作成することもできます。 DevTest Labs にカスタム ロールを作成する方法については、「[特定のラボ ポリシーに対するアクセス許可をユーザーに付与する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)」をご覧ください。

スコープは階層構造を持つため、特定のスコープでアクセス許可を持つユーザーには、そのスコープのすべての下位のスコープでそれらのアクセス許可が自動的に付与されます。 たとえば、ユーザーにサブスクリプション所有者のロールが割り当てられている場合、そのユーザーはサブスクリプションのすべてのリソースにアクセスできます。 これには、すべての VM、すべての仮想ネットワーク、すべてのラボが含まれます。 サブスクリプション所有者は、ラボ所有者のロールを自動的に継承します。 ただし、その逆は真ではありません。 ラボ所有者はラボにアクセスできます。これはサブスクリプション レベルよりも下位のスコープです。 そのため、ラボ所有者はラボの外部の VM、仮想ネットワーク、またはその他のリソースを表示することはできません。

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>特定の 1 つのタスクの実行をユーザーに許可するようにロールを作成するにはどうすればよいですか。
カスタム ロールを作成し、ロールにアクセス許可を割り当てる方法の詳細については、「[特定のラボ ポリシーに対するアクセス許可をユーザーに付与する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)」をご覧ください。 ラボ内のすべての VM を起動および停止するアクセス許可を持つ *DevTest Labs Advanced User* ロールを作成するスクリプトの例を次に示します。

    $policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  


**CI/CD 統合と自動化**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs は、CI/CD ツールチェーンと統合されますか。
Azure DevOps を使用している場合は、[DevTest Labs Tasks の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)を使用して、DevTest Labs でのリリース パイプラインを自動化できます。 この拡張機能を使用して実行できるタスクの一部を次に示します。

* VM を自動的に作成してデプロイします。 Azure ファイル コピーまたは PowerShell の Azure DevOps Services タスクを使用して、最新のビルドで VM を構成することもできます。
* テストの終了後、詳しい調査を目的として同じ VM 上でバグを再現するために、VM の状態を自動的にキャプチャします。
* VM が不要になったら、リリース パイプラインの最後に VM を削除します。

次のブログ記事では、Azure DevOps Services 拡張機能の使用方法に関するガイダンスと情報を提供しています。

* [DevTest Labs と Azure DevOps の拡張機能](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Azure DevOps Services から既存の DevTest Labs ラボに新しい VM をデプロイする](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Azure DevOps Services のリリース管理を使用した DevTest Labs への継続的なデプロイ](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

他の継続的インテグレーション (CI)/継続的デリバリー (CD) ツールチェーンの場合、[Azure PowerShell コマンドレット](../azure-resource-manager/resource-group-template-deploy.md)と [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) を使用して [Azure Resource Manager テンプレート](https://aka.ms/dtlquickstarttemplate)をデプロイすることによって、同じシナリオを実現できます。 [DevTest Labs 用 REST API](https://aka.ms/dtlrestapis) を使用して、お使いのツールチェーンと統合することもできます。  


**仮想マシン**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>DevTest Labs で表示されている VM が [仮想マシン] ページに表示されないのはなぜですか。
DevTest Labs で VM を作成すると、その VM にアクセスするためのアクセス許可が付与されます。 この VM は、Labs ページと **[仮想マシン]** ページの両方に表示されます。 DevTest Labs のラボ ユーザー ロールが割り当てられているユーザーは、ラボの **[すべての仮想マシン]** ページで、ラボで作成されたすべての VM を確認できます。 ただし、DevTest Labs のラボ ユーザー ロールが割り当てられているユーザーには、他のユーザーが作成した VM リソースへの読み取りアクセス権が自動的に付与されるわけではありません。 そのため、これらの VM は **[仮想マシン]** ページには表示されません。

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>カスタム イメージと数式の違いは何ですか。
カスタム イメージは仮想ハード ディスク (VHD) です。 数式は、追加設定で構成し、保存して再現できるイメージです。 同じ基本的な不変イメージを使用して複数の環境をすばやく作成する場合は、カスタム イメージをお勧めします。 数式は、最新のビットで、仮想ネットワークまたはサブネットの一部として、または特定のサイズの VM として、VM の構成を再現する場合に適しています。 詳細については、「[DevTest ラボのカスタム イメージと数式の比較](devtest-lab-comparing-vm-base-image-types.md)」をご覧ください。

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>同じテンプレートから複数の VM を一度に作成するにはどうすればよいですか。
同じテンプレートから複数の VM を一度に作成する場合、次の 2 つのオプションがあります。
* [Azure DevOps Tasks の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)を使用することができます。 
* VM の作成中に [Resource Manager テンプレートを生成](devtest-lab-add-vm.md#save-azure-resource-manager-template)し、[Windows PowerShell から Resource Manager テンプレートをデプロイ](../azure-resource-manager/resource-group-template-deploy.md)します。

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>既存の Azure VM を DevTest Labs ラボに移動するにはどうすればよいですか。
既存の VM を DevTest Labs にコピーするには、次の手順に従います。

1. PowerShell スクリプトを使用して、既存の VM の VHD ファイルをコピーします。
   * Resource Manager:[CopyRmVHDFromVMToLab.ps1](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyRmVHDFromVMToLab.ps1)
   * クラシック:[CopyClassicVHDFromVMToLab.ps1](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyClassicVHDFromVMToLab.ps1)
2. DevTest Labs ラボ内に[カスタム イメージを作成](devtest-lab-create-template.md)します。
3. カスタム イメージからラボ内に VM を作成します。

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>複数のディスクを VM に接続できますか。
はい。複数のディスクを VM に接続できます。  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>テストに Windows OS イメージを使用する場合、MSDN サブスクリプションを購入する必要はありますか。
Azure での開発またはテストに Windows クライアント OS イメージ (Windows 7 以降) を使用するには、次のいずれかを行う必要があります。

- [MSDN サブスクリプションを購入する](https://www.visualstudio.com/products/how-to-buy-vs)。
- Enterprise Agreement をご利用の場合は、[Enterprise Dev/Test オファー](https://azure.microsoft.com/offers/ms-azr-0148p)で Azure サブスクリプションを作成する。

各 MSDN サービスの Azure クレジットの詳細については、「[Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>カスタム イメージを作成するために VHD ファイルをアップロードするプロセスを自動化するにはどうすればよいですか。
カスタム イメージを作成するために VHD ファイルのアップロードを自動化する場合、次の 2 つのオプションがあります。

* [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) を使用して、ラボに関連付けられているストレージ アカウントに VHD ファイルをコピーまたはアップロードします。
* [Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用します。 ストレージ エクスプローラーは、Windows、OS X、Linux で動作するスタンドアロン アプリです。   

ラボに関連付けられているコピー先ストレージ アカウントを検索するには、次の手順に従います。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. 左側のメニューの **[リソース グループ]** を選択します。
3. ラボに関連付けられているリソース グループを見つけて選択します。
4. **[概要]** で、いずれかのストレージ アカウントを選択します。
5. **[BLOB]** を選択します。
6. 一覧内でアップロードを検索します。 存在しない場合は、手順 4. に戻り、別のストレージ アカウントを試します。
7. AzCopy コマンドで、コピー先として **URL** を使用します。

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>ラボ内の VM をすべて削除するプロセスを自動化するにはどうすればよいですか。
Azure Portal でラボから VM を削除できます。 また、PowerShell スクリプトを使用して、ラボ内の VM をすべて削除することもできます。 次の例では、**Values to change** コメントの下のパラメーター値を変更します。 `subscriptionId`、`labResourceGroup`、`labName` の各値は、Azure Portal のラボ ウィンドウから取得できます。

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.Name -like "$($lab.Name)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzResource -ResourceId $labVM.ResourceId -Force
    }

**アーティファクト**
## <a name="what-are-artifacts"></a>アーティファクトとは何ですか。
アーティファクトは、最新のビットまたは開発用ツールを VM にデプロイするために使用できるカスタマイズ可能な要素です。 VM の作成時に、アーティファクトを VM に接続します。 VM がプロビジョニングされると、アーティファクトによって VM がデプロイされ、構成されます。 [パブリック GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)で、さまざまな既存のアーティファクトを利用できます。 また、[独自のアーティファクトを作成](devtest-lab-artifact-author.md)することもできます。


**ラボの構成**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Resource Manager テンプレートからラボを作成するにはどうすればよいですか。
Microsoft では、そのままデプロイしたり、変更してラボ用のカスタム テンプレートを作成したりできる、[ラボの Azure Resource Manager テンプレートの GitHub リポジトリ](https://aka.ms/dtlquickstarttemplate)を提供しています。 各テンプレートには、独自の Azure サブスクリプションにラボをそのままデプロイするためのリンクが含まれています。 また、テンプレートをカスタマイズし、[PowerShell または Azure CLI を使用してデプロイ](../azure-resource-manager/resource-group-template-deploy.md)することもできます。

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>VM が任意の名前でさまざまなリソース グループに作成されるのはなぜですか。 これらのリソース グループの名前や内容を変更できますか。
リソース グループがこのように作成されるのは、DevTest Labs がユーザーのアクセス許可と VM へのアクセスを管理できるようにするためです。 VM を別のリソース グループに移動し、希望の名前を使用することはできますが、リソース グループには変更を加えないことをお勧めします。 柔軟性の向上を目指して、機能の改善に取り組んでいます。   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>同じサブスクリプションにラボをいくつ作成できますか。
サブスクリプションごとに作成できるラボの数に特定の制限はありません。 ただし、サブスクリプションごとに使用できるリソースの量には制限があります。 [Azure サブスクリプションの制限とクォータ](../azure-subscription-service-limits.md)および[これらの制限を引き上げる方法](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)に関する記事をご覧ください。

## <a name="how-many-vms-can-i-create-per-lab"></a>ラボごとにいくつの VM を作成できますか。
ラボごとに作成できる VM の数に特定の制限はありません。 ただし、使用できるリソース (VM コア、パブリック IP など) はサブスクリプションごとに制限されています。 [Azure サブスクリプションの制限とクォータ](../azure-subscription-service-limits.md)および[これらの制限を引き上げる方法](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)に関する記事をご覧ください。

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>ラボへの直接リンクを共有するにはどうすればよいですか。

1. Azure Portal でラボに移動します。
2. ブラウザーからラボの URL をコピーし、ラボ ユーザーと共有します。

> [!NOTE]
> ラボ ユーザーが [Microsoft アカウント](#what-is-a-microsoft-account)を持つ外部ユーザーであり、組織の Active Directory インスタンスのメンバーではない場合、そのユーザーが共有リンクにアクセスしようとすると、エラー メッセージが表示される場合があります。 外部ユーザーにエラー メッセージが表示された場合は、そのユーザーに、Azure Portal の右上隅に表示されている自分の名前をまず選択してもらいます。 これで、ユーザーはメニューの **[ディレクトリ]** セクションで、ラボが存在するディレクトリを選択できるようになります。
>
>

## <a name="what-is-a-microsoft-account"></a>Microsoft アカウントとは何ですか。
Microsoft アカウントは、Microsoft のデバイスとサービスで実行するほぼすべての操作に使用するアカウントです。 Skype、Outlook.com、OneDrive、Windows Phone、Xbox LIVE へのサインインに使用する電子メール アドレスとパスワードで構成されます。 1 つのアカウントで、どのデバイスからでもファイル、写真、連絡先、設定を利用できます。

> [!NOTE]
> Microsoft アカウントは、以前は *Windows Live ID* と呼ばれていました。
>
>


**トラブルシューティング**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM の作成時にアーティファクトでエラーが発生しました。 どのようにトラブルシューティングすればよいですか。
失敗したアーティファクトのログを取得する方法については、[DevTest Labs でアーティファクトの失敗を診断する方法](devtest-lab-troubleshoot-artifact-failure.md)に関する記事をご覧ください。

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>既存の仮想ネットワークが正しく保存されないのはなぜですか。
仮想ネットワーク名にピリオドが含まれていることが原因となっている可能性があります。 その場合は、ピリオドを削除するか、ハイフンに置き換えてみます。 その後、仮想ネットワークをもう一度保存してみてください。

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>PowerShell から VM をプロビジョニングしたときに、"親リソースが見つからない" ことを示すエラーが発生するのはなぜですか。
リソースが別のリソースの親である場合、子リソースを作成するには、親リソースが存在している必要があります。 親リソースが存在しない場合、**ParentResourceNotFound** メッセージが表示されます。 親リソースに依存関係を指定していない場合、子リソースは親の前にデプロイされる可能性があります。

VM は、リソース グループ内のラボの下の子リソースです。 PowerShell で Resource Manager テンプレートを使用して VM をデプロイした場合、PowerShell スクリプトで指定されたリソース グループ名がラボのリソース グループ名になります。 詳細については、[Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors)に関する記事をご覧ください。

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>VM のデプロイが失敗した場合、さらに詳しいエラー情報はどこで確認できますか。
VM のデプロイ エラーは、アクティビティ ログに記録されます。 ラボの VM のアクティビティ ログは、ラボの VM ページにあるリソース メニューの **[監査ログ]** または **[仮想マシンの診断]** で確認できます (VM ページは、**[自分の仮想マシン]** の一覧から VM を選択すると表示されます)。

VM のデプロイが開始される前に、デプロイ エラーが発生する場合もあります。 たとえば、VM で作成されたリソースのサブスクリプションの制限を超えた場合です。 この場合、ラボ レベルのアクティビティ ログにエラーの詳細が記録されます。 アクティビティ ログは、**[Configuration and policies]\(構成とポリシー\)** 設定の下部にあります。 Azure でのアクティビティ ログ使用の詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)」を参照してください。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
