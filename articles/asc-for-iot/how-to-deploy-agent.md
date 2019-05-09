---
title: Azure Security Center for IoT エージェント (プレビュー) を選択してデプロイする | Microsoft Docs
description: IoT デバイスに Azure Security Center for IoT セキュリティ エージェントを選択してデプロイする方法について説明します。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862422"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>IoT デバイスにセキュリティ エージェントを選択してデプロイする

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Security Center (ASC) for IoT では、IoT デバイスを監視してデータを収集するセキュリティ エージェントの参照アーキテクチャが提供されます。
詳しくは、「[セキュリティ エージェントの参照アーキテクチャ](security-agent-architecture.md)」をご覧ください。

エージェントはオープン ソース プロジェクトとして開発されており、2 つのフレーバーがあります。 <br> それは、[C](https://aka.ms/iot-security-github-c) と [C#](https://aka.ms/iot-security-github-cs) です。

この記事では、次のことについて説明します。 
> [!div class="checklist"]
> * セキュリティ エージェントのフレーバーを比較します
> * サポートされているエージェント プラットフォームを確認します
> * ソリューションに適したエージェントのフレーバーを選択します

## <a name="understand-security-agent-options"></a>セキュリティ エージェントのオプションを理解する

すべての ASC for IoT セキュリティ エージェントのフレーバーでは、同じ機能のセットが提供され、似た構成オプションがサポートされています。 

C ベースのセキュリティ エージェントはメモリ占有領域が小さく、使用できるリソースが少ないデバイスに最適です。 

|     | C ベースのセキュリティ エージェント | C# ベースのセキュリティ エージェント |
| --- | ----------- | --------- |
| オープン ソース | [GitHub](https://aka.ms/iot-security-github-cs) の [MIT ライセンス](https://en.wikipedia.org/wiki/MIT_License)で使用可能 | [GitHub](https://aka.ms/iot-security-github-c) の [MIT ライセンス](https://en.wikipedia.org/wiki/MIT_License)で使用可能 |
| 開発言語    | C | C# |
| Windows プラットフォームのサポート | いいえ  | はい |
| Windows の前提条件 | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| Linux プラットフォームのサポート | はい、x64 と x86 | はい、x64 のみ |
| Linux の前提条件 | libunwind8、libcurl3、uuid ランタイム、auditd audispd プラグイン | libunwind8、libcurl3、uuid ランタイム、auditd、audispd プラグイン、sudo、netstat、iptables |
| ディスクの占有領域 | 10.5 MB | 90 MB |
| メモリ占有領域 (平均) | 5.5 MB | 33 MB |
| IoT Hub に対する[認証](concept-security-agent-authentication-methods.md) | はい | はい |
| セキュリティ データ[収集](how-to-agent-configuration.md#supported-security-events) | はい | はい |
| イベントの集計 | はい | はい |
| [セキュリティ モジュール ツイン](concept-security-module.md)によるリモート構成 | はい | はい |


## <a name="choose-an-agent-flavor"></a>エージェントのフレーバーを選択する 

IoT デバイスに関する次の質問に答えて、適切なエージェントを選択します。

- _Windows Server_ または _Windows IoT Core_ を使用していますか? 

    [Windows 用の C# ベースのセキュリティ エージェントをデプロイします](how-to-deploy-windows-cs.md)。

- x86 アーキテクチャの Linux ディストリビューションを使用していますか? 

    [Linux 用の C ベースのセキュリティ エージェントをデプロイします](how-to-deploy-linux-c.md)。

- x64 アーキテクチャの Linux ディストリビューションを使用していますか?

    いずれかのフレーバーを使用できます。 <br>
    [Linux 用の C ベースのセキュリティ エージェントをデプロイ](how-to-deploy-linux-c.md)するか、[Linux 用の C# ベースのセキュリティ エージェントをデプロイ](how-to-deploy-linux-cs.md)します。

どちらのエージェント フレーバーでも同じ機能セットが提供され、同じ構成オプションがサポートされています。
詳しくは、[セキュリティ エージェントの比較](how-to-deploy-agent.md#understand-security-agent-options)に関する記事をご覧ください。

## <a name="supported-platforms"></a>サポートされるプラットフォーム

次の一覧には、現在サポートされているすべてのプラットフォームが含まれています。

|ASC for IoT エージェント |オペレーティング システム |アーキテクチャ |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64、x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core ビルド 17763 |x64|

## <a name="next-steps"></a>次の手順

構成オプションの詳細については、エージェント構成の攻略ガイドに進んでください。 
> [!div class="nextstepaction"]
> [エージェント構成の攻略ガイド](./how-to-agent-configuration.md)
