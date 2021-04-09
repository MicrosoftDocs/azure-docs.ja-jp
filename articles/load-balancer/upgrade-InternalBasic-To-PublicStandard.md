---
title: Basic Internal から Standard Public へのアップグレード - Azure Load Balancer
description: このアーティクルでは、Azure Basic Internal Load Balancer を Standard Public Load Balancer にアップグレードする方法について説明します
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 3394754f2829018f7862b3775f8ab2cb2d07d005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051362"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Azure Internal Load Balancer のアップグレード - 送信接続が必要
[Azure Standard Load Balancer](load-balancer-overview.md) では、豊富な機能とゾーンの冗長性による高可用性が提供されます。 Load Balancer SKU の詳細については、[比較表](./skus.md#skus)を参照してください。 Standard Internal Load Balancer は送信接続を提供しないため、代わりに標準のPublic Load Balancer を作成するためのソリューションを提供します。

アップグレードには、次の 4 つの段階があります。

1. 構成を Standard Public Load Balancer に移行する
2. Standard Public Load Balancer のバックエンド プールに VM を追加する
3. インターネットとの間でリフレインされる必要があるサブネット/VM の NSG ルールを設定する

この記事では、構成の移行について説明します。 バックエンド プールへの VM の追加は、お客様固有の環境によって異なる場合があります。 ただし、一般的な推奨事項を簡単に[説明しています](#add-vms-to-backend-pools-of-standard-load-balancer)。

## <a name="upgrade-overview"></a>アップグレードの概要

次の処理を行う Azure PowerShell スクリプトが用意されています。

* 指定したリソース グループと場所に Standard SKU Public Load Balancer を作成します。
* Basic SKU Internal Load Balancer の構成を、新しく作成された Standard Public Load Balancer にシームレスにコピーします。
* エグレス接続を有効にするアウトバウンド規則を作成します。

### <a name="caveatslimitations"></a>注意事項と制限事項

* スクリプトは、送信接続が必要な Internal Load Balancer のアップグレードをサポートしています。 VM のいずれにもアウトバウンド接続が必要ない場合、ベストプラクティスについては [このページ](upgrade-basicInternal-standard.md) をご参照ください。
* Standard Load Balancer に新しいパブリック アドレスがあります。 既存の Basic Internal Load Balancer に関連付けられている IP アドレスを Standard Public Load Balancer にシームレスに移動することはできません。それぞれの SKU が異なるためです。
* Standard Load Balancer が別のリージョンに作成されている場合、以前のリージョンに存在する VM を新しく作成した Standard Load Balancer に関連付けることはできません。 この制限を回避するには、必ず新しいリージョンに新しい VM を作成してください。
* Load Balancer にフロントエンド IP 構成またはバックエンド プールがない場合は、スクリプトの実行中にエラーが発生する可能性があります。  それらが空でないことを確認してください。

## <a name="download-the-script"></a>スクリプトのダウンロード

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureLBUpgrade/2.0)から移行スクリプトをダウンロードします。
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
   * **newrgName: [文字列]:必須** – Standard Load Balancer が作成されるリソース グループです。 新しいリソース グループまたは既存のものを指定できます。 既存のリソース グループを選択する場合は、Load Balancer の名前がリソース グループ内で一意でなければならないことに注意してください。 
   * **newlocation: [文字列]:必須** – Standard Load Balancer が作成される場所です。 他の既存のリソースとの関連付けを強化するために、選択したBasic Load Balancer の同じ場所を Standard Load Balancer に継承することをお勧めします。
   * **newLBName: [文字列]:必須** – 作成される Standard Load Balancer の名前です。
1. 適切なパラメーターを使用してスクリプトを実行します。 完了するまで 5 から 7 分かかることがあります。

    **例**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg&quot; -oldLBName &quot;LBForPublic&quot; -newrgName &quot;test_userInput3_rg&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Standard Load Balancer のバックエンド プールに VM を追加する

まず、スクリプトによって、Basic Public Load Balancer から移行された正確な構成で新しい Standard Public Load Balancer が正常に作成されたことを再確認します。 この確認は Azure portal から行うことができます。

手動テストとして、必ず少量のトラフィックを Standard Load Balancer 経由で送信してください。
  
新しく作成された Standard Public Load Balancer のバックエンド プールに VM を追加する方法に関するいくつかのシナリオと、それぞれについての推奨事項を次に示します。

* **以前の Basic Public Load Balancer のバックエンド プールから、新しく作成された Standard Public Load Balancer のバックエンド プールに既存の VM を移動する。**
    1. このクイック スタートのタスクを実行するには、[Azure portal](https://portal.azure.com) にサインインする必要があります。
 
    1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **新しく作成された Standard Load Balancer** を選択します。
   
    1. **[設定]** で、 **[バックエンド プール]** を選択します。
   
    1. Basic Load Balancer のバックエンド プールと一致するバックエンド プールを選択し、次の値を選択します。 
      - **仮想マシン**: ドロップダウンして、Basic Load Balancer の一致するバックエンド プールから VM を選択します。
    1. **[保存]** を選択します。
    >[!NOTE]
    >パブリック IP を持つ VM の場合は、同じ IP アドレスが保証されていない標準 IP アドレスを最初に作成する必要があります。 基本 IP から VM の関連付けを解除し、新しく作成した標準 IP アドレスに関連付けます。 次に、手順に従って、Standard Load Balancer のバックエンド プールに VM を追加できます。 

* **新しく作成された Standard Public Load Balancer のバックエンド プールに追加する新しい VM を作成する。**
    * VM を作成して Standard Load Balancer に関連付ける方法の詳細については、[こちら](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)参照してください。

### <a name="create-an-outbound-rule-for-outbound-connection"></a>送信接続用のアウトバウンド規則を作成する

[手順](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration)に従ってアウトバウンド規則を作成します。それにより、次のことができます。
* 送信 NAT をゼロから定義する。
* 既存の送信 NAT の動作をスケーリングして調整する。

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>インターネットとの通信を避ける VM の NSG ルールを作成する
インターネット トラフィックが VM に到達しないようにするには、VM のネットワーク インターフェイスに [NSG ルール](../virtual-network/manage-network-security-group.md) を作成します。

## <a name="common-questions"></a>一般的な質問

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell スクリプトで v1 から v2 に構成を移行するにあたり、何か制限事項はありますか?

はい。 「[注意事項と制限事項](#caveatslimitations)」をご覧ください。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell スクリプトでは、Basic Load Balancer から新しく作成した Standard Load Balancer にトラフィックを切り替えることもできますか?

いいえ。 Azure PowerShell スクリプトで移行されるのは構成のみです。 実際のトラフィックの移行は、お客様ご自身の責任において行っていただく必要があります。

## <a name="next-steps"></a>次のステップ

[Standard Load Balancer の詳細を確認する](load-balancer-overview.md)
