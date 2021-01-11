---
title: Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: Speech CLI は、コードを記述せずに Speech サービスを使用するためのコマンド ライン ツールです。 Speech CLI では最小限の設定が必要であり、ユース ケースを満たすことができるかどうかを確認するための、Speech サービスの主要な機能をすぐに簡単に試すことができます。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067428"
---
# <a name="what-is-the-speech-cli"></a>Speech CLI とは

Speech CLI は、コードを記述せずに Speech サービスを使用するためのコマンド ライン ツールです。 Speech CLI では最小限の設定が必要であり、ユース ケースを満たすことができるかどうかを確認するための、Speech サービスの主要な機能をすぐに簡単に試すことができます。 ファイルのディレクトリからのバッチ音声認識、ファイルからの文字列のコレクションのテキスト読み上げなどのシンプルなテスト ワークフローを数分以内に実行できます。 Speech CLI は、シンプルなワークフロー以外にも、実稼働で使用する準備ができており、自動化された `.bat` またはシェル スクリプトを使用して、より大きなプロセスを実行するようにスケールアップすることができます。

Speech SDK の主な機能の大部分は、Speech CLI で利用できます。また、Speech CLI では、一部の高度な機能とカスタマイズが簡略化されています。 Speech CLI または Speech SDK のどちらを使用するかを決定するには、次のガイダンスを考慮してください。

Speech CLI を使用する場合
* 最小限の設定かつコードなしで Speech サービス機能を試す必要がある
* Speech サービスを使用した実稼働アプリケーションの比較的シンプルな要件がある

Speech SDK を使用する場合
* 特定の言語またはプラットフォーム (C#、Python、C++ など) 内で Speech サービス機能を統合する必要がある
* 高度なサービス要求、応答ストリーミングを含むカスタム動作の開発を必要とする複雑な要件がある

## <a name="core-features"></a>コア機能

* 音声認識 - 音声ファイルから、またはマイクからの直接のいずれかの音声テキスト変換、または記録された会話の書き起こしを行います。

* 音声合成 - テキスト ファイルからの入力、またはコマンド ラインから直接入力のいずれかを使用して、テキスト読み上げの変換を行います。 [SSML 構成](speech-synthesis-markup.md)および[標準またはニューラルの音声](speech-synthesis-markup.md#standard-neural-and-custom-voices)のいずれかを使用して、音声出力の特性をカスタマイズします。

* 音声翻訳 - ソース言語の音声をターゲット言語のテキストまたは音声に翻訳します。

* Azure コンピューティング リソースで実行する - `spx webjob` を使用して、Azure リモート コンピューティング リソースで実行する Speech CLI コマンドを送信します。

## <a name="get-started"></a>はじめに

Speech CLI の使用を開始するには、[基本の記事](spx-basics.md)を参照してください。 この記事では、いくつかの基本的なコマンドを実行する方法について示します。また、音声テキスト変換やテキスト読み上げのバッチ操作を実行するための少し高度なコマンドも示します。 基本の記事を読み終えたら、いくつかのカスタム コマンドの記述を開始するか、シンプルな Speech サービス操作を自動化する構文について十分に理解しておく必要があります。

## <a name="next-steps"></a>次のステップ

- [Speech CLI の基本](spx-basics.md)
- ユース ケースがより複雑な場合は、[Speech SDK を入手してください](speech-sdk.md)
