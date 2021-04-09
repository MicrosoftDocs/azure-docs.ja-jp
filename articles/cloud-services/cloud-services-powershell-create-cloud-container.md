---
title: PowerShell を使用してクラウド サービス (クラシック) コンテナーを作成する | Microsoft Docs
description: この記事では、PowerShell を使用してクラウド サービス コンテナーを作成する方法について説明します。 コンテナーは、Web ロールと worker ロールをホストします。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610418"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Azure PowerShell コマンドを使用して空のクラウド サービス (クラシック) コンテナーを作成する

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

この記事では、Azure PowerShell コマンドレットを使用して Cloud Services コンテナーを簡単に作成する方法について説明します。 次の手順に従ってください。

1. [Azure PowerShell のダウンロード](https://aka.ms/webpi-azps) ページから Microsoft Azure PowerShell コマンドレットをインストールします。
2. PowerShell コマンド プロンプトを開きます。
3. [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) を使用してサインインします。

   > [!NOTE]
   > Azure PowerShell コマンドレットをインストールして、Azure サブスクリプションに接続する手順の詳細については、「 [Azure PowerShell をインストールして構成する方法](/powershell/azure/)」を参照してください。
   >
   >
4. **New-AzureService** コマンドレットを使用し、空の Azure クラウド サービス コンテナーを作成します。

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. この例に従って、コマンドレットを呼び出します。

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Azure クラウド サービスの作成方法については、次のコマンドレットを実行します。

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>次のステップ

* クラウド サービス デプロイの管理については、[Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService)、[Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService)、[Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) の各コマンドの説明を参照してください。 詳細については、 [クラウド サービスを構成する方法](cloud-services-how-to-configure-portal.md) に関するページも参考になります。
* Azure にクラウド サービス プロジェクトを発行する方法については、[アーカイブ済みのクラウド サービス リポジトリ](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)に関するページの **PublishCloudService.ps1** コード サンプルを参照してください。