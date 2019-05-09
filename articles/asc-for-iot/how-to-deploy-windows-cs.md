---
title: Azure Security Center for IoT エージェント プレビューの Windows インストール | Microsoft Docs
description: Azure Security Center for IoT エージェントを 32 ビットまたは 64 ビットの Windows デバイスにインストールする方法について説明します。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 601ee706adedf522890acc3f3996a7d36b349348
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577840"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Windows 用の Azure Security Center for IoT の C# ベースのセキュリティ エージェントをデプロイする

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このガイドでは、Azure Security Center (ASC) for IoT の C# ベースのセキュリティ エージェントを Windows にインストールする方法について説明します。

このガイドでは、以下の方法について説明します。 
> [!div class="checklist"]
> * Install
> * デプロイの確認
> * エージェントのアンインストール
> * トラブルシューティング 

## <a name="prerequisites"></a>前提条件

その他のプラットフォームとエージェントの種類については、[適切なセキュリティ エージェントの選択](how-to-deploy-agent.md)に関するページを参照してください。

1. インストール先のマシンのローカル管理者権限。 

1. デバイスの[セキュリティ モジュールを作成](quickstart-create-security-twin.md)します。

## <a name="installation"></a>インストール 

セキュリティ エージェントをインストールするには、次の操作を行います。

1. ASC for IoT Windows C# エージェントをデバイスにインストールするには、ASC for IoT [GitHub リポジトリ](https://github.com/Azure/Azure-IoT-Security-Agent-CS)から最新バージョンを自分のマシンにダウンロードします。

2. パッケージの内容を展開し、/Install フォルダーに移動します。

3. Windows PowerShell を管理者として開きます。 
    1. ```Unblock-File .\InstallSecurityAgent.ps1``` を実行して、InstallSecurityAgent スクリプトに実行アクセス許可を追加します。
    
        次に、以下を実行します。

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    例: 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    認証パラメーターの詳細については、[認証を構成する方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

このスクリプトでは、次の処理が実行されます。

- 前提条件のインストール。

- サービス ユーザーを追加する (対話型ログインは無効)。

- **システム サービス**としてエージェントをインストールする。

- 指定された認証パラメーターでエージェントを構成する。


追加のヘルプについては、PowerShell で Get-Help コマンドを使用してください <br>Get-Help の例:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>デプロイ状態の確認

- 以下を実行して、エージェントのデプロイの状態を確認します。<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>エージェントのアンインストール

エージェントをアンインストールするには、次の操作を行います。

1. 次の PowerShell スクリプトを、**-mode** パラメーターを **Uninstall** に設定して実行します。  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>トラブルシューティング

エージェントが起動しない場合は、ログ記録を有効にして (既定では、ログ記録は "*無効*" になっています)、詳細情報を取得します。

ログ記録を有効にするには、以下の操作を行います。

1. 標準のファイル エディターを使用して、編集するために構成ファイル (General.config) を開きます。

1. 以下の値を編集します。

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > トラブルシューティングの終了後は、ログ記録を**無効**にすることをお勧めします。 ログ記録を**有効**のままにしておくと、ログ ファイルのサイズとデータの使用量が増加します。 

1. 次の PowerShell またはコマンド ラインを実行して、エージェントを再起動します。

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   or

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. ログ ファイルで、エラーの詳細情報を調べます。

   ログ ファイルの保存先: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>次の手順
- ASC for IoT サービスの[概要](overview.md)を読みます
- ASC for IoT の[アーキテクチャ](architecture.md)についてさらに学習します
- [サービス](quickstart-onboard-iot-hub.md)を有効にします
- [FAQ](resources-frequently-asked-questions.md) を読みます
- [アラート](concept-security-alerts.md)について理解します