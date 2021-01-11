---
title: Azure Cloud Shell の機能 | Microsoft Docs
description: Azure Cloud Shell の機能の概要
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: ea11dddd83651e233f008197ffa23a3becd978de
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642352"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell の機能とツール

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell は `Ubuntu 16.04 LTS` 上で実行されます。

## <a name="features"></a>特徴

### <a name="secure-automatic-authentication"></a>セキュリティで保護された自動認証

Cloud Shell は、Azure CLI と Azure PowerShell のアカウント アクセスを安全かつ自動的に認証します。

### <a name="home-persistence-across-sessions"></a>セッション間での $HOME の永続化

セッション間でファイルを保持する場合、Cloud Shell の初回起動時に、Azure ファイル共有のアタッチについてのチュートリアルがあります。
完了すると、今後すべてのセッションで、ストレージが自動的にアタッチされます (`$HOME\clouddrive` としてマウントされます) 。
さらに、`$HOME` ディレクトリが .img として Azure ファイル共有に永続化されます。
`$HOME` およびマシン状態の外部にあるファイルは、セッション間で保持されません。 SSH キーなどのシークレットを格納するときは、ベスト プラクティスを使用します。 [Azure Key Vault などのサービスには、設定用のチュートリアルが用意されています](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites)。

[Cloud Shell でのファイルの永続化については、こちらを参照してください。](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure ドライブ (Azure:)

Cloud Shell の PowerShell には、Azure ドライブ (`Azure:`) が備わっています。 「`cd Azure:`」で Azure ドライブに切り替えたり、「`cd  ~`」でホーム ディレクトリに戻ったりすることができます。
Azure ドライブを使用すると、ファイル システムのナビゲーションと同じように、Compute、Network、Storage などの Azure リソースを簡単に検出およびナビゲーションできるようになります。
使用しているドライブに関係なく、引き続き使い慣れた [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure)を使用してこれらのリソースを管理できます。
Azure リソースに対するすべての変更は、Azure Portal で直接行われたものも、Azure PowerShell コマンドレット経由で行われたものも、Azure ドライブに反映されます。  `dir -Force` を実行してリソースを最新の情報に更新できます。

![初期化中の Azure Cloud Shell とディレクトリ リソースの一覧のスクリーンショット。](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Manage Exchange Online

Cloud Shell の PowerShell には、Exchange Online モジュールのプライベート ビルドが含まれています。  Exchange コマンドレットを取得するには、`Connect-EXOPSSession`を実行します。

![Connect-EXOPSSession コマンドと Get-User コマンドを実行している Azure Cloud Shell のスクリーンショット。](media/features-powershell/exchangeonline.png)

 `Get-Command -Module tmp_*` を実行します。
> [!NOTE]
> 同じプレフィックスを持つモジュールをインストールした場合、モジュール名の先頭には`tmp_`が付き、そのコマンドレットも表面化します。 

![Get-Command -Module tmp_* コマンドを実行している Azure Cloud Shell のスクリーンショット。](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>オープンソース ツールとの緊密な統合

Cloud Shell には、Terraform、Ansible、Chef InSpec などのオープンソース ツールのための事前に構成された認証が含まれています。 チュートリアルの例からそれを試してみてください。

## <a name="tools"></a>ツール

|カテゴリ   |名前   |
|---|---|
|Linux ツール            |Bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure ツール            |[Azure CLI](https://github.com/Azure/azure-cli) と [Azure クラシック CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|テキスト エディター           |コード (Cloud Shell エディター)<br> vim<br> nano<br> emacs    |
|ソース管理         |git                    |
|ビルド ツール            |make<br> maven<br> npm<br> pip         |
|Containers             |[Docker マシン](https://github.com/docker/machine)<br> [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|データベース              |MySQL クライアント<br> PostgreSql クライアント<br> [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|その他                  |iPython クライアント<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Puppet Bolt](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)<br> [Office 365 CLI](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>言語のサポート

|Language   |Version   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 および 3.5 (既定)|

## <a name="next-steps"></a>次のステップ
[Cloud Shell の Bash のクイックスタート](quickstart.md) <br>
[Cloud Shell の PowerShell のクイック スタート](quickstart-powershell.md) <br>
[Azure CLI について](https://docs.microsoft.com/cli/azure/) <br>
[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/) <br>
