---
title: 'クイックスタート: セキュリティ エージェントの概要'
description: このクイックスタートでは、Azure Defender for IoT サービスで使用されるエージェントのセキュリティ エージェントのアーキテクチャについて説明します。
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: 2e7d7d1e6770667b1ce966724611cc003116409d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778510"
---
# <a name="quickstart-security-agent-reference-architecture"></a>クイックスタート: セキュリティ エージェントの参照アーキテクチャ

Azure Defender for IoT には、IoT Hub を介してセキュリティ データのログを収集、処理、集計、および送信する、セキュリティ エージェントの参照アーキテクチャがあります。

セキュリティ エージェントは、制約のある IoT 環境で機能するように設計されており、消費するリソースと比較した場合、提供する価値という観点で高度にカスタマイズ可能です。

セキュリティ エージェントは、次の機能をサポートしています。

- 既存のデバイス ID、または専用のモジュール ID で認証する。 詳細については、 [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

- 基になるオペレーティング システム (Linux、Windows) から未処理のセキュリティ イベントを収集する。 使用可能なセキュリティ データ コレクターの詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

- 未加工のセキュリティ イベントを収集して、IoT Hub を介して送信されるメッセージにする。

- **azureiotsecurity** モジュール ツインを使用してリモートで構成する。 詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

Defender for IoT セキュリティ エージェントはオープン ソース プロジェクトとして開発されており、次のように GitHub から入手できます。

- [Defender for IoT の C ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender for IoT の C# ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="prerequisites"></a>前提条件

なし

## <a name="agent-supported-platforms"></a>エージェントでサポートされているプラットフォーム

Defender for IoT では、32 ビットと 64 ビットの Windows のエージェントには異なるインストーラー、32 ビットと 64 ビットの Linux のエージェントには同じインストーラーが用意されています。 次の表に従って、ご使用のデバイスごとに適切なエージェント インストーラーがあることを確認してください。

| Architecture | Linux | Windows | 詳細 |
|--|--|--|--|
| 32 ビット | C | C# |  |
| 64 ビット | C# または C | C# | デバイス リソースがさらに制限されているか少ないデバイスについては、C エージェントを使用することをお勧めします。 |


## <a name="next-steps"></a>次のステップ

この記事では、Defender for IoT Defender-IoT-micro-agent アーキテクチャと利用できるインストーラーについて概要を学習しました。

Defender for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)
