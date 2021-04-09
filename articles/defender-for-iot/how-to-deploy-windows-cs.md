---
title: Windows デバイスに C# エージェントをインストールする
description: Defender for IoT エージェントを 32 ビットまたは 64 ビットの Windows デバイスにインストールする方法について説明します。
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: b5efc9ce675fa068f378cdc2bdd5a077d3437a93
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778765"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Windows 用の Defender for IoT の C# ベースのセキュリティ エージェントをデプロイする

このガイドでは、Defender for IoT の C# ベースのセキュリティ エージェントを Windows にインストールする方法について説明します。

このガイドでは、以下の方法について説明します。

- インストール
- デプロイの確認
- エージェントのアンインストール
- トラブルシューティング

## <a name="prerequisites"></a>前提条件

その他のプラットフォームとエージェントの種類については、[適切なセキュリティ エージェントの選択](how-to-deploy-agent.md)に関するページを参照してください。

1. インストール先のマシンのローカル管理者権限。

1. デバイスの [Defender-IoT-micro-agent を作成します](quickstart-create-security-twin.md)。

## <a name="installation"></a>インストール

セキュリティ エージェントをインストールするには、次のワークフローを使用してください。

1. Defender for IoT Windows C# エージェントをデバイスにインストールします。 Defender for IoT [GitHub リポジトリ](https://github.com/Azure/Azure-IoT-Security-Agent-CS)から最新バージョンを自分のマシンにダウンロードします。

1. パッケージの内容を展開し、/Install フォルダーに移動します。

1. Windows PowerShell を管理者として開きます。
1. 以下を実行して、InstallSecurityAgent スクリプトに実行アクセス許可を追加します。

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    次に、以下を実行します。

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    次に例を示します。

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    認証パラメーターの詳細については、[認証を構成する方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

このスクリプトでは、次のアクションを実行します。

* 前提条件のインストール。
* サービス ユーザーを追加する (対話型サインインは無効)。
* **システム サービス** としてエージェントをインストールする。
* 指定された認証パラメーターでエージェントを構成する。

追加のヘルプについては、PowerShell で Get-Help コマンドを使用してください。

Get-Help の例: ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>デプロイ状態の確認

以下を実行して、エージェントのデプロイの状態を確認します。

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>エージェントのアンインストール

エージェントをアンインストールするには、次の操作を行います。

1. 次の PowerShell スクリプトを、 **-mode** パラメーターを **Uninstall** に設定して実行します。

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
    > トラブルシューティングの終了後は、ログ記録を **無効** にすることをお勧めします。 ログ記録を **有効** のままにしておくと、ログ ファイルのサイズとデータの使用量が増加します。

1. 次の PowerShell またはコマンド ラインを実行して、エージェントを再起動します。

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   or

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. ログ ファイルで、エラーの詳細情報を調べます。 ログ ファイルは、スクリプトを実行する作業ファイルにあります。 

   ログ ファイルの保存先: `.\IoTAgentLog.log`

## <a name="next-steps"></a>次のステップ

* Defender for IoT サービスの[概要](overview.md)を確認する
* Defender for IoT の[アーキテクチャ](architecture.md)について詳細を確認する
* [サービス](quickstart-onboard-iot-hub.md)を有効にします
* [FAQ](resources-frequently-asked-questions.md) を読みます
* [アラート](concept-security-alerts.md)について理解します
