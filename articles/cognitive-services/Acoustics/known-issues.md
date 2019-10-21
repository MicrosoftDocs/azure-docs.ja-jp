---
title: Project Acoustics プラグインの既知の問題
titlesuffix: Azure Cognitive Services
description: Project Acoustics では、次の既知の問題が発生する可能性があります。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243039"
---
# <a name="project-acoustics-known-issues"></a>Project Acoustics の既知の問題
この記事では、Project Acoustics の使用時に発生する可能性がある問題について説明します。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>シーンの名前を変更すると音響パラメーターが失われる

シーンの名前を変更しても、そのシーンに属するすべての音響パラメーターが新しいシーンに自動転送されることはありません。 古いアセット ファイルに残ります。 シーン ファイルに隣にある *Editor* ディレクトリ内で *[SceneName]_AcousticParameters.asset* ファイルを探します。 新しいシーン名を反映するようにファイルの名前を変更します。

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>一部のバージョンの Unity で Android にデプロイする際のバグ

一部のバージョンの Unity では、Android にオーディオ プラグインをデプロイするしくみに[バグ](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)があります。 このバグの影響を受けるバージョンを使用していないことを確認してください。

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"could not find metadata file System.Security.dll" (メタデータ ファイル System.Security.dll が見つかりませんでした) というエラー

**Player** の設定で **[Scripting Runtime Version]** が *[.NET 4.x Equivalent]* に設定されていることを確認し、Unity を再起動します。

## <a name="authentication-problems-when-connecting-to-azure"></a>Azure に接続するときの認証の問題

以下を確認します。
- Azure アカウントに正しい資格情報を使用している。
- お使いのアカウントで、ベイクで要求した種類のノードがサポートされている。
- お使いのシステム クロックが正しく設定されている。

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>キャンセル後も [Bake]\(ベイク\) タブに "削除中" と表示される
Project Acoustics では、正常な完了時またはキャンセル時にすべての Azure リソースがクリーンアップされます。 このプロセスには最大 5 分かかることがあります。

## <a name="next-steps"></a>次の手順
* [Unity](unity-quickstart.md) または [Unreal](unreal-quickstart.md) サンプル コンテンツを試してください。
