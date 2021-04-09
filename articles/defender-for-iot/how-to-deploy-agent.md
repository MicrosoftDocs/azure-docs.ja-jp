---
title: セキュリティ エージェントを選択してデプロイする
description: Defender for IoT セキュリティ エージェントを選択して IoT デバイスに展開する方法について説明します。
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: c71c92ffa79c844f3529265320b46eadd0c158cf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778850"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>IoT デバイスにセキュリティ エージェントを選択してデプロイする

Defender for IoT を使用すると、IoT デバイスを監視してデータを収集するセキュリティ エージェントの参照アーキテクチャが提供されます。
詳しくは、「[セキュリティ エージェントの参照アーキテクチャ](security-agent-architecture.md)」をご覧ください。

エージェントはオープン ソース プロジェクトとして開発されており、2 つのフレーバーがあります。 <br> それは、[C](https://aka.ms/iot-security-github-c) と [C#](https://aka.ms/iot-security-github-cs) です。

この記事では、次のことについて説明します。
- セキュリティ エージェントのフレーバーを比較します
- サポートされているエージェント プラットフォームを確認します
- ソリューションに適したエージェントのフレーバーを選択します

## <a name="understand-security-agent-options"></a>セキュリティ エージェントのオプションを理解する

すべての Defender for IoT セキュリティ エージェントのフレーバーでは、同じ機能のセットが提供され、似た構成オプションがサポートされています。

C ベースのセキュリティ エージェントはメモリ占有領域が小さく、使用できるリソースが少ないデバイスに最適です。

|     | C ベースのセキュリティ エージェント | C# ベースのセキュリティ エージェント |
| --- | ----------- | --------- |
| **オープンソース** | [GitHub](https://aka.ms/iot-security-github-c) の [MIT ライセンス](https://en.wikipedia.org/wiki/MIT_License)で使用可能 | [GitHub](https://aka.ms/iot-security-github-cs) の [MIT ライセンス](https://en.wikipedia.org/wiki/MIT_License)で使用可能 |
| **開発言語**    | C | C# |
| **Windows プラットフォームのサポート** | いいえ | はい |
| **Windows の前提条件** | --- | [WMI](/windows/desktop/wmisdk/) |
| **Linux プラットフォームのサポート** | はい、x64 と x86 | はい、x64 のみ |
| **Linux の前提条件** | libunwind8、libcurl3、uuid ランタイム、auditd audispd プラグイン | libunwind8、libcurl3、uuid ランタイム、auditd、audispd プラグイン、sudo、netstat、iptables |
| **ディスクの占有領域** | 10.5 MB | 90 MB |
| **メモリ占有領域 (平均)** | 5.5 MB | 33 MB |
| **IoT Hub に対する [認証](concept-security-agent-authentication-methods.md)** | はい | はい |
| **セキュリティ データの [収集](how-to-agent-configuration.md#supported-security-events)** | はい | はい |
| **イベントの集計** | はい | はい |
| **[Defender-IoT-micro-agent ツイン](concept-security-module.md)によるリモート構成** | はい | はい |

## <a name="security-agent-installation-guidelines"></a>セキュリティ エージェントのインストール ガイドライン

**Windows** の場合:InstallSecurityAgent.ps1 スクリプトは、管理者の PowerShell ウィンドウから実行する必要があります。

**Linux** の場合:InstallSecurityAgent.sh はスーパーユーザーとして実行する必要があります。 インストール コマンドの前に "sudo" を付けることをお勧めします。

## <a name="choose-an-agent-flavor"></a>エージェントのフレーバーを選択する

IoT デバイスに関する次の質問に答えて、適切なエージェントを選択します。

- _Windows Server_ または _Windows IoT Core_ を使用していますか?

    [Windows 用の C# ベースのセキュリティ エージェントをデプロイします](how-to-deploy-windows-cs.md)。

- x86 アーキテクチャの Linux ディストリビューションを使用していますか?

    [Linux 用の C ベースのセキュリティ エージェントをデプロイします](how-to-deploy-linux-c.md)。

- x64 アーキテクチャの Linux ディストリビューションを使用していますか?

    両方のエージェント フレーバーを使用できます。 <br>
    [Linux 用の C ベースのセキュリティ エージェントをデプロイ](how-to-deploy-linux-c.md)するか、[Linux 用の C# ベースのセキュリティ エージェントをデプロイ](how-to-deploy-linux-cs.md)します。

どちらのエージェント フレーバーでも同じ機能セットが提供され、同じ構成オプションがサポートされています。
詳しくは、[セキュリティ エージェントの比較](how-to-deploy-agent.md#understand-security-agent-options)に関する記事をご覧ください。

## <a name="supported-platforms"></a>サポートされているプラットフォーム

次の一覧には、現在サポートされているすべてのプラットフォームが含まれています。

|Defender for IoT エージェント |オペレーティング システム |Architecture |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64、ARMv7|
|C|Debian 9 |    x64、x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64、ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core ビルド 17763    |x64|
|

## <a name="next-steps"></a>次のステップ

構成オプションの詳細については、エージェント構成の攻略ガイドに進んでください。
> [!div class="nextstepaction"]
> [エージェント構成の攻略ガイド](./how-to-agent-configuration.md)