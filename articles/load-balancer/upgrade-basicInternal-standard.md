---
title: Basic Internal から Standard Internal へのアップグレード - Azure Load Balancer
description: この記事では、Basic SKU から Standard SKU に Azure Internal Load Balancer をアップグレードする方法について説明します
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 1b7bdbdb9e1d642f2ef4a715d4993e4f449ccd0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050699"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure Internal Load Balancer のアップグレード - 送信接続は不要
[Azure Standard Load Balancer](load-balancer-overview.md) では、豊富な機能とゾーンの冗長性による高可用性が提供されます。 Load Balancer SKU の詳細については、[比較表](./skus.md#skus)を参照してください。

この記事では、Basic Load Balancer と同じ構成で Standard Load Balancer を作成し、Basic Load Balancer から Standard Load Balancer にトラフィックを移行する PowerShell スクリプトについて説明します。

## <a name="upgrade-overview"></a>アップグレードの概要

次の処理を行う Azure PowerShell スクリプトが用意されています。

* 指定した場所に Standard Internal SKU Load Balancer を作成します。 Standard Internal Load Balancer によって[送信接続](./load-balancer-outbound-connections.md)が提供されないことに注意してください。
* 新しく作成した Standard Load Balancer に Basic SKU Load Balancer の構成をシームレスにコピーします。
* 新しく作成した Standard Load Balancer に Basic Load Balancer からプライベート IP をシームレスに移動します。
* Standard Load Balancer のバックエンド プールに Basic Load Balancer のバックエンド プールから VM をシームレスに移動します。

### <a name="caveatslimitations"></a>注意事項と制限事項

* スクリプトでは、送信接続の必要がない Internal Load Balancer のアップグレードのみをサポートしています。 一部の VM に[送信接続](./load-balancer-outbound-connections.md)が必要な場合は、この[ページ](upgrade-InternalBasic-To-PublicStandard.md)の説明をご覧ください。 
* Basic Load Balancer は、バックエンド VM および NIC と同じリソース グループに存在する必要があります。
* Standard Load Balancer が別のリージョンに作成されている場合、以前のリージョンに存在する VM を新しく作成した Standard Load Balancer に関連付けることはできません。 この制限を回避するには、必ず新しいリージョンに新しい VM を作成してください。
* Load Balancer にフロントエンド IP 構成またはバックエンド プールがない場合は、スクリプトの実行中にエラーが発生する可能性があります。 それらが空でないことを確認してください。

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>フロントエンド IP 構成の IP 割り当て方法を静的に変更します (既に静的である場合は、この手順を無視します)

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧でお使いの Basic Load Balancer を選択します。

2. **[設定]** で **[フロントエンド IP 構成]** を選択し、1 つ目のフロントエンド IP 構成を選択します。 

3. **[割り当て]** で **[静的]** を選択します

4. Basic Load Balancer のすべてのフロントエンド IP 構成について、ステップ 3 を繰り返します。


## <a name="download-the-script"></a>スクリプトのダウンロード

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0)から移行スクリプトをダウンロードします。
## <a name="use-the-script"></a>スクリプトの使用

ローカルの PowerShell 環境のセットアップと設定に応じて、次の 2 つのオプションがあります。

* Azure Az モジュールがインストールされていない場合、または Azure Az モジュールをアンインストールしてもかまわない場合、最善の方法は `Install-Script` オプションを使用してスクリプトを実行することです。
* Azure Az モジュールを保持する必要がある場合は、スクリプトをダウンロードして直接実行するのが最善の方法です。

Azure Az モジュールがインストールされているかどうかを確認するには、`Get-InstalledModule -Name az` を実行します。 インストールされている Az モジュールが見つからなかった場合は、`Install-Script` メソッドを使用できます。

### <a name="install-using-the-install-script-method"></a>Install-Script メソッドを使用してインストールする

このオプションを使用するには、コンピューターに Azure Az モジュールがインストールされていないことが必要です。 インストールされている場合、次のコマンドにはエラーが表示されます。 Azure Az モジュールをアンインストールするか、もう 1 つのオプションであるスクリプトを手動でダウンロードして実行する方法を使用します。
  
次のコマンドを使用してこのスクリプトを実行します。

`Install-Script -Name AzureILBUpgrade`

このコマンドでは、必要な Az モジュールもインストールされます。  

### <a name="install-using-the-script-directly"></a>スクリプトを使用して直接インストールする

Azure Az モジュールがインストールされていて、それらをアンインストールできない (またはそれらをアンインストールしたくない) 場合は、スクリプトのダウンロード リンクの **[Manual Download]\(手動ダウンロード\)** タブを使用して、手動でスクリプトをダウンロードすることができます。 スクリプトは、生の nupkg ファイルとしてダウンロードされます。 この nupkg ファイルからスクリプトをインストールするには、「[パッケージの手動ダウンロード](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)」を参照してください。

スクリプトを実行するには、次の手順を実行します。

1. `Connect-AzAccount` を使用して Azure に接続します。

1. `Import-Module Az` を使用して Az モジュールをインポートします。

1. 必要なパラメーターを確認します。

   * **rgName: [文字列]:必須** – 既存の Basic Load Balancer と新しい Standard Load Balancer のリソース グループです。 この文字列値を検索するには、Azure portal に移動し、Basic Load Balancer ソースを選択して、ロードバランサーの **[概要]** をクリックします。 そのページにリソース グループがあります。
   * **oldLBName: [文字列]:必須** – アップグレードする既存の Basic Balancer の名前です。 
   * **newlocation: [文字列]:必須** – Standard Load Balancer が作成される場所です。 他の既存のリソースとの関連付けを強化するために、選択した Basic Load Balancer の同じ場所を Standard Load Balancer に継承することをお勧めします。
   * **newLBName: [文字列]:必須** – 作成される Standard Load Balancer の名前です。
1. 適切なパラメーターを使用してスクリプトを実行します。 完了するまで 5 から 7 分かかることがあります。

    **例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg&quot; -oldLBName &quot;LBForInternal&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

## <a name="common-questions"></a>一般的な質問

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell スクリプトで v1 から v2 に構成を移行するにあたり、何か制限事項はありますか?

はい。 「[注意事項と制限事項](#caveatslimitations)」をご覧ください。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell スクリプトでは、Basic Load Balancer から新しく作成した Standard Load Balancer にトラフィックを切り替えることもできますか?

はい、トラフィックは移行されます。 トラフィックを個人的に移行する場合は、VM を移動しない[このスクリプト](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)を使用します。

## <a name="next-steps"></a>次のステップ

[Standard Load Balancer の詳細を確認する](load-balancer-overview.md)
