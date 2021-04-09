---
title: Azure Lab Services 用の PowerShell モジュール | Microsoft Docs
description: この記事では、Azure Lab Services でのアーティファクトの管理に役立つ PowerShell モジュールについて説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646527"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell モジュール (プレビュー)
Az.LabServices は、Azure Lab Services の管理を簡略化する PowerShell モジュールです。 ラボ アカウント、ラボ、VM、イメージの作成、クエリ、更新、削除を行う構成可能な機能が用意されています。 このモジュールについて詳しくは、[GitHub の Az.LabServices ホーム ページ](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)をご覧ください。

> [!NOTE]
> このモジュールは **プレビュー** 段階にあります。 

## <a name="example-command"></a>コマンドの例
PowerShell コマンドを使って、すべてのラボで実行中のすべての VM を停止する例を次に示します。

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>はじめに
1. コンピューターに [Azure PowerShell](/powershell/azure/) が存在しない場合は、インストールします。 
2. [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) をお使いのコンピューターにダウンロードします。
3. モジュールをインポートします。

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. サブスクリプションに含まれるすべてのラボの一覧を表示するには、次のコマンドを実行します。

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>次のステップ
[GitHub の Az.LabServices ホーム ページ](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)を参照してください。