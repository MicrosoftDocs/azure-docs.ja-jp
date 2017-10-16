---
title: "Azure Cloud Shell (プレビュー) のトラブルシューティング | Microsoft Docs"
description: "Azure Cloud Shell のトラブルシューティング"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 32d4d4d10e5d8986e2dfe94430f52db8f038e245
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshooting-azure-cloud-shell"></a>Azure Cloud Shell のトラブルシューティング
Azure Cloud Shell での問題に対する既知の解決策は以下のとおりです。

## <a name="error-400-disallowedoperation"></a>エラー: 400 DisallowedOperation
 - **詳細**: Azure Active Directory サブスクリプションを使うと、ストレージを作成できません。
 - **解決策**: ストレージ リソースを作成できる Azure サブスクリプションを使ってください。 Azure AD サブスクリプションでは、Azure のリソースを作成できません。

## <a name="powershell-resolutions"></a>PowerShell の解決策

### <a name="no-home-directory-persistence"></a>$Home ディレクトリが永続化されない
  - **詳細**: $Home にデータを書き込むアプリケーション (git、vim など) は、PowerShell のセッション間で保持されません。
  - **解決策**: PowerShell プロファイルで、`clouddrive` 内のアプリケーション固有のフォルダーへのシンボリック リンクを $Home に作成します。

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl + C キーでコマンドレット プロンプトが終了しない
 - **詳細**: コマンドレットのプロンプトを終了しようとして `Ctrl+C` キーを押しても、プロンプトが終了しません。
 - **解決策**: プロンプトを終了するには、`Ctrl+C` キーを押した後、`Enter` キーを押します。

### <a name="gui-applications-are-not-supported"></a>GUI アプリケーションがサポートされていない
  - **詳細**: ユーザーが GUI アプリを起動しても、プロンプトが返りません。  たとえば、ユーザーが `git` を使って 2 要素認証が有効なプライベート リポジトリを閉じると、2 要素認証コードのダイアログ ボックスが表示されます。
  - **解決策**: コマンドを終了するには `Ctrl+C` キーを押します。

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help -online でヘルプ ページが開かない
 - **詳細**: 「`Get-Help Find-Module -online`」と入力した場合、次のようなエラー メッセージが表示されます。`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
 - **解決策**: URL をブラウザーにコピーして手動で開きます。
 
### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM のリモート管理のトラブルシューティング
 - **詳細**: WinRM に対する Windows ファイアウォールの既定の設定のため、次のようなエラー メッセージが表示されることがあります。`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
 - **解決策**: VM が実行されていることを確認します。 `Get-AzureRmVM -Status` を実行して、VM の状態を確認できます。  次に、サブネットからの WinRM 接続を許可する新しいファイアウォール規則をリモート VM に追加します。次はその例です。

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 [Azure カスタム スクリプト拡張機能][customex] を使って、リモート VM にログオンしないで新しいファイアウォール規則を追加できます。
 上のスクリプトをファイル (`addfirerule.ps1` など) に保存し、Azure ストレージ コンテナーにアップロードできます。
 その後、次のコマンドを試します。

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```
