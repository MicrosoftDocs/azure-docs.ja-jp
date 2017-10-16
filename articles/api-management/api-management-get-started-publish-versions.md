---
title: "Azure API Management を使用して API の複数のバージョンを公開する | Microsoft Docs"
description: "このチュートリアルの手順に従って、API Management で複数のバージョンを公開する方法を学びます。"
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>予測可能な方法で API の複数のバージョンを公開する
このチュートリアルでは、API の複数のバージョンを設定し、API 開発者がバージョンを呼び出す方法を選択する方法について説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを実行するには、API Management サービスを作成する必要があります。また、(Conference API の代わりに) 変更できる既存の API が必要です。

## <a name="about-versions"></a>バージョンについて
API のすべての呼び出し元がまったく同じバージョンを使用するのは実用的ではない場合があります。 新しい API 機能や異なる API 機能を一部のユーザーに公開し、他のユーザーは現在役立っている API を引き続き使用することを望む場合があります。 呼び出し元は、新しいバージョンにアップグレードするときに、わかりやすい方法でアップグレードできることを求めています。  これは、Azure API Management で**複数のバージョン**を使用することで実現できます。

## <a name="walkthrough"></a>チュートリアル
このチュートリアルでは、バージョン管理スキームとバージョン識別子を選択して、既存の API に新しいバージョンを追加します。

## <a name="add-a-new-version"></a>新しいバージョンを追加する
![API コンテキスト メニュー - バージョンの追加](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Azure Portal で API Management サービス内の **[API]** ページを参照します。
2. API の一覧で **[Conference API]** を選択し、その横のコンテキスト メニュー (**[...]**) をクリックします。
3. **[+ バージョンの追加]** を選択します。

    > [!TIP]
    > 新しい API を最初に作成したときに、複数のバージョンを有効にすることもできます。**[API の追加]** 画面で、**[この API をバージョン管理しますか?]** を選択します。

## <a name="choose-a-versioning-scheme"></a>バージョン管理スキームを選択する
Azure API Management では、呼び出し元が API の必要なバージョンを指定できるようにするための方法を選択できます。 これを行うには、**バージョン管理スキーム**を選択します。 このスキームには、**パス、ヘッダー、クエリ文字列**のいずれかを使用できます。 この例ではパスを使用します。
![[バージョンの追加] 画面](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. **バージョン管理スキーム**として、**[パス]** が選択されたままにしておきます。
2. **バージョン識別子**として **v1** を追加します。

    > [!TIP]
    > バージョン管理スキームとして、**[ヘッダー]** または **[クエリ文字列]** を選択した場合は、追加の値 (ヘッダーの名前またはクエリ文字列パラメーター) を指定する必要があります。

3. 必要に応じて説明を入力します。
4. **[作成]** を選択して、新しいバージョンを設定します。
5. API の一覧の **[Big Conference API]** の下に、**[オリジナル]** と **[v1]** の 2 つの異なる API が表示されます。
![Azure Portal の API の下に表示された複数のバージョン](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > バージョンのない API にバージョンを追加すると、既定の URL で応答する**オリジナル**が常に作成されます。 これにより、バージョンを追加するプロセスによって、既存の呼び出し元が切断されないようにすることができます。 最初に複数のバージョンを有効にして新しい API を作成した場合には、オリジナルは作成されません。

6. **オリジナル**とはまったく別の API として **v1** を編集し、構成することができます。 あるバージョンの変更が別のバージョンに影響することはありません。

## <a name="add-the-version-to-a-product"></a>バージョンを製品に追加する
呼び出し元に新しいバージョンを表示するには、新しいバージョンを**製品**に追加する必要があります (製品は親バージョンから継承されません)。

1. サービス管理ページで **[製品]** を選択します。
2. **[無制限]** を選択します。
3. **[API]** を選択します。
4. **[追加]**を選択します。
5. **[Conference API, Version v1]\(Conference API バージョン v1\)** を選択します。
6. サービス管理ページに戻り、**[API]** を選択します。

## <a name="browse-the-developer-portal-to-see-the-version"></a>開発者ポータルを参照してバージョンを確認する
1. 上部のメニューの **[開発者ポータル]** を選択します。
2. **[API]** を選択します。**[Conference API]** に **[オリジナル]** と **[v1]** の各バージョンが示されていることがわかります。
3. **[v1]** を選択します。
4. 一覧で最初の操作の **[要求 URL]** を確認します。 API の URL パスに "**v1**" が含まれています。
![開発者ポータルに表示されたバージョン](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)
