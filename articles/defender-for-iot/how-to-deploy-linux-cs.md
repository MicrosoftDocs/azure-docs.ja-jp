---
title: Linux C# エージェントをインストールおよびデプロイする
description: Defender for IoT の C# ベースのセキュリティ エージェントを Linux にインストールしてデプロイする方法について説明します
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: c84a70928be13212b56636ad1fbb9baaadd0e7d0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784205"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Linux 用の Defender for IoT の C# ベースのセキュリティ エージェントをデプロイする

このガイドでは、Defender for IoT の C# ベースのセキュリティ エージェントを Linux にインストールしてデプロイする方法について説明します。

このガイドでは、以下の方法について説明します。

- インストール
- デプロイの確認
- エージェントのアンインストール
- トラブルシューティング

## <a name="prerequisites"></a>前提条件

その他のプラットフォームとエージェントの種類については、[適切なセキュリティ エージェントの選択](how-to-deploy-agent.md)に関するページを参照してください。

1. セキュリティ エージェントをデプロイするには、インストール先のマシンでのローカル管理者権限が必要です。

1. デバイスの [Defender for IoT マイクロ エージェントを作成](quickstart-create-security-twin.md)します。

## <a name="installation"></a>インストール

セキュリティ エージェントをデプロイするには、次の手順に従います。

1. [GitHub](https://aka.ms/iot-security-github-cs) からマシンに最新バージョンをダウンロードします。

1. パッケージの内容を展開し、 _/Install_ フォルダーに移動します。

1. `chmod +x InstallSecurityAgent.sh` を実行して、**InstallSecurityAgent スクリプト** に実行アクセス許可を追加します。

1. 次に、**ルート特権** を使用して次のコマンドを実行します。

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   認証パラメーターの詳細については、[認証を構成する方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

このスクリプトは、次のアクションを実行します。

- 前提条件のインストール。

- サービス ユーザーを追加する (対話型サインインは無効)。

- エージェントを **デーモン** としてインストールする - デバイスがクラシック デプロイ モデルに **systemd** を使用すると想定します。

- エージェントに特定のタスクをルートとして実行することを許可するように **sudoers** を構成する。

- 指定された認証パラメーターでエージェントを構成する。

追加のヘルプについては、–help パラメーターを指定してスクリプトを実行します (`./InstallSecurityAgent.sh --help`)。

### <a name="uninstall-the-agent"></a>エージェントのアンインストール

エージェントをアンインストールするには、–u パラメーターを指定してスクリプトを実行します (`./InstallSecurityAgent.sh -u`)。

> [!NOTE]
> アンインストールしても、インストール中にインストールされた、不足していた前提条件は削除されません。

## <a name="troubleshooting"></a>トラブルシューティング

1. 以下を実行して、デプロイの状態を確認します。

    `systemctl status ASCIoTAgent.service`

1. ログ記録を有効にします。
   エージェントが起動しない場合は、ログ記録を有効にして、詳細情報を取得します。

   ログ記録を有効にするには、次の操作を行います。

   1. 任意の Linux エディターで、編集するために構成ファイルを開きます。

        `vi /var/ASCIoTAgent/General.config`

   1. 以下の値を編集します。

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       **logFilePath** 値は、構成可能です。

       > [!NOTE]
       > トラブルシューティングの終了後は、ログ記録を **無効** にすることをお勧めします。 ログ記録を **有効** のままにしておくと、ログ ファイルのサイズとデータの使用量が増加します。

   1. 以下を実行して、エージェントを再起動します。

       `systemctl restart ASCIoTAgent.service`

   1. ログ ファイルで、エラーの詳細情報を調べます。

       ログ ファイルの場所は、`/var/ASCIoTAgent/IotAgentLog.log` です。

       ファイルの場所のパスは、手順 2. で **logFilePath** に対して選択した名前に応じて変わります。

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの[概要](overview.md)を確認する
- Defender for IoT の[アーキテクチャ](architecture.md)について詳細を確認する
- [サービス](quickstart-onboard-iot-hub.md)を有効にします
- [FAQ](resources-frequently-asked-questions.md) を読みます
- [アラート](concept-security-alerts.md)について理解します
