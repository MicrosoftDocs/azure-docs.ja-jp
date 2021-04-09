---
title: Basic Public から Standard Public へのアップグレード - Azure Load Balancer
description: この記事では、Basic SKU から Standard SKU に Azure Public Load Balancer をアップグレードする方法について説明します
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 125d4a02d06e2792f9a2a4e646c3788dcf223318
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612832"
---
# <a name="upgrade-azure-public-load-balancer"></a>Azure Public Load Balancer をアップグレードする
[Azure Standard Load Balancer](load-balancer-overview.md) では、豊富な機能とゾーンの冗長性による高可用性が提供されます。 Load Balancer SKU の詳細については、[比較表](./skus.md#skus)を参照してください。

アップグレードには、次の 2 つの段階があります。

1. IP の割り当て方法を動的から静的に変更します。
2. PowerShell スクリプトを実行して、アップグレードとトラフィックの移行を完了します。

> [!IMPORTANT]
> スクリプトは現在メンテナンス中です。 Basic SKU と Standard SKU からパブリック IP アドレスをアップグレードする方法については、[こちら](../virtual-network/virtual-network-public-ip-address-upgrade.md)を参照してください。

## <a name="upgrade-overview"></a>アップグレードの概要

次の処理を行う Azure PowerShell スクリプトが用意されています。

* Basic Standard Load Balancer の同じリソース グループ内で指定した場所に Standard SKU ロード バランサーを作成します。
* パブリック IP アドレスを Basic SKU から Standard SKU にインプレース アップグレードします。
* 新しく作成した Standard SKU ロード バランサーに Basic SKU ロード バランザーの構成をシームレスにコピーします。
* 送信接続を有効にする既定のアウトバウンド規則を作成します。

### <a name="caveatslimitations"></a>注意事項と制限事項

* スクリプトは Public Load Balancer のアップグレードのみをサポートします。 内部 Basic Load Balancer のアップグレードの手順については、[こちらのページ](./upgrade-basicinternal-standard.md)を参照してください。
* スクリプトを実行する前に、パブリック IP アドレスの割り当て方法を "静的" に変更する必要があります。 
* Load Balancer にフロントエンド IP 構成またはバックエンド プールがない場合は、スクリプトの実行中にエラーが発生する可能性があります。 それらが空でないことを確認してください。

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>パブリック IP アドレスの割り当て方法を静的に変更する

* \* * 推奨される手順をこちらに示します。

    1. このクイック スタートのタスクを実行するには、[Azure portal](https://portal.azure.com) にサインインする必要があります。
 
    1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[Basic Public IP Address associated with Basic Load Balancer]\(Basic Load Balancer に関連付けられた Basic パブリック IP アドレス\)** を選択します。
   
    1. **[設定]** で **[構成]** を選択します。
   
    1. **[割り当て]** で **[静的]** を選択します。
    1. **[保存]** を選択します。
    >[!NOTE]
    >パブリック IP を持つ VM の場合は、同じ IP アドレスが保証されていない標準 IP アドレスを最初に作成する必要があります。 基本 IP から VM の関連付けを解除し、新しく作成した標準 IP アドレスに関連付けます。 次に、手順に従って、Standard Load Balancer のバックエンド プールに VM を追加できます。 

* **新しく作成された Standard Public Load Balancer のバックエンド プールに追加する新しい VM を作成する。**
    * VM を作成して Standard Load Balancer に関連付ける方法の詳細については、[こちら](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)参照してください。


## <a name="download-the-script"></a>スクリプトのダウンロード

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0)から移行スクリプトをダウンロードします。
## <a name="use-the-script"></a>スクリプトの使用

ローカルの PowerShell 環境のセットアップと設定に応じて、次の 2 つのオプションがあります。

* Azure Az モジュールがインストールされていない場合、または Azure Az モジュールをアンインストールしてもかまわない場合、最善の方法は `Install-Script` オプションを使用してスクリプトを実行することです。
* Azure Az モジュールを保持する必要がある場合は、スクリプトをダウンロードして直接実行するのが最善の方法です。

Azure Az モジュールがインストールされているかどうかを確認するには、`Get-InstalledModule -Name az` を実行します。 インストールされている Az モジュールが見つからなかった場合は、`Install-Script` メソッドを使用できます。

### <a name="install-using-the-install-script-method"></a>Install-Script メソッドを使用してインストールする

このオプションを使用するには、コンピューターに Azure Az モジュールがインストールされていないことが必要です。 インストールされている場合、次のコマンドにはエラーが表示されます。 Azure Az モジュールをアンインストールするか、もう 1 つのオプションであるスクリプトを手動でダウンロードして実行する方法を使用します。
  
次のコマンドを使用してこのスクリプトを実行します。

`Install-Script -Name AzurePublicLBUpgrade`

このコマンドでは、必要な Az モジュールもインストールされます。  

### <a name="install-using-the-script-directly"></a>スクリプトを使用して直接インストールする

Azure Az モジュールがインストールされていて、それらをアンインストールできない (またはそれらをアンインストールしたくない) 場合は、スクリプトのダウンロード リンクの **[Manual Download]\(手動ダウンロード\)** タブを使用して、手動でスクリプトをダウンロードすることができます。 スクリプトは、生の nupkg ファイルとしてダウンロードされます。 この nupkg ファイルからスクリプトをインストールするには、「[パッケージの手動ダウンロード](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)」を参照してください。

スクリプトを実行するには、次の手順を実行します。

1. `Connect-AzAccount` を使用して Azure に接続します。

1. `Import-Module Az` を使用して Az モジュールをインポートします。

1. 必要なパラメーターを確認します。

   * **oldRgName: [文字列]:必須** – アップグレードする既存の Basic Load Balancer のリソース グループです。 この文字列値を検索するには、Azure portal に移動し、Basic Load Balancer ソースを選択して、ロードバランサーの **[概要]** をクリックします。 そのページにリソース グループがあります。
   * **oldLBName: [文字列]:必須** – アップグレードする既存の Basic Balancer の名前です。 
   * **newLBName: [文字列]:必須** – 作成される Standard Load Balancer の名前です。
1. 適切なパラメーターを使用してスクリプトを実行します。 完了するまで 5 から 7 分かかることがあります。

    **例**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>送信接続用のアウトバウンド規則を作成する

[手順](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration)に従ってアウトバウンド規則を作成します。それにより、次のことができます。
* 送信 NAT をゼロから定義する。
* 既存の送信 NAT の動作をスケーリングして調整する。

## <a name="common-questions"></a>一般的な質問

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell スクリプトで v1 から v2 に構成を移行するにあたり、何か制限事項はありますか?

はい。 「[注意事項と制限事項](#caveatslimitations)」をご覧ください。

### <a name="how-long-does-the-upgrade-take"></a>アップグレードにはどのくらいの時間がかかりますか。

通常、スクリプトが完了するまでに約 5 分から 10 分かかります。Load Balancer 構成の複雑さによってはさらに時間がかかることがあります。 そのため、ダウンタイムを考慮し、必要に応じてフェールオーバーを計画してください。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell スクリプトでは、Basic Load Balancer から新しく作成した Standard Load Balancer にトラフィックを切り替えることもできますか?

はい。 Azure PowerShell スクリプトでは、パブリック IP アドレスをアップグレードし、構成を Basic から Standard Load Balancer にコピーするだけでなく、新しく作成された Standard Public Load Balancer の背後に VM を移行することもできます。 

## <a name="next-steps"></a>次のステップ

[Standard Load Balancer の詳細を確認する](load-balancer-overview.md)