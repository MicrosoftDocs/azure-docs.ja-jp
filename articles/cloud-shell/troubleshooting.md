---
title: Azure Cloud Shell のトラブルシューティング | Microsoft Docs
description: Azure Cloud Shell のトラブルシューティング
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: eea64520dd5440467c911b6de42d8c8c31fc1bde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543454"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Azure Cloud Shell のトラブルシューティングと制限事項

Azure Cloud Shell に関する問題のトラブルシューティングを行うための既知の解決策は以下のとおりです。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング

### <a name="error-running-azuread-cmdlets-in-powershell"></a>PowerShell で AzureAD コマンドレットを実行中にエラーが発生した

- **[詳細]** :Cloud Shell で `Get-AzureADUser` などの AzureAD コマンドレットを実行すると、`You must call the Connect-AzureAD cmdlet before calling any other cmdlets` のエラーが表示されることがあります。 
- **解決方法**:`Connect-AzureAD` コマンドレットを実行します。 以前は、PowerShell の起動時にこのコマンドレットが Cloud Shell により自動的に実行されました。 起動時間を短縮するために、このコマンドレットは自動的には実行されなくなりました。 PowerShell で $PROFILE ファイルに `Connect-AzureAD` を追加して、以前の動作を復元することもできます。

### <a name="early-timeouts-in-firefox"></a>FireFox での早期タイムアウト

- **[詳細]** :Cloud Shell は開いている WebSocket を使ってお使いのブラウザーに入力/出力を渡します。 FireFox には WebSocket を途中で閉じることができる事前設定されたポリシーがあり、Cloud Shell の早期タイムアウトの原因になります。
- **解決方法**:FireFox を開き、URL で "about:config" に移動します。 "network.websocket.timeout.ping.request" を検索し、値を 0 から 10 に変更します。

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>ロック ダウンされたネットワーク環境で Cloud Shell を無効にする

- **[詳細]** :管理者によっては、ユーザーが Cloud Shell にアクセスできないようにしたほうが望ましいと判断する場合があります。 Cloud Shell では、`ux.console.azure.com` ドメインへのアクセスが使用されますが、このアクセスは拒否される場合があり、その場合は、Cloud Shell のエントリ ポイントへのアクセスがすべて停止されます (portal.azure.com、shell.azure.com、Visual Studio Code Azure Account 拡張機能、docs.microsoft.com を含む)。 米国政府のクラウドでは、エントリポイントは `ux.console.azure.us` であり、対応する shell.azure.us はありません。
- **解決方法**:ご利用の環境のネットワーク設定を通じて、`ux.console.azure.com` または `ux.console.azure.us` へのアクセスを制限します。 Cloud Shell アイコンはその後も Azure portal に表示されますが、サービスに正常に接続することはできなくなります。

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>ストレージ ダイアログ - エラー: 403 RequestDisallowedByPolicy

- **[詳細]** :Cloud Shell 経由でストレージ アカウントを作成する場合は、管理者によって配置された Azure Policy の割り当てが原因で、作成に失敗します。エラー メッセージには以下が含まれます。`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **解決方法**:Azure 管理者に連絡して、ストレージの作成を拒否している Azure Policy 割り当てを削除または更新してもらいます。

### <a name="storage-dialog---error-400-disallowedoperation"></a>ストレージ ダイアログ - エラー: 400 DisallowedOperation

- **[詳細]** :Azure Active Directory サブスクリプションを使うと、ストレージを作成できません。
- **解決方法**:ストレージ リソースを作成できる Azure サブスクリプションを使ってください。 Azure AD サブスクリプションでは、Azure のリソースを作成できません。

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>ターミナル出力 - エラー: Failed to connect terminal: websocket cannot be established. (ターミナルに接続できませんでした: WebSocket を確立できません。) Press `Enter` to reconnect. (再接続するには Enter キーを押してください。)
- **[詳細]** :Cloud Shell では、Cloud Shell インフラストラクチャへの WebSocket 接続を確立できる必要があります。
- **解決方法**:HTTPS 要求および WebSocket 要求の *.console.azure.com のドメインへの送信を有効にするようにネットワーク設定を構成していることを確認します。

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>TLS 1.2 を使用したサポートのための Cloud Shell 接続の設定
 - **[詳細]** :Cloud Shell への接続用の TLS のバージョンを定義するには、ブラウザー固有の設定を行う必要があります。
 - **解決方法**:ブラウザーのセキュリティ設定に移動して、[TLS 1.2 の使用] の横にあるチェック ボックスをオンにします。

## <a name="bash-troubleshooting"></a>Bash のトラブルシューティング

### <a name="cannot-run-the-docker-daemon"></a>Docker デーモンを実行できない

- **[詳細]** :Cloud Shell では、コンテナーを利用してシェル環境をホストするため、デーモンを実行することが許可されていません。
- **解決方法**:既定でインストールされている [Docker マシン](https://docs.docker.com/machine/overview/)を利用して、リモート Docker ホストから Docker コンテナーを管理します。

## <a name="powershell-troubleshooting"></a>PowerShell のトラブルシューティング

### <a name="gui-applications-are-not-supported"></a>GUI アプリケーションがサポートされていない

- **[詳細]** :ユーザーが GUI アプリケーションを起動しても、プロンプトが返りません。 たとえば、2 要素認証が有効なプライベート GitHub リポジトリを複製すると、2 要素認証を完了するためのダイアログ ボックスが表示されます。
- **解決方法**:シェルをいったん閉じて、再び開きます。

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM のリモート管理のトラブルシューティング
> [!NOTE]
> Azure VM には、一般に公開されている IP アドレスが必要です。

- **[詳細]** :WinRM に対する Windows ファイアウォールの既定の設定のため、次のようなエラー メッセージが表示されることがあります。`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **解決方法**:`Enable-AzVMPSRemoting` を実行して、ターゲット コンピューター上での PowerShell リモート処理のすべての側面を有効にします。

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` によって Azure ドライブで結果が更新されない

- **[詳細]** :既定では、ユーザー エクスペリエンスを確保する最適化のために、`dir` の結果が Azure ドライブにキャッシュされます。
- **解決方法**:Azure リソースを作成、更新、または削除した後に、`dir -force` を実行して Azure ドライブで結果を更新します。

## <a name="general-limitations"></a>一般的な制限事項

Azure Cloud Shell には、次の既知の制限があります。

### <a name="quota-limitations"></a>クォータ制限

Azure Cloud Shell には、リージョンごとにテナントあたり 20 人という同時実行ユーザー数の制限があります。 制限よりも多くの同時セッションを開こうとすると、"Tenant User Over Quota"\(テナントユーザーがクォータを超えています\) というエラーが表示されます。 この数よりも多くのセッションを開く正当な必要性がある場合 (トレーニング セッションの場合など)、予定される使用よりも前にサポートに問い合わせて、クォータの引き上げを要求します。

Cloud Shell は無料のサービスとして提供されており、汎用的なコンピューティング プラットフォームとしてではなく、Azure 環境の構成に使用するように設計されています。 使用を過剰に自動化すると、Azure のサービス利用規約に違反していると見なされ、Cloud Shell のアクセスがブロックされる可能性があります。

### <a name="system-state-and-persistence"></a>システム状態と永続化

Cloud Shell セッションを提供するマシンは一時的であり、セッションが 20 分間非アクティブの状態になると、リサイクルされます。 Cloud Shell では、Azure ファイル共有がマウントされている必要があります。 そのため、Cloud Shell にアクセスするには、ご利用のサブスクリプションでストレージ リソースをセットアップできることが必要です。 その他の考慮事項:

- マウントされたストレージでは、`clouddrive` ディレクトリ内の変更のみが永続化されます。 Bash では、`$HOME` ディレクトリも永続化されます。
- Azure ファイル共有は、[割り当て済みリージョン](persisting-shell-storage.md#mount-a-new-clouddrive)内からのみマウントできます。
  - Bash では、`env` を実行して、`ACC_LOCATION` として設定されたリージョンを検索します。
- Azure Files は、ローカル冗長ストレージと geo 冗長ストレージのアカウントのみをサポートします。

### <a name="browser-support"></a>ブラウザーのサポート

Cloud Shell では、以下のブラウザーの最新バージョンがサポートされます。

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - プライベート モードの Safari はサポートされません。

### <a name="copy-and-paste"></a>コピーと貼り付け

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Usage limits (使用状況の制限)

Cloud Shell は対話型のユース ケースを想定しています。 そのため、実行時間の長い非対話型セッションは、警告なしで終了します。

### <a name="user-permissions"></a>ユーザーのアクセス許可

権限は、sudo アクセスのない、通常のユーザーとして設定されます。 `$Home` ディレクトリ外のインストールはすべて失われます。

## <a name="bash-limitations"></a>Bash の制限事項

### <a name="editing-bashrc"></a>.bashrc の編集

.bashrc を編集すると Cloud Shell で予期しないエラーが発生する可能性があるため、編集には注意が必要です。

## <a name="powershell-limitations"></a>PowerShell の制限事項

### <a name="preview-version-of-azuread-module"></a>AzureAD モジュールのプレビュー バージョン

現時点では、.NET Standard ベースのモジュールのプレビュー バージョンである `AzureAD.Standard.Preview` を入手できます。 このモジュールは、`AzureAD` と同じ機能を提供します。

## <a name="personal-data-in-cloud-shell"></a>Cloud Shell での個人データ

Azure Cloud Shell は、ユーザーの個人データを慎重に取り扱います。Azure Cloud Shell サービスによって取得および格納されたデータは、最近使用されたシェル、推奨されるフォント サイズ、推奨されるフォントの種類、クラウド ドライブの基盤となるファイル共有の詳細などの、ユーザー エクスペリエンスのための既定値を提供するために使用されます。 このデータをエクスポートまたは削除したい場合は、以下の手順に従ってください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>エクスポート
選択されたシェル、フォント サイズ、フォントの種類など、Cloud Shell によって保存されるユーザー設定を **エクスポート** するには、次のコマンドを実行します。

1. [![[Azure Cloud Shell の起動] というラベルが付いたボタンを示すイメージ。](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Bash または PowerShell で次のコマンドを実行します。

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>削除
選択されたシェル、フォント サイズ、フォントの種類など、Cloud Shell によって保存されるユーザー設定を **削除** するには、次のコマンドを実行します。 次回、Cloud Shell を起動すると、再びファイル共有にオンボードするように求めるメッセージが表示されます。 

>[!Note]
> ユーザー設定を削除しても、実際の Azure Files 共有は削除されません。 Azure Files に移動して、そのアクションを完了します。

1. [![[Azure Cloud Shell の起動] というラベルが付いたボタンを示すイメージ。](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Bash または PowerShell で次のコマンドを実行します。

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Azure Government の制限事項
Azure Government の Azure Cloud Shell には Azure portal からのみアクセスできます。

>[!Note]
> 現在、Exchange Online 用の GCC-High または Government DoD Cloud への接続はサポートされていません。
