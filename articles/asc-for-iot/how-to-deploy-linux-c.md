---
title: Azure Security Center for IoT エージェント プレビューの Linux C エージェントをインストールおよびデプロイするガイド | Microsoft Docs
description: Azure Security Center for IoT エージェントを 32 ビットと 64 ビットの両方の Linux にインストールする方法について説明します。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 0089fd1af6576f9bcdebe4b7f270a573205dea82
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861940"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Linux 用の Azure Security Center for IoT の C ベースのセキュリティ エージェントをデプロイする

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このガイドでは、Azure Security Center (ASC) for IoT の C ベースのセキュリティ エージェントを Linux にインストールしてデプロイする方法について説明します。

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

セキュリティ エージェントをインストールおよびデプロイするには、次の操作を行います。


1. [GitHub](https://aka.ms/iot-security-github-c) からマシンに最新バージョンをダウンロードします。

1. パッケージの内容を展開し、_/Install_ フォルダーに移動します。

1. 以下を実行して、**InstallSecurityAgent スクリプト**に実行アクセス許可を追加します。
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 次に、以下を実行します。 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   認証パラメーターの詳細については、[認証を構成する方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

このスクリプトでは、次の処理が実行されます。

1. 前提条件のインストール。

2. サービス ユーザーを追加する (対話型ログインは無効)。

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
- ASC for IoT サービスの[概要](overview.md)を読みます
- ASC for IoT の[アーキテクチャ](architecture.md)についてさらに学習します
- [サービス](quickstart-onboard-iot-hub.md)を有効にします
- [FAQ](resources-frequently-asked-questions.md) を読みます
- [セキュリティ アラート](concept-security-alerts.md)について理解します