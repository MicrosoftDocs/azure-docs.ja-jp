---
title: バージョン管理
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS のバージョンは、従来のプログラミングのバージョンと似ています。 各バージョンは、アプリのある時点のスナップショットです。 アプリを変更する前に、新しいバージョンを作成してください。 特定のアプリに戻り、アプリの意図と発話を前の状態に戻してみることが簡単になります。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: diberry
ms.openlocfilehash: 9da79e5b744f8ba70c0e265f0d1f0126b37eba49
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870654"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>LUIS バージョンを使用する方法とタイミング

LUIS のバージョンは、従来のプログラミングのバージョンと似ています。 各バージョンは、アプリのある時点のスナップショットです。 アプリを変更する前に、新しいバージョンを作成してください。 特定のバージョンに戻り、意図と発話を削除し、前の状態に戻してみるとより簡単です。

[バージョン](luis-how-to-manage-versions.md)を使用して同じアプリの別モデルを作成します。 

## <a name="version-id"></a>バージョン ID
バージョン ID は、文字、数字、または '.' で構成します。文字数は 10 文字までです。

## <a name="initial-version"></a>初期バージョン
初期バージョン (0.1) は、既定のアクティブなバージョンです。 

## <a name="active-version"></a>アクティブなバージョン
アクティブとして[バージョンを設定する](luis-how-to-manage-versions.md#set-active-version)ことは、[LUIS](luis-reference-regions.md) Web サイトでそのバージョンが現在編集およびテストされていることを意味します。 データにアクセスし、バージョンを更新、テスト、および公開するには、バージョンをアクティブとして設定します。

現在アクティブなバージョンの名前は、左上のパネルのアプリ名の後に表示されます。 

[![アクティブなバージョンの変更](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>バージョンと公開スロット
ステージング スロットと運用スロットのどちらかに公開します。 スロットごとで、別のバージョンを公開することも、同じバージョンを公開することもできます。 これは、エンドポイントを介してモデルのバージョン間の変更を確認するときに役立ち、ボットや他の LUIS 呼び出し元アプリケーションで使用できます。 

## <a name="clone-a-version"></a>バージョンを複製する
バージョンを複製して既存のバージョンのコピーを作成し、新規バージョンとして保存します。 新規バージョンの開始点として、既存のバージョンと同じコンテンツを使用するバージョンを複製します。 バージョンを複製すると、新規バージョンが**アクティブ**なバージョンになります。 

## <a name="import-and-export-a-version"></a>バージョンのインポートとエクスポート
バージョンは、アプリ レベルでインポートできます。 そのバージョンがアクティブなバージョンになり、アプリ ファイルの "versionId" プロパティでそのバージョン ID で使用されます。 バージョン レベルで既存のアプリにインポートすることもできます。 新規バージョンがアクティブなバージョンになります。 

アプリ レベルやバージョン レベルでバージョンをエクスポートできます。 唯一の違いは、アプリ レベルでエクスポートされるバージョンは現在アクティブなバージョンであるのに対して、バージョン レベルでは、**[[設定]](luis-how-to-manage-versions.md)** ページで任意のバージョンを選択してエクスポートできます。 

アプリはインポート後に再度トレーニングされるため、エクスポートしたファイルには機械学習された情報が含まれません。 エクスポートしたファイルにはコラボレーターは含まれません。新しいアプリにそのバージョンをインポートした後、コラボレーターを追加し直す必要があります。

## <a name="export-each-version-as-app-backup"></a>各バージョンをアプリのバックアップとしてエクスポートする
LUIS アプリをバックアップするために、**[[設定]](luis-how-to-manage-versions.md)** ページで各バージョンをエクスポートします。

## <a name="delete-a-version"></a>バージョンを削除する
[設定] ページのバージョンの一覧から、アクティブなバージョンを除くすべてのバージョンを削除できます。 

## <a name="version-availability-at-the-endpoint"></a>エンドポイントでのバージョンの可用性
トレーニング済みのバージョンは、アプリの[エンドポイント](luis-glossary.md#endpoint)で自動的に利用できるわけではありません。 バージョンをアプリのエンドポイントで利用可能にするには、[公開](luis-how-to-publish-app.md)または再公開する必要があります。 **ステージング**と**運用**に公開できるので、エンドポイントで使用できるアプリのバージョンは最大 2 つです。 エンドポイントで使用できるアプリのバージョンがさらに必要な場合は、バージョンをエクスポートして、新しいアプリに再度インポートします。 新しいアプリには別のアプリ ID が付きます。

## <a name="collaborators"></a>コラボレーター
所有者とすべての[コラボレーター](luis-how-to-collaborate.md)が、アプリのすべてのバージョンへのフル アクセス権を持ちます。

## <a name="next-steps"></a>次の手順

アプリの設定ページで[バージョン](luis-how-to-manage-versions.md)を管理する方法を確認します。 

モデルに[意図](luis-concept-intent.md)を設計する方法を学習します。
