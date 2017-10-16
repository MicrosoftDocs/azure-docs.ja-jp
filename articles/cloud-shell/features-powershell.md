---
title: "Azure Cloud Shell (プレビュー) での PowerShell の機能 | Microsoft Docs"
description: "Azure Cloud Shell での PowerShell の機能の概要"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.openlocfilehash: 9eacb57b5a00ff11739695ed3311be0c638ba25f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell での PowerShell の機能とツール

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> [Bash](features.md) の機能とツールも利用できます。

Cloud Shell での PowerShell は `Windows Server 2016` で実行します。

## <a name="features"></a>Features (機能)

### <a name="secure-automatic-authentication"></a>自動認証をセキュリティで保護する

Cloud Shell での PowerShell は、Azure PowerShell のアカウント アクセスを安全かつ自動的に認証します。

### <a name="files-persistence-across-sessions"></a>セッション間でのファイルの永続化

セッション間でファイルを保持する場合、Cloud Shell の初回起動時に、Azure ファイル共有のアタッチについてのチュートリアルがあります。
チュートリアルが完了すると、今後のすべてのセッションで、記憶域 (`$home\clouddrive` としてマウントされる) が自動的にアタッチされます。
Cloud Shell に対する各要求は一時的なマシンを割り当てるので、`$home\clouddrive` の外のファイルやマシンの状態は、セッション間で保持されません。

[Azure ファイル共有を Cloud Shell にアタッチする方法の詳細について説明します。](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Azure ドライブ (Azure:)

Cloud Shell の PowerShell は、Azure ドライブ (`Azure:`) で開始します。
Azure ドライブを使うと、ファイル システムのナビゲーションと同じように、Compute、Network、Storage などの Azure リソースを簡単に検出およびナビゲーションできるようになります。
引き続き、使い慣れた [Azure PowerShell コマンドレット](https://docs.microsoft.com/en-us/powershell/azure)を使ってこれらのリソースを管理できます。
Azure リソースに対するすべての変更は、Azure Portal で直接行われたものも、Azure PowerShell コマンドレットを使って行われたものも、Azure ドライブにすぐに反映されます。

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>コンテキストの認識

- **リソース グループのスコープ**: Azure ドライブ (`Azure:`) のリソース グループ パスのコンテキスト内にいるときは、リソース グループ名が自動的に Azure PowerShell コマンドレットに渡されます。

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: このコマンドレットは、Azure ドライブ (`Azure:`) 下の場所のコンテキストで使用できるコマンドの一覧を返します。 たとえば、ユーザーが `Azure:\<subscription name>\StorageAccounts` にいるときは、ストレージ関連のコマンドのみを表示します。

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>リッチな PowerShell スクリプトの編集

VIM を使って PowerShell のファイル (`.ps1,.psm1,.psd1`) を編集すると、構文の強調表示と IntelliSense のサポートが自動的に得られます。
IntelliSense のサポートは、[PowerShell エディター サービス](https://github.com/PowerShell/PowerShellEditorServices)のローカル インスタンスと対話する VIM プラグインを使って実装されています。

> [!TIP]
> `TAB` キーを使って、コマンド名、パラメーター名、およびパラメーター値 (該当する場合) の入力候補 (IntelliSense) を取得します。

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>拡張可能なモデル

[PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget) を使うと、[PowerShell ギャラリー](https://www.powershellgallery.com)からカスタム モジュールとスクリプトを簡単にインストール (および更新) できます。
インストール後、モジュールは自動的に Cloud Shell セッション間で永続化されます。

> [!TIP]
> ユーザーがインストールしたモジュールは、`$Home\CloudDrive\.pscloudshell\WindowsPowerShell` フォルダーに保存されます。 このフォルダーへのシンボリック リンクが、ユーザーのドキュメント フォルダーに作成されます (`$home\Documents\WindowsPowerShell`)。

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>ゲスト VM の管理

2 つの組み込みコマンド `Enter-AzureRmVM` と `Invoke-AzureRmVMCommand` を使って、Azure VM をリモート管理できます。
これらのコマンドは、PowerShell リモート処理の上に構築されており、Azure VM に対する PowerShell 接続を必要とします。

## <a name="tools"></a>ツール

|**カテゴリ**    |**名前**                                 |
|----------------|-----------------------------------------|
|Azure ツール     |[Azure PowerShell (4.3.1)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1) |
|テキスト エディター    |vim<br> nano                             |
|パッケージ マネージャー |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|ソース管理  |git                                      |
|データベース       |[SqlServer モジュール](https://www.powershellgallery.com/packages/SqlServer)<br> [sqlcmd ユーティリティ](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|テスト ツール      |Pester                                   |

## <a name="language-support"></a>言語のサポート

|**言語**|**バージョン**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 および [6.0 (ベータ)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>次のステップ

[Cloud Shell での PowerShell のクイックスタート](quickstart-powershell.md) <br>
[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/)
