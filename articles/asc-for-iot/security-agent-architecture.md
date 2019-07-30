---
title: Azure Security Center for IoT のセキュリティ エージェントのアーキテクチャについて (プレビュー) | Microsoft Docs
description: Azure Security Center for IoT サービスで使用されるエージェントのセキュリティ エージェントのアーキテクチャについて説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f4ca9a2689ab9e09b4ebff903e757f5c352b556
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616578"
---
# <a name="security-agent-reference-architecture"></a>セキュリティ エージェントの参照アーキテクチャ

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


Azure Security Center (ASC) for IoT は、IoT ハブ経由でセキュリティ データをログ記録、処理、集計、および送信するセキュリティ エージェントのための参照アーキテクチャを提供します。

セキュリティ エージェントは、制約のある IoT 環境で機能するように設計されており、消費するリソースと比較した場合、提供する価値という観点で高度にカスタマイズ可能です。

セキュリティ エージェントは、次の機能をサポートしています。

- 基になるオペレーティング システム (Linux、Windows) から未処理のセキュリティ イベントを収集する。 使用可能なセキュリティ データ コレクターの詳細については、[ASC for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

- 未加工のセキュリティ イベントを収集して、IoT ハブを介して送信されるメッセージにする。

- 既存のデバイス ID、または専用のモジュール ID で認証する。 詳細については、[セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

- **azureiotsecurity** モジュール ツインを使用してリモートで構成する。 詳細については、[ASC for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

ASC for IoT セキュリティ エージェントはオープン ソース プロジェクトとして開発されており、GitHub から入手できます。 

- [ASC for IoT の C ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [ASC for IoT の C# ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>エージェントでサポートされているプラットフォーム

ASC for IoT では、32 ビットと 64 ビットの Windows のエージェントには異なるインストーラー、32 ビットと 64 ビットの Linux のエージェントには同じインストーラーが用意されています。 次の表に従って、ご使用のデバイスごとに適切なエージェント インストーラーがあることを確認してください。

| 32 または 64 ビット | Linux | Windows |    詳細|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 ビット  | C  | C#  ||
| 64 ビット  | C# または C           | C#      | リソースが少ないデバイスの場合は C エージェントを使用|

## <a name="next-steps"></a>次の手順

この記事では、ASC for IoT セキュリティ エージェントのアーキテクチャと利用できるインストーラーについて学習しました。

ASC for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)を理解する
- [セキュリティ エージェント](how-to-deploy-agent.md)を選択してデプロイする
- ASC for IoT の[サービスの要件](service-prerequisites.md)を確認する
- [IoT Hub で ASC for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法について確認する
- [ASC for IoT についてよく寄せられる質問](resources-frequently-asked-questions.md)からサービスについて確認する
