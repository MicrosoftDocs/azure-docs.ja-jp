---
title: Azure Marketplace に出品する仮想マシンの技術資産を作成する| Microsoft Docs
description: Azure Marketplace に出品する仮想マシンの技術資産を作成する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 6f1a93c3d3059e612d8c309b263e263dbb84c67f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050103"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>出品する仮想マシンの技術資産を作成する

このセクションでは､Azure Marketplace に出品する仮想マシン (VM) の技術資産を作成､構成する手順を説明します｡  1 つの VM は､ソリューション用の仮想ハード ディスク (VHD) と関連するデータ ディスク オプションの 2 つのコンポーネントで構成されてます｡  

- *仮想ハード ディスク (VHD)* - オペレーティング システムとソリューションで構成されるディスクで､Azure Marketplace に出品する機能を使ってデプロイします｡ VHD の準備プロセスは､その仮想マシンが Linux､Windows､またはカスタム ベースであるかによって異なります｡
- *データ ディスク* - 仮想マシンに対する専用の永続的なストレージです｡ 永続的な情報の格納にソリューション用 VHD (`C:` ドライブなど) を*使用してはいけません*｡

VM イメージには、1 個のオペレーティング システム ディスクと 0 個以上のデータ ディスクが含まれます。 ディスクごとに 1 つの VHD が必要です。 空のデータ ディスクにも VHD を作成する必要があります。
接続したデータ ディスク （最大 15 のディスク） を開くには､VM の OS、サイズ､ポートを構成する必要があります｡

> [!TIP] 
> どのオペレーティング システムを使っているかにかかわらず、SKU に必要な最小数のデータ ディスクのみを追加します。 顧客はデプロイ時にイメージの一部であるディスクを削除できませんが、デプロイ中またはデプロイ後にいつでもディスクを追加できます。 

> [!IMPORTANT]
> *新しいイメージ バージョンでディスク数を変更しないでください。*  イメージでデータ ディスクを再構成する必要がある場合は、新しい SKU を定義します。 異なるディスク数で新しいバージョンのイメージを発行すると、自動スケール、ARM テンプレートを使用したソリューションの自動デプロイなどのシナリオで、その新しいバージョンに基づく新しいデプロイが破損する可能性があります。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です｡ エンジニアリング チームには､ソリューションのドメインばかりでなく､Microsoft の次の技術に関する知識も必要です｡ 
-   [Azure Services](https://azure.microsoft.com/services/) に関する基本知識 
-   [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
-   [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/) と [Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
-   [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
-   [JSON](https://www.json.org/) に関する実用的な知識


## <a name="suggested-tools"></a>推奨ツール 

VHD と VM の管理に役立つ次のスクリプト環境のいずれか一方､または両方を利用することを推奨します｡
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

また､開発環境には次にツールを加えることを推奨します｡ 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   拡張機能: [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

また､[Azure Developer Tools](https://azure.microsoft.com/tools/) ページの記載されている利用可能なツールもご覧になることをお勧めします｡Visual Studio を使用する場合は､[Visual Studio Marketplace](https://marketplace.visualstudio.com/) もご覧ください｡


## <a name="next-steps"></a>次の手順

このセクションに以降の記事では､これらの VM 資産を作成して登録する手順を説明します｡

1. ｢[Azure と互換性のある仮想ハード ディスクを作成する｣](./cpp-create-vhd.md) では､Azure と互換性のある Linux または Windows ベースの VHD の作成方法を説明しています｡  サイズや修正プログラムの適用､VM のアップロードの準備などのベスト プラクティスが記載されています｡

2. ｢[仮想マシンに接続する](./cpp-connect-vm.md)｣では､新しく作成した仮想マシンに遠隔から接続して､サインインする方法を説明しています｡  この記事ではまた､使用コストを節約するために VM を停止する方法についても説明しています｡

3. ｢[仮想マシンを構成する](./cpp-configure-vm.md)｣では､適切な VHD サイズを選択する方法と､イメージを一般化する方法､最新の更新プログラム (修正プログラム) を適用する方法､ カスタム構成をスケジュールする方法を説明しています｡

4. ｢[仮想ハード ディスクから仮想マシンをデプロイする](./cpp-deploy-vm-vhd.md)｣ では､Azure にデプロイされた VHD から VM を登録する方法を説明しています。  必要なツールを示し、それらツールを使ってユーザー VM イメージを作成し､[Microsoft Azure portal](https://ms.portal.azure.com/) または PowerShell スクリプトのいずれかを使って Azure にデプロイする方法を説明しています｡ 

5. ｢[仮想マシンのイメージを認定する](./cpp-certify-vm.md)｣では､VM イメージをテストし送信して､Azure Marketplace の認定を受ける方法を説明しています｡ *Certification Test Tool for Azure Certified* ツールの入手場所と､このツールを利用して VM を認定する方法を説明しています｡ 

6. ｢[SAS の URI を取得する](./cpp-get-sas-uri.md)｣では､VM のイメージに対するShared Access Signature (SAS) の URI を取得する方法を説明しています｡
 
関連する記事として [Shared Access Signature の URL のよくある問題](./cpp-common-sas-url-issues.md)｣では､SAS URI とそれに対応するソリューションを使用したときによくある問題を示しています｡

これらのすべての手順を完了すると､Azure Marketplace に [VM を出品する](./cpp-publish-offer.md)準備が完了したことになります｡
