---
title: Azure Security Center for IoT エージェントの Linux C エージェントをインストールおよびデプロイするガイド | Microsoft Docs
description: Azure Security Center for IoT エージェントを 32 ビットと 64 ビットの両方の Linux にインストールする方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812735"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Linux 用の Azure Security Center for IoT の C ベースのセキュリティ エージェントをデプロイする

このガイドでは、Azure Security Center for IoT の C ベースのセキュリティ エージェントを Linux にインストールしてデプロイする方法について説明します。

このガイドでは、以下の方法について説明します。 
> [!div class="checklist"]
> * Install
> * デプロイの確認
> * エージェントのアンインストール
> * トラブルシューティング 

## <a name="prerequisites"></a>前提条件

その他のプラットフォームとエージェントの種類については、[適切なセキュリティ エージェントの選択](how-to-deploy-agent.md)に関するページを参照してください。

1. セキュリティ エージェントをデプロイするには、インストール先のマシンでのローカル管理者権限が必要です (sudo)。

1. デバイスの[セキュリティ モジュールを作成](quickstart-create-security-twin.md)します。

## <a name="installation"></a>インストール 

セキュリティ エージェントをインストールおよびデプロイするには、次のワークフローを使用してください。


1. [GitHub](https://aka.ms/iot-security-github-c) からマシンに最新バージョンをダウンロードします。

1. パッケージの内容を展開し、 _/src/installation_ フォルダーに移動します。

1. 以下のコマンドを実行して、**InstallSecurityAgent スクリプト**に実行アクセス許可を追加します。
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 次に、以下を実行します。 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   認証パラメーターの詳細については、[認証を構成する方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

このスクリプトは、次の関数を実行します。

1. 前提条件のインストール。

2. サービス ユーザーを追加する (対話型サインインは無効)。

3. エージェントを**デーモン**としてインストールする - デバイスがサービス管理に **systemd** を使用すると想定します。

4. 指定された認証パラメーターでエージェントを構成する。 

追加のヘルプについては、–help パラメーターを指定してスクリプトを実行します。 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>エージェントのアンインストール

エージェントをアンインストールするには、–uninstall パラメーターを指定してスクリプトを実行します。

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>トラブルシューティング
以下を実行して、デプロイの状態を確認します。

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>次の手順
- Azure Security Center for IoT サービスの[概要](overview.md)を読みます
- Azure Security Center for IoT の[アーキテクチャ](architecture.md)の詳細を確認します
- [サービス](quickstart-onboard-iot-hub.md)を有効にします
- [FAQ](resources-frequently-asked-questions.md) を読みます
- [セキュリティ アラート](concept-security-alerts.md)について理解します
