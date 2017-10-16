---
title: "GitHub からの Azure Stack ツールのダウンロード | Microsoft Docs"
description: "Azure Stack の操作に必要なツールをダウンロードする方法について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 20cac6eace89d86f8fa6d7640357e19c8d7359b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>GitHub からの Azure Stack ツールのダウンロード

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

AzureStack-Tools は、リソースの管理と Azure Stack へのデプロイに使用できる PowerShell モジュールをホストする GitHub レポジトリです。 これらの PowerShell モジュールは、Azure Stack Development Kit にダウンロードするか、または VPN 接続を確立する予定の場合は Windows ベースの外部クライアントにダウンロードして使用できます。 これらのツールを取得するには、GitHub レポジトリを複製するか、AzureStack-Tools フォルダーをダウンロードします。 

レポジトリを複製するには、Windows 用の [Git](https://git-scm.com/download/win) をダウンロードし、コマンド プロンプト ウィンドウを開いて、次のスクリプトを実行します。

```PowerShell
# Change directory to the root directory 
cd \

# clone the repository
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory
cd AzureStack-Tools
```

ツールのフォルダーをダウンロードするには、次のスクリプトを実行します。

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>モジュールで提供される機能

AzureStack-Tools レポジトリには、Azure Stack の次の機能をサポートする PowerShell モジュールが含まれています。  

| 機能 | Description | このモジュールを使用できるユーザー |
| --- | --- | --- |
| [クラウド機能](user/azure-stack-validate-templates.md) | このモジュールを使用すると、クラウドのクラウド機能を取得できます。 たとえば、このモジュールを使用して、Azure Stack および Azure クラウドで、API バージョン、Azure Resource Manager リソース、VM 拡張機能などのクラウド機能を取得できます。 | クラウド オペレーターとユーザー。 |
| [Azure Stack コンピューティングの管理](azure-stack-add-vm-image.md) | このモジュールを使用すると、Azure Stack マーケットプレースに VM イメージを追加したり削除したりできます。 | クラウド オペレーター。 |
| [Azure Stack インフラストラクチャの管理](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | このモジュールを使用すると、Azure Stack インフラストラクチャの VM、アラート、更新などを管理できます。 |  クラウド オペレーター。|
| [Azure Stack の Resource Manager ポリシー](user/azure-stack-policy-module.md) | このモジュールを使用すると、Azure Stack と同じバージョン管理とサービス可用性で、Azure サブスクリプションまたは Azure リソース グループを構成できます。 | クラウド オペレーターとユーザー |
| [Azure への登録](azure-stack-register.md) | このモジュールを使用すると、開発キットのインスタンスを Azure に登録できます。 登録後、Azure からマーケットプレースの項目をダウンロードして Azure Stack で使用できます。 | クラウド オペレーター |
| [Azure Stack のデプロイ](azure-stack-run-powershell-script.md) | このモジュールを使用すると、Azure Stack Virtual Hard Disk(VHD) イメージを使用して、Azure Stack のホスト コンピューターでデプロイおよび再デプロイを行う準備ができます。 | クラウド オペレーター。 |
| [Azure Stack への接続](azure-stack-connect-powershell.md) | このモジュールを使用すると、PowerShell で Azure Stack インスタンスに接続し、Azure Stack への VPN 接続を構成できます。 | クラウド オペレーターとユーザー |
| [Azure Stack サービスの管理](azure-stack-create-offer.md) | Azure Stack 管理者は、このモジュールを使用して、コンピューティング、ストレージ、ネットワーク、Key Vault の各サービスにわたって無制限のクォータを含む既定のテナント オファーを作成できます。   | クラウド オペレーター。|
| [テンプレートの検証](user/azure-stack-validate-templates.md) | このモジュールを使用すると、既存のまたは新しいテンプレートを Azure Stack にデプロイできるかどうか確認できます。 | クラウド オペレーターとユーザー |


## <a name="next-steps"></a>次のステップ
* [Azure Stack ユーザーの PowerShell 環境の構成](user/azure-stack-powershell-configure-user.md)   
* [VPN 経由での Azure Stack Development Kit への接続](azure-stack-connect-azure-stack.md)  
