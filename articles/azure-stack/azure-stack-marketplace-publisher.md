---
title: "Marketplace ツールキットを使用して Marketplace アイテムの作成と発行を行う | Microsoft Docs"
description: "発行ツールキットを使用して Marketplace アイテムをすばやく作成する方法を説明します"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: ByronR
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: helaw
ms.openlocfilehash: 5b2c04d2cbc06e1572dc2e40712f6cf9d886aa1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>発行ツールを使用して Marketplace アイテムを追加する
[Azure Stack Marketplace](azure-stack-marketplace.md) にコンテンツを追加して、オペレーターやテナントがデプロイのためにソリューションを使用できるようにします。  Marketplace ツールキットでは、IaaS の Azure Resource Manager テンプレートまたは VM 拡張機能に基づく Azure Marketplace パッケージ (.azpkg) ファイルを作成します。  Marketplace ツールキットを使用して、このツールまたは[手動](azure-stack-create-and-publish-marketplace-item.md)の手順で作成した .azpkg ファイルを発行することもできます。  このトピックでは、ツールをダウンロードし、VM テンプレートに基づいて Marketplace アイテムを作成してから、Azure Stack Marketplace にそのアイテムを発行する手順を説明します。     


## <a name="prerequisites"></a>前提条件
 - このツールキットは Azure Stack ホストで実行するか、ツールを実行するマシンとの間に [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 接続を用意する必要があります。

 - [Azure Stack クイック スタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip)をダウンロードして抽出します。

 - [Azure ギャラリー パッケージ化ツール](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe) をダウンロードします。 

 - Marketplace への発行にはアイコンとサムネイル ファイルが必要です。  独自のものを使用することも、この例の[サンプル](azure-stack-marketplace-publisher.md#support-files) ファイルをローカルに保存することもできます。

## <a name="download-the-tool"></a>ツールをダウンロードする
Marketplace ツールキットは、[Azure Stack のツール リポジトリからダウンロード](azure-stack-powershell-download.md)できます。


##  <a name="create-marketplace-items"></a>Marketplace アイテムを作成する
このセクションでは、Marketplace ツールキットを使用して、.azpkg 形式で Marketplace アイテムのパッケージを作成します。  

### <a name="provide-marketplace-information-with-wizard"></a>ウィザードで Marketplace の情報を指定する
1. PowerShell セッションから Marketplace ツールキットを実行します。
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. **[Solution]** (ソリューション) タブをクリックします｡この画面では Marketplace アイテムに関する情報を指定できます。 アイテムに関する情報を、Marketplace で表示したいように入力します。  フォームに事前に入力する[パラメーター ファイル](azure-stack-marketplace-publisher.md#use-a-parameters-file)を指定することもできます。  
    
    ![Marketplace ツールキットの最初の画面のスクリーンショット](./media/azure-stack-marketplace-publisher/image7.png)
3. **[参照]** をクリックして、アイコンとスクリーンショットのフィールドごとにイメージ ファイルを選択します。  独自のアイコンか、[サポート ファイル](azure-stack-marketplace-publisher.md#support-files) セクションのサンプル アイコンを使用できます。
4. すべてのフィールドを指定したら、Marketplace 内でソリューションのプレビューを行うために、"プレビュー ソリューション" を選択します。  **[次へ]** をクリックする前に、テキスト、イメージ、およびスクリーンショットの修正や編集が可能です。  

### <a name="import-template-and-create-package"></a>テンプレートをインポートしてパッケージを作成する
このセクションでは、テンプレートをインポートし、ソリューションへの入力処理を行います。

1.  **[参照]** をクリックして、ダウンロードしたテンプレートの 101-Simple-Windows-VM フォルダーから *azuredeploy.json* を選択します。

    ![Marketplace ツールキットの 2 番目の画面のスクリーンショット](./media/azure-stack-marketplace-publisher/image8.png)
2.  デプロイ ウィザードには、テンプレートで指定されている*基本的*手順と各パラメーターの入力項目が格納されます。  他の手順を追加して、手順間で入力を移動することができます。  たとえば、ソリューションに "フロント エンド構成" の手順と "バックエンド構成" の手順を設けたい場合もあります。
3.  AzureGalleryPackager.exe へのパスを指定します。  
4.  **[Create]** (作成) をクリックすると、Marketplace ツールキットによって、ソリューションが .azpkg ファイルにパッケージ化されます。  完了すると、ウィザードにはソリューション ファイルへのパスが表示され、引き続きパッケージの Azure Stack への発行を行うオプションが表示されます。


## <a name="publish-marketplace-items"></a>Marketplace アイテムを発行する
このセクションでは、Azure Stack Marketplace に Marketplace アイテムを発行します。

![Marketplace ツールキットの最初の画面のスクリーンショット](./media/azure-stack-marketplace-publisher/image9.png)

1.  ウィザードにはソリューションを発行するための情報が必要です。
    
    |フィールド|Description|
    |-----|-----|
    | サービス管理者名 | サービス管理者アカウント。  例: ServiceAdmin@mydomain.onmicrosoft.com |
    | パスワード | サービス管理者アカウントのパスワード。 |
    | API エンドポイント | Azure Stack の Azure Resource Manager エンドポイント。  management.local.azurestack.external |
2.  **[Publish]** (発行) をクリックすると、発行ログが表示されます。
3.  これで Azure Stack ポータルを使用して、パブリッシュしたアイテムをデプロイできるようになりました。


## <a name="use-a-parameters-file"></a>パラメーター ファイルを使用する
パラメーター ファイルを使用して Marketplace アイテムの情報を記入するすることもできます。  

Marketplace ツールキットには、独自のパラメーター ファイルを作成するために使用できる *solution.parameters.ps1* が含まれています。


## <a name="support-files"></a>サポート ファイル
| Description | サンプル |
| ----- | ----- |
| 40 x 40 .png アイコン | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90 x 90 .png アイコン | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115 x 115 .png アイコン | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255 x 115 .png アイコン | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 .png サムネイル | ![](./media/azure-stack-marketplace-publisher/image5.png) |


