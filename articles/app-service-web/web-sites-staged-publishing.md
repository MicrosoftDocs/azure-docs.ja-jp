---
title: "Azure App Service の Web アプリのステージング環境を設定する | Microsoft Docs"
description: "Azure App Service の Web アプリのステージングされた発行を使用する方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 8ec4e8699eb2f2e060db264634b04abfacf40e34
ms.lasthandoff: 03/27/2017


---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service でステージング環境を設定する
<a name="Overview"></a>

使用している App Service プラン モードが **Standard** または **Premium** である場合は、Web アプリ、モバイル バック エンド、API アプリを [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする際、既定の運用スロットではなく、個別のデプロイ スロットにデプロイすることができます。 デプロイ スロットは、実際には固有のホスト名を持つライブ アプリです。 アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つのデプロイ スロットの間でスワップすることができます。 デプロイ スロットにアプリケーションをデプロイすることには、次のメリットがあります。

* ステージング デプロイ スロットでアプリの変更を検証した後に、運用スロットにスワップできます。
* スロットにアプリをデプロイした後に運用サイトにスワップすると、運用サイトへのスワップ前にスロットのすべてのインスタンスが準備されます。 これにより、アプリをデプロイする際のダウンタイムがなくなります。 トラフィックのリダイレクトはシームレスであるため、スワップ操作によりドロップされる要求はありません。 このワークフロー全体は、スワップ前の検証が必要ない場合、 [自動スワップ](#Auto-Swap) を構成することで自動化できます。
* スワップ後も、以前のステージング アプリ スロットに元の運用アプリが残っているため、 運用スロットにスワップした変更が想定どおりでない場合は、適切な動作が確認されている元のサイトにすぐに戻すことができます。

サポートされるデプロイ スロット数は、App Service のプラン モードごとに異なります。 使用しているアプリのモードでサポートされるスロット数を確認するには、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。

* アプリに複数のスロットがある場合は、モードを変更することはできません。
* スケーリングは
* リンク済みリソースの管理は運用サイト スロットでのみサポートされています。 [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715) を使用している場合のみ、非運用スロットを異なる App Service プラン モードに変更することで、この運用スロットに対する影響を回避することができます。 なお、2 つのスロットをスワップする前には、非運用スロットと運用スロットを再度同じモードにする必要があります。

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>デプロイ スロットの追加
複数のデプロイ スロットを有効にするには、アプリが **Standard** または **Premium** モードで実行されている必要があります。

1. [Azure Portal](https://portal.azure.com/) でアプリの[リソース ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)を開きます。
2. **[デプロイ スロット]** オプションを選択し、**[スロットの追加]** をクリックします。
   
    ![新しいデプロイ スロットの追加][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > アプリがまだ **Standard** または **Premium** モードでない場合は、ステージングされた発行を有効にするためのモードを示すメッセージが表示されます。 その場合は、操作を継続する前に、**[アップグレード]** を選択してアプリの **[スケール]** タブに移動できます。
   > 
   > 
3. **[スロットの追加]** ブレードで、スロット名を設定し、別の既存のデプロイ スロットからアプリ構成を複製するかどうかを選択します。 チェック マークをクリックして続行します。
   
    ![構成元][ConfigurationSource1]
   
    初めてスロットを追加する場合、選択肢は 2 つのみです。運用環境内の既定のスロットから構成を複製するか、または複製しないかのどちらかです。
    複数のスロットを作成すると、運用サイトのスロット以外のスロットから構成を複製できるようになります。
   
    ![構成元][MultipleConfigurationSources]
4. アプリのリソース ブレードで **[デプロイメント スロット]** をクリックしてからデプロイ スロットをクリックして、他のアプリと同様のメトリックと構成のセットが含まれるスロットのリソース ブレードを開きます。 デプロイ スロットが表示されていることを通知するためにスロットの名前がブレードの上部に表示されます。
   
    ![デプロイ スロットのタイトル][StagingTitle]
5. スロットのブレードで、アプリの URL をクリックします。 デプロイメント スロットが独自のホスト名を持つライブ アプリであることに注意してください。 デプロイ スロットへのパブリック アクセスを制限する方法については、「 [App Service Web App - 運用環境以外のデプロイ スロットへの Web アクセスの禁止](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)」を参照してください。

デプロイ スロットを作成した直後は、コンテンツはありません。 スロットには、異なるリポジトリ分岐、またはまったく異なるリポジトリからデプロイできます。 またスロットの構成を変更することもできます。 コンテンツの更新には、デプロイ スロットに関連付けられた発行プロファイルまたはデプロイ資格情報を使用してください。  たとえば、 [Git を使用してこのスロットに発行する](app-service-deploy-local-git.md)ことができます。

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>デプロイ スロットの構成
別のデプロイ スロットから構成を複製する場合、複製された構成を編集することができます。 なお、スワップ先のコンテンツに反映される構成要素 (非スロット固有) もあれば、スワップ後にも同じスロットに維持される構成要素 (スロット固有) もあります。 次の一覧では、スロットのスワップ時に変更される構成を示します。

**スワップされる設定**:

* 一般設定 - フレームワーク バージョン、32/64 ビット、Web ソケットなど
* アプリ設定 (スロット固有として構成可能)
* 接続設定 (スロット固有として構成可能)
* ハンドラー マッピング
* 監視と診断の設定
* Web ジョブ コンテンツ

**スワップされない設定**:

* 発行エンドポイント
* カスタム ドメイン名
* SSL 証明書とバインド
* スケールの設定
* Web ジョブ スケジューラ

アプリ設定や接続文字列をスロット固有として構成する (スワップされないようにする) には、特定のスロットの **[アプリケーション設定]** ブレードにアクセスし、スロット固有にする構成要素の **[スロット設定]** ボックスを選択します。 構成要素をスロット固有としてマークすると、そのアプリに関連付けられたすべてのデプロイ スロットにわたってその要素がスワップできなくなります。

![スロットの設定][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>デプロイ スロットをスワップする 
アプリのリソース ブレードの **[概要]** または **[デプロイ スロット]** ビューでデプロイ スロットをスワップできます。

> [!IMPORTANT]
> アプリをデプロイ スロットから運用環境にスワップする前に、スロット固有でないすべての設定が、スワップ ターゲットへと反映させたいとおりに構成されていることを確認してください。
> 
> 

1. デプロイ スロットをスワップするには、アプリのコマンド バーか、またはデプロイ スロットのコマンド バーで **[スワップ]** ボタンをクリックします。
   
    ![[スワップ] ボタン][SwapButtonBar]

2. スワップのソースとターゲットが正しく設定されていることを確認してください。 通常、スワップ ターゲットは運用スロットになります。 **[OK]** をクリックして操作を完了します。 操作が完了すると、デプロイ スロットはスワップされています。

    ![完全なスワップ](./media/web-sites-staged-publishing/SwapImmediately.png)

    **プレビューでのスワップ**型のスワップについては、「[プレビューでのスワップ (複数フェーズのスワップ)](#Multi-Phase)」をご覧ください。  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>プレビューでのスワップ (複数フェーズのスワップ)

プレビューでのスワップ (または複数フェーズのスワップ) は、接続文字列などのスロットに固有の構成要素の検証を簡略化します。
ミッション クリティカルなワークロードでは、運用スロットの構成が適用されるときにアプリが期待どおりに動作することを検証します。このような検証はアプリが運用環境にスワップされる*前*に実行する必要があります。 プレビューでのスワップが必要です。

**[プレビューでのスワップ]** オプション (「[デプロイ スロットをスワップする](#Swap)」を参照) を使う場合、App Service は次の処理を行います。

- スロット上の既存のワークロード (運用など) が影響を受けないように、送信先スロットを変更しません。
- スロット固有の接続文字列やアプリの設定など、送信先スロットの構成要素をソース スロットに適用します。
- これらの前述の構成要素を使って、ソース スロットのワーカー プロセスを再起動します。
- スワップが完了したら、事前に準備されたソース スロットを送信先スロットに移動します。 送信先スロットは、手動スワップのようにソース スロットに移動されます。
- スワップをキャンセルした場合、ソース スロットの構成要素をソース スロットに再適用します。

送信先スロットの構成でのアプリの厳密な動作のプレビューを表示できます。 検証が完了したら、別の手順でスワップを完了します。 この手順には、ソース スロットが目的の構成で既に準備され、クライアントでダウンタイムが発生しないという利点もあります。  

複数フェーズのスワップに使用できる Azure PowerShell コマンドレットのサンプルについては、「デプロイ スロット用の Azure PowerShell コマンドレット」をご覧ください。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>自動スワップの構成
自動スワップを使うと、アプリのエンド ユーザーにコールド スタートやダウンタイムを強制することなく、アプリを連続的にデプロイできる、効率的な DevOps シナリオを実現できます。 デプロイ スロットを運用環境に自動スワップするよう構成した場合、そのスロットにコードの更新をプッシュすると、アプリがスロット内で起動されしだい、App Service によって自動的に運用環境へとスワップされます。

> [!IMPORTANT]
> 自動スワップを有効にする場合は、スロット構成がターゲット スロット (通常は運用スロット) に反映させたいとおりの構成になっていることを確認してください。
> 
> 

スロットの自動スワップは簡単に構成できます。 次の手順に従ってください。

1. **[デプロイ スロット]** で、非運用スロットを選び、そのスロットのリソース ブレードで **[アプリケーション設定]** を選択します。  
   
    ![][Autoswap1]
2. **[自動スワップ]** の **[オン]** を選択し、**[自動スワップ スロット]** で目的のターゲット スロットを選択して、コマンド バーの **[保存]** をクリックします。 スロットの構成が、ターゲット スロットに反映させたいとおりの構成になっていることを確認してください。
   
    操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。
   
    ![][Autoswap2]
   
   > [!NOTE]
   > アプリの自動スワップ機能に事前に慣れておきたい場合は、まず **[自動スワップ スロット]** で運用スロット以外のターゲット スロットを選ぶことで、機能をテストすることができます。  
   > 
   > 
3. デプロイ スロットにコードをプッシュします。 しばらくすると自動スワップが実行され、更新がターゲット スロットの URL に反映されます。

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>スワップ後に運用環境のアプリをロールバックするには
スロットのスワップ後に運用サイトでエラーが見つかった場合は、同じ 2 つのスロットをすぐにスワップして、スロットをスワップ前の状態にロールバックすることができます。

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>スワップ前のカスタム ウォームアップ
アプリによっては、カスタム ウォームアップ アクションが必要な場合があります。 web.config の `applicationInitialization` 構成要素を使って、要求の受信前に実行されるカスタムの初期化アクションを指定できます。 スワップ操作は、このカスタム ウォームアップが完了するまで待機します。 サンプルの web.config フラグメントを次に示します。

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>デプロイ スロットを削除するには
デプロイ スロットのブレードで、デプロイ スロットのブレードを開き、**[概要]** (既定のページ) をクリックし、コマンド バーで **[削除]** をクリックします。  

![デプロイ スロットの削除][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>デプロイ スロット用の Azure PowerShell コマンドレット
Azure PowerShell は、Windows PowerShell から Azure を管理するためのコマンドレットを提供するモジュールです (Azure App Service のデプロイ スロットを管理するためのサポートなど)。

* Azure PowerShell のインストールと構成、Azure サブスクリプションを使用した Azure PowerShell の認証については、「 [Microsoft Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。  

- - -
### <a name="create-a-web-app"></a>Web アプリを作成する
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>デプロイ スロットの作成
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-review-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>レビューでのスワップ (複数フェーズのスワップ) を開始し、送信先スロットの構成をソース スロットに適用する
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>保留中のスワップ (レビューでのスワップ) を取り消し、ソースのスロットの構成を復元する
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>デプロイ スロットをスワップする
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>デプロイ スロットを削除する
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>デプロイ スロット用の Azure コマンド ライン インターフェイス (Azure CLI) コマンド
Azure CLI には、App Service デプロイ スロットの管理のサポートなど、Azure を使うためのクロスプラットフォーム コマンドが用意されています。

* Azure サブスクリプションへのAzure CLI の接続方法に関する情報など、Azure CLI のインストールと構成手順については、「 [Azure CLI のインストールと構成](../cli-install-nodejs.md)」をご覧ください。
* Azure CLI で Azure App Service に使用できるコマンドの一覧を表示するには、 `azure site -h`を呼び出してください。

> [!NOTE] 
> デプロイ スロット用の [Azure CLI 2.0](https://github.com/Azure/azure-cli) コマンドについては、「[az appservice web deployment slot](/cli/azure/appservice/web/deployment/slot)」(az appservice Web デプロイ スロット) をご覧ください。

- - -
### <a name="azure-site-list"></a>azure site list
現在のサブスクリプションのアプリに関する情報については、次の例に示すように、**azure site list** を呼び出します。

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>azure site create
デプロイ スロットを作成するには、次の例に示すように、**azure site create** を呼び出し、既存のアプリの名前と作成するスロットの名前を指定します。

`azure site create webappslotstest --slot staging`

新しいスロットのソース管理を有効にするには、次の例に示すように、 **--git** オプションを使用します。

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>azure site swap
更新したデプロイ スロットを運用アプリにするには、次の例に示すように、 **azure site swap** コマンドを使用してスワップ操作を実行します。 運用アプリではダウン タイムは発生せず、コールド スタートが行われることもありません。

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>azure site delete
不要になったデプロイ スロットを削除するには、次の例に示すように、 **azure site delete** コマンドを使用します。

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Web アプリの動作を確認してください。 今すぐ [App Service を試用](https://azure.microsoft.com/try/app-service/)して、有効期間が短いスターター アプリを作成してみてください。このサービスの利用にあたり、クレジット カードや契約は必要ありません。
> 
> 

## <a name="next-steps"></a>次のステップ
[Azure App Service Web App - 運用環境以外のデプロイ スロットへの Web アクセスの禁止](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png


