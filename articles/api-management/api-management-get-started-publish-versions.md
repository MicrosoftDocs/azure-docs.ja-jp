---
title: Azure API Management を使用して API の複数のバージョンを公開する | Microsoft Docs
description: このチュートリアルの手順に従って、API Management で複数のバージョンを公開する方法を学びます。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b683910180e597cb8cbfa642bb2d9ac3200b42ee
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255032"
---
# <a name="publish-multiple-versions-of-your-api"></a>API の複数のバージョンを発行する 

API のすべての呼び出し元がまったく同じバージョンを使用するのは実用的ではない場合があります。 呼び出し元は、新しいバージョンにアップグレードするときに、わかりやすい方法でアップグレードできることを求めています。 これは、Azure API Management で**複数のバージョン**を使用することで実現できます。 詳細については、「[Versions & Revisions (バージョンとリビジョン)](https://azure.microsoft.com/blog/versions-revisions/)」を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 既存の API に新しいバージョンを追加する
> * バージョン スキームを選択する
> * バージョンを製品に追加する
> * 開発者ポータルを参照してバージョンを確認する

![開発者ポータルに表示されたバージョン](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
+ また、「[Import and publish your first API (最初の API をインポートして発行する)](import-and-publish.md)」のチュートリアルも完了します。

## <a name="add-a-new-version"></a>新しいバージョンを追加する

![API コンテキスト メニュー - バージョンの追加](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. API の一覧で **[Demo Conference API]\(デモ会議 API\)** を選択します。
2. その横のコンテキスト メニュー ( **[...]** ) を選択します。
3. **[+ バージョンの追加]** を選択します。

> [!TIP]
> 新しい API を最初に作成したときに、複数のバージョンを有効にすることもできます。 **[API の追加]** 画面で、 **[この API をバージョン管理しますか?]** を選択します。

## <a name="choose-a-versioning-scheme"></a>バージョン管理スキームを選択する

Azure API Management では、呼び出し元が API の必要なバージョンを指定できるようにするための方法を選択できます。 使用する API バージョンは、**バージョン管理スキーム**を選択することで指定します。 このスキームには、**パス、ヘッダー、クエリ文字列**のいずれかを使用できます。 次の例では、パスを使用してバージョン管理スキームを選択します。

![[バージョンの追加] 画面](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. **バージョン管理スキーム**として、 **[パス]** が選択されたままにしておきます。
2. **[名前]** フィールドに「**demo-conference-api-v1**」と入力します。

    > [!NOTE]
    > バージョンは、実際には API のリビジョンに基づく新しい API です。 **[名前]** は新しい API の名前であり、API Management インスタンス全体で一意である必要があります。

3. **[バージョン ID]** フィールドに「**v1**」と入力します。

    > [!TIP]
    > バージョン管理スキームとして **[ヘッダー]** または **[クエリ文字列]** を選択した場合は、追加の値 (ヘッダーの名前またはクエリ文字列パラメーター) を指定する必要があります。

4. **[作成]** を選択して、新しいバージョンを設定します。
5. API の一覧の **[Demo Conference API]** の下に、 **[オリジナル]** と **[v1]** の 2 つの異なる API が表示されます。

    ![Azure Portal の API の下に表示された複数のバージョン](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > バージョンのない API にバージョンを追加すると、既定の URL で応答する**オリジナル**が自動的に作成されます。 これにより、バージョンを追加するプロセスによって、既存の呼び出し元が切断されないようにすることができます。 最初に複数のバージョンを有効にして新しい API を作成した場合には、オリジナルは作成されません。

6. **オリジナル**とは別の API として **v1** を編集し、構成することができます。 あるバージョンの変更が別のバージョンに影響することはありません。

## <a name="add-the-version-to-a-product"></a>バージョンを製品に追加する

呼び出し元が新しいバージョンを表示するためには、それが**製品**に追加されている必要があります。

![API Management 製品](media/api-management-getstarted-publish-versions/08-AddMultipleVersions-03-AddVersionToProduct.png)

1. クラシック デプロイ モデル ページから **[製品]** を選択します。
2. **[無制限]** を選択します。
3. **[API]** を選択します。
4. **[追加]** を選択します。
5. **[Demo Conference API, Version v1]\(Demo Conference API バージョン v1)** を選択します。
6. **[選択]** をクリックします。

## <a name="browse-the-developer-portal-to-see-the-version"></a>開発者ポータルを参照してバージョンを確認する

1. 上部のメニューから **[開発者ポータル]** を選択します。
2. **[API]** を選択し、 **[Demo Conference API]\(デモ会議 API\)** をクリックします。
3. API 名の横に複数のバージョンのドロップダウンが表示されます。
4. **[v1]** を選択します。
5. 一覧で最初の操作の **[要求 URL]** を確認します。 API の URL パスに "**v1**" が含まれています。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 既存の API に新しいバージョンを追加する
> * バージョン スキームを選択する 
> * バージョンを製品に追加する
> * 開発者ポータルを参照してバージョンを確認する

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [開発者ポータルのページのスタイルをカスタマイズする](api-management-customize-styles.md)
