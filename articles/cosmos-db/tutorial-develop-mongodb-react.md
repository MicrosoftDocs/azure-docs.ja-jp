---
title: Azure を対象とした MongoDB、React、Node.js のチュートリアル
description: このチュートリアル シリーズでは、React と Node.js で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成する方法について、動画を交えながら説明しています。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jopapa
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 1425b89e42450123c1696ddcee4458e1f69b8a6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348570"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>React と Azure Cosmos DB を使って MongoDB アプリを作成する  
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

このビデオ チュートリアルでは、React フロントエンドでヒーローの追跡アプリを作成する方法を複数のパートにわたって紹介しています。 このアプリは、サーバーに Node と Express を使用し、[Azure Cosmos DB の MongoDB 用 API](mongodb-introduction.md) で構成された Cosmos データベースに接続した後、アプリのサーバー部分に React フロントエンドを接続するものです。 このチュートリアルでは、Azure portal からポイントアンドクリック方式で Cosmos DB をスケーリングする方法や、アプリをインターネットにデプロイしてだれでもお気に入りのヒーローを追跡できるようにする方法も紹介しています。 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) は MongoDB とのワイヤ プロトコル互換性をサポートしているので、クライアントは MongoDB の代わりに Azure Cosmos DB を使用できます。  

このマルチパート チュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * はじめに
> * プロジェクトを設定する
> * React で UI を作成する
> * Azure Portal を使用して Azure Cosmos DB アカウントを作成する
> * Mongoose を使って Azure Cosmos DB に接続する
> * React、Create、Update、Delete の各操作をアプリに追加する

これと同じアプリを Angular で作成する方法については、 [Angular のチュートリアル ビデオ シリーズ](tutorial-develop-mongodb-nodejs.md)をご覧ください。

## <a name="prerequisites"></a>前提条件
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>完全なプロジェクト
完成したアプリケーションは、[GitHub から](https://github.com/Azure-Samples/react-cosmosdb)ダウンロードできます。

## <a name="introduction"></a>はじめに 

この動画では、Burke Holland が Azure Cosmos DB の概要を説明し、このビデオ シリーズで作成するアプリについて簡単に紹介しています。 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>プロジェクトの設定

この動画では、Express と React を同じプロジェクトで設定する方法を紹介します。 その後、プロジェクトに含まれるコードを実際に紹介しながら Burke が解説します。

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>UI の作成

この動画では、アプリケーションのユーザー インターフェイス (UI) を React で作成する方法を紹介します。 

> [!NOTE]
> この動画で言及されている CSS は、[react-cosmosdb GitHub リポジトリ](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css)にあります。

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB への接続

この動画では、Azure Portal から Azure Cosmos DB アカウントを作成して MongoDB と Mongoose のパッケージをインストールし、Azure Cosmos DB 接続文字列を使って、新しく作成したアカウントにアプリを接続する方法を紹介します。 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>アプリでのヒーローの読み取りと作成

この動画では、Cosmos データベースにヒーローを作成したり、ヒーローを読み取ったりする方法のほか、Postman と React UI でそれらのメソッドをテストする方法について紹介します。 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>アプリでのヒーローの削除と更新

この動画では、アプリからヒーローを削除したり更新したりする方法のほか、更新した結果を UI に表示する方法について取り上げます。 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>アプリの仕上げ

いよいよアプリの仕上げに入ります。この動画で、UI をバックエンド API に接続する方法をご覧ください。 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリの使用を続けない場合は、以下の手順に従い、このチュートリアルで作成したすべてのリソースを Azure Portal で削除してください。 

1. Azure Portal の左側のメニューで、 **[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * React、Node、Express、Azure Cosmos DB を使ってアプリを作成する 
> * Azure Cosmos DB アカウントを作成する
> * Azure Cosmos DB アカウントにアプリを接続する
> * Postman を使ってアプリをテストする
> * アプリケーションを実行してヒーローをデータベースに追加する

次のチュートリアルに進み、MongoDB のデータを Azure Cosmos DB にインポートする方法を学習しましょう。  

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
