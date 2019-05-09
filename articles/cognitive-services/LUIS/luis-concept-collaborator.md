---
title: コラボレーション
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS アプリには、1 人の所有者が必要です。さらに任意でコラボレーターを追加すれば、複数のユーザーで 1 つのアプリを作成することができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 34ae47723762215fdea99362419c3875dc2e5942
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522747"
---
# <a name="collaborating-with-other-authors"></a>他の作成者との共同作業

LUIS アプリには、1 人の所有者が必要です。さらに任意でコラボレーターを追加すれば、複数のユーザーで 1 つのアプリを作成することができます。

## <a name="luis-account"></a>LUIS アカウント
LUIS アカウントは、1 つの [Microsoft Live](https://login.live.com/) アカウントに関連付けられています。 各 LUIS アカウントには、そのアカウントがアクセスできるすべての LUIS アプリの作成に使用する、無料の[オーサリング キー](luis-concept-keys.md#authoring-key)が与えられます。 

LUIS アカウントは、多くの LUIS アプリを所有できます。

Active Directory ユーザー アカウントの詳細については、「[Azure Active Directory テナント ユーザー](luis-how-to-collaborate.md#azure-active-directory-tenant-user)」を参照してください。 

## <a name="luis-app-owner"></a>LUIS アプリ所有者

アプリを作成するアカウントが所有者です。アプリごとに 1 人の所有者がいます。 所有者は、アプリの **[[設定]](luis-how-to-collaborate.md)** ページに表示されます。 所有者は、エンドポイント クォータが毎月の制限の 75% に達したときにアプリの受信メールを削除できます。 

## <a name="authorization-roles"></a>承認ロール
LUIS では、1 つの例外を除き、所有者とコラボレーターの異なるロールをサポートしていません。 所有者はアプリを削除できる唯一のアカウントです。

モデルへのアクセスを制御することに関心がある場合は、より小さな LUIS アプリにモデルを細分化し、より限定的なコラボレーターのセットをそれぞれの小さなアプリに割り当てることを検討してください。 [ディスパッチ](https://aka.ms/dispatch-tool)を使用して、親の LUIS アプリが親子アプリ間の協調を管理できるようにします。

## <a name="transfer-ownership"></a>所有権の移転
LUIS には所有権の移転の機能はありませんが、コラボレーターはアプリをエクスポートしてからインポートすることで、アプリを作成できます。 新しいアプリには別のアプリ ID が付きます。 新しいアプリをトレーニングして公開する必要があります。また、使用する新しいエンドポイントが必要です。

## <a name="luis-app-collaborators"></a>LUIS アプリ コラボレーター
アプリの所有者は、コラボレーターをアプリに追加できます。 所有者は、アプリのコラボレーターの電子メール アドレスを **[[設定]](luis-how-to-collaborate.md)** に追加する必要があります。 コラボレーターには、アプリへのフル アクセス権が付与されます。 コラボレーターがアプリを削除した場合、そのアプリはこのコラボレーターのアカウントから削除されますが、所有者のアカウントには残ります。 

コラボレーターと複数のアプリを共有する場合は、それぞれのアプリにコラボレーターの電子メールを追加する必要があります。 

## <a name="managing-multiple-authors"></a>複数の作成者の管理
[LUIS](luis-reference-regions.md#luis-website) Web サイトでは現在、トランザクション レベルの作成は提供されていません。 基本バージョンから独立したバージョンで作業することを作成者に許可できます。 次のセクションで、2 種類の方法について説明します。

## <a name="manage-multiple-versions-inside-the-same-app"></a>同じアプリ内で複数のバージョンを管理する
まず、各作成者用に、基本バージョンから[複製](luis-how-to-manage-versions.md#clone-a-version)します。 

各作成者は、アプリの自分のバージョンに変更を加えます。 各作成者は自分のモデルに満足したら、新しいバージョンを JSON ファイルにエクスポートします。  

エクスポートされたアプリは JSON 形式のファイルなので、変更点を比較することができます。 ファイルを結合して、新しいバージョンの JSON ファイルを 1 つ作成します。 JSON の **versionId** プロパティを変更して、マージされた新しいバージョンを示します。 このバージョンを元のアプリにインポートします。 

この方法に従うと、アクティブなバージョンが 1 つ、ステージング バージョンが 1 つ、公開済みのバージョンが 1 つ存在することになります。 この 3 種類のバージョンの結果を、対話型のテスト ウィンドウでを比較できます。

## <a name="manage-multiple-versions-as-apps"></a>複数のバージョンをアプリとして管理する
基本バージョンを[エクスポート](luis-how-to-manage-versions.md#export-version)します。 各作成者がこのバージョンをインポートします。 アプリをインポートするユーザーが、バージョンの所有者です。 アプリの変更が完了したら、バージョンをエクスポートします。 

エクスポートされたアプリは JSON 形式ファイルなので、変更点を基本のエクスポートと比較することができます。 ファイルを結合して、新しいバージョンの JSON ファイルを 1 つ作成します。 JSON の **versionId** プロパティを変更して、マージされた新しいバージョンを示します。 このバージョンを元のアプリにインポートします。

## <a name="collaborator-roles-vs-entity-roles"></a>コラボレーター ロールとエンティティのロール

[エンティティのロール](luis-concept-roles.md)は、LUIS アプリのデータ モデルに適用されます。 コラボレーター ロールは、オーサリング アクセス許可のレベルに適用されます。 

## <a name="next-steps"></a>次の手順

[バージョン管理](luis-concept-version.md)の概念を理解します。 

[アプリ設定](luis-how-to-collaborate.md)を参照して、LUIS アプリでコラボレーターを管理する方法を学習します。

オーサリング API を使用して[メールをアクセス リストに追加する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342)方法を確認します。
