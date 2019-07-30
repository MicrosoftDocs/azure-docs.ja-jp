---
title: Project Acoustics プラグインに関する既知の問題
titlesuffix: Azure Cognitive Services
description: Project Acoustics の Designer Preview の使用時に、以下で説明する既知の問題が発生する場合があります。
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylsto
ms.openlocfilehash: 53df981564eb177da66b86022ecfc80b25f1c763
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296544"
---
# <a name="project-acoustics-known-issues"></a>Project Acoustics の既知の問題
Project Acoustics の Designer Preview の使用時に、以下で説明する既知の問題が発生する場合があります。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>シーンの名前を変更すると音響パラメーターが失われる

シーンの名前を変更する場合、そのシーンに属するすべての音響パラメーターが自動的に新しいシーンに移動するわけではありません。 ただし、古いアセット ファイルにはまだ存在します。 シーン ファイルに隣接する **Editor** ディレクトリ内の **SceneName_AcousticParameters.asset** ファイルを探します。 新しいシーン名を反映するようにファイルの名前を変更します。

## <a name="unity-crashes-when-closing-project"></a>プロジェクトを閉じると Unity がクラッシュする

Unity の最新バージョン (2018.2 以降) には、プロジェクトを閉じると Unity がクラッシュするという既知のバグがあります。 この件は [Unity の問題](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)として調査中です。

## <a name="deploying-to-android-from-some-unity-versions"></a>Unity の一部のバージョンから Android へのデプロイ

Unity の一部のバージョンには、Android へのオーディオ プラグインのデプロイに関するバグがあります。 [こちらのバグ](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)の影響を受けるバージョンを使用していないことを確認してください。

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>"could not find metadata file System.Security.dll"(メタデータ ファイル System.Security.dll が見つかりませんでした) というエラーが表示される

Player の設定で [Scripting Runtime Version] が **[.NET 4.x Equivalent]** に設定されていることを確認して、Unity を再起動します。

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Azure への接続時に認証の問題が発生している

Azure アカウントの正しい資格情報を使用していること、ベイクで要求されるノードの種類をアカウントがサポートしていること、および、システム クロックが正確であることを再確認してください。

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>ベイクをキャンセルすると、[Bake]\(ベイク\) タブは「削除中」状態のままになります。
Project Acoustics は正常な完了時またはキャンセル時にすべての Azure リソースをクリーンアップします。 この処理には最大 5 分かかることがあります。

## <a name="next-steps"></a>次の手順
* [Unity](unity-quickstart.md) または [Unreal](unreal-quickstart.md) サンプル コンテンツを試してください

