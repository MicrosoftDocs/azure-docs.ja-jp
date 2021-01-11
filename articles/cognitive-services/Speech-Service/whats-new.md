---
title: ドキュメントの最新情報 - 音声サービス
titleSuffix: Azure Cognitive Services
description: Azure 音声サービスのドキュメントの更新情報について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: erhopf
ms.openlocfilehash: 4c448b8ed50863457da69db3f74b959f04e26ec1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131856"
---
# <a name="speech-service-whats-new-in-docs"></a>音声サービス: ドキュメントの最新情報

ようこそ。 このページでは、音声サービスのドキュメントの新機能について説明します。毎月、サービスの変更、ドキュメントの追加および更新に関する情報を確認してください。

### <a name="service-updates"></a>サービスの更新情報

音声サービス、Speech SDK、Speech Devices SDK、または Speech CLI に対する更新情報については、次を参照してください。
* [Speech SDK のリリース ノート](releasenotes.md)。
* [Speech Devices SDK のリリース ノート](devices-sdk-release-notes.md)

## <a name="may-2020"></a>2020 年 5 月

### <a name="new-articles"></a>新しい記事

* [Custom Speech 用のモデルを向上させる](how-to-custom-speech-improve-accuracy.md)

### <a name="updated-articles"></a>更新された記事

* [Speech SDK オーディオ入力ストリーム API について](how-to-use-audio-input-streams.md) - 1 秒間に許可されるサンプル数を更新しました。
* [音声テキスト変換の自動言語検出](how-to-automatic-language-detection.md) - Objective-C の手順をドキュメントに追加しました。
* [音声認識モードを選択する](how-to-choose-recognition-mode.md) - JavaScript の手順をドキュメントに追加しました。
* [Speech SDK 用に RHEL/CentOS 7 を構成する](how-to-configure-rhel-centos-7.md) - 設定手順を改善しました。
* [音声テキスト変換用のフレーズ リスト](how-to-phrase-lists.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: Python の長い形式の音声の非同期合成 (プレビュー)](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) - パブリック ニューラル音声のサポートと関連するパラメーターについて更新しました。
* [クイック スタート: オーディオ ファイルから音声を認識する](quickstarts/speech-to-text-from-file.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: マイクから音声を認識する](quickstarts/speech-to-text-from-microphone.md )- Go および JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: BLOB ストレージに格納された音声を認識する](quickstarts/from-blob.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: Language Understanding (LUIS) を使用して、音声、意図、エンティティを認識する](quickstarts/intent-recognition.md)
* [クイック スタート: 開発環境を設定する](quickstarts/setup-platform.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: オーディオ ファイルに音声を合成する](quickstarts/text-to-speech-audio-file.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: スピーカーに音声を合成する](quickstarts/text-to-speech.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: 音声間翻訳](quickstarts/translate-speech-to-speech.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: 音声を複数の言語に翻訳する](quickstarts/translate-speech-to-text-multiple-languages.md) - JavaScript の手順をドキュメントに追加しました。
* [クイック スタート: 音声テキスト変換の翻訳](quickstarts/translate-speech-to-text.md) - JavaScript の手順をドキュメントに追加しました。
* [キーワードとは](custom-keyword-overview.md) - 概要の内容およびリンクを更新しました。
* [音声テキスト変換のソース言語を指定する](how-to-specify-source-language.md) - JavaScript および Objective-C の手順をドキュメントに追加しました。

### <a name="github-issues-opened-in-may"></a>5 月にオープンされた GitHub イシュー

以下のイシューは 5 月にオープンされました。 次の表に、イシューをオープンしたユーザー、オープンされた日、およびその状態を示します。  

この表は毎月更新されるため、5月にオープンされたイシューのみが反映されています。  

|id|User|説明|開始済み|State|Type|
| :--- | :--- | :--- | :--- | :--- | :--- |
|[56045](https://github.com/MicrosoftDocs/azure-docs/issues/56045)|rhalaly|ボットのエンドポイントがないため、アクティビティが破棄された|2020-05-31|クローズ|問題|
|[56038](https://github.com/MicrosoftDocs/azure-docs/issues/56038)|rhalaly|ボットの発行手順が正しくない|2020-05-31|[ファイル]|問題|
|[56014](https://github.com/MicrosoftDocs/azure-docs/issues/56014)|mosdav|PCM サンプル形式に関するより明確なドキュメントの追加|2020-05-30|[ファイル]|問題|
|[55984](https://github.com/MicrosoftDocs/azure-docs/issues/55984)|chschrae|タイトルがサイド バーと一致しない|2020-05-29|クローズ|問題|
|[55857](https://github.com/MicrosoftDocs/azure-docs/issues/55857)|nitinbhatia-dev|wave ファイルでの CLI エラー|2020-05-28|クローズ|問題|
|[55717](https://github.com/MicrosoftDocs/azure-docs/pull/55717)|dargilco|speech-sdk.md の更新|2020-05-27|[ファイル]|Pull Request|
|[55299](https://github.com/MicrosoftDocs/azure-docs/issues/55299)|Tirumala-K|サポートされていない音声名が使用された奇妙なエラー|2020-05-20|クローズ|問題|
|[55099](https://github.com/MicrosoftDocs/azure-docs/issues/55099)|kmoore-riphaina|Speech to Text API に関するドキュメントの内容が不十分|2020-05-18|[ファイル]|問題|
|[55032](https://github.com/MicrosoftDocs/azure-docs/issues/55032)|dubbySwords|Microsoft CognitiveServices 音声クラス SpeechRecognizer で結果のテキストを収集できない|2020-05-18|クローズ|問題|
|[55031](https://github.com/MicrosoftDocs/azure-docs/issues/55031)|dubbySwords|不明瞭である|2020-05-18|クローズ|問題|
|[55027](https://github.com/MicrosoftDocs/azure-docs/issues/55027)|ovishesh|グラフィックスがダーク テーマで表示されない|2020-05-17|クローズ|問題|
|[54919](https://github.com/MicrosoftDocs/azure-docs/issues/54919)|kmoore-riphaina|セクションが見当たらない?|2020-05-15|[ファイル]|問題|
|[54743](https://github.com/MicrosoftDocs/azure-docs/issues/54743)|fifteenjoy|音声サービス コンテナーの実行が失敗する|2020-05-13|[ファイル]|問題|
|[54550](https://github.com/MicrosoftDocs/azure-docs/issues/54550)|manish-95|発音ファイルの例|2020-05-11|[ファイル]|問題|
|[54522](https://github.com/MicrosoftDocs/azure-docs/issues/54522)|pjmlp|Java サンプルが正しくない。|2020-05-10|[ファイル]|問題|
|[54387](https://github.com/MicrosoftDocs/azure-docs/issues/54387)|ziadhassan7|発音スコアを取得できない|2020-05-08|クローズ|問題|
|[54382](https://github.com/MicrosoftDocs/azure-docs/issues/54382)|jgtellez1|YAML ファイル テンプレート|2020-05-07|クローズ|問題|
|[54208](https://github.com/MicrosoftDocs/azure-docs/issues/54208)|paparush|C# サンプル コードで、ユーザーに発話するように求めるメッセージが表示されない。|2020-05-06|クローズ|問題|
|[54132](https://github.com/MicrosoftDocs/azure-docs/pull/54132)|anthonsu|TTS を v1.3 から v1.4 にアップグレードする|2020-05-05|クローズ|Pull Request|
|[54111](https://github.com/MicrosoftDocs/azure-docs/pull/54111)|anthonsu|カスタム STT の最新バージョンを v2.2.0 に更新する|2020-05-05|クローズ|Pull Request|
|[53919](https://github.com/MicrosoftDocs/azure-docs/issues/53919)|eyast|github プロジェクトへのリンクが壊れている|2020-05-03|[ファイル]|問題|
|[53892](https://github.com/MicrosoftDocs/azure-docs/issues/53892)|viju2008|定義するプロパティ:Android マイクの最大オーディオ認識時間。 15 秒後にオーディオ認識を停止する|2020-05-02|クローズ|問題|
|[53796](https://github.com/MicrosoftDocs/azure-docs/pull/53796)|singhsaumya|カスタム コマンド: ドキュメントの更新|2020-05-01|クローズ|Pull Request|
