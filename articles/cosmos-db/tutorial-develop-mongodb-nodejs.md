---
title: Azure Cosmos DB の MongoDB 用 API を使用する Node.Js、Angular アプリ (パート 1)
description: このチュートリアル シリーズでは、Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成する方法について、動画を交えながら説明しています。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: d3e33878fdd88ebe0582187274278e150b4507fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074337"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用して Angular アプリを作成する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

複数のパートから成るこのチュートリアルでは、Express と Angular を使用して Node.js で新しいアプリを作成した後、[Cosmos DB の MongoDB 用 API を使用して構成された Cosmos アカウント](mongodb-introduction.md)にそれを接続する方法を紹介します。

Azure Cosmos DB は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 SLA によって裏付けられた速度と可用性、自動かつ即時のスケーラビリティ、さまざまな NoSQL エンジンに対応したオープンソース API を備えた最新のアプリを開発できます。

このマルチパート チュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * [Angular CLI で Node.js Express アプリを作成する](tutorial-develop-mongodb-nodejs-part2.md)
> * [Angular で UI を作成する](tutorial-develop-mongodb-nodejs-part3.md)
> * [Azure CLI を使用して Azure Cosmos DB アカウントを作成する](tutorial-develop-mongodb-nodejs-part4.md) 
> * [Mongoose を使って Azure Cosmos DB に接続する](tutorial-develop-mongodb-nodejs-part5.md)
> * [Post、Put、Delete の各関数をアプリに追加する](tutorial-develop-mongodb-nodejs-part6.md)

これと同じアプリを React で作成する方法については、 [React のチュートリアル ビデオ シリーズ](tutorial-develop-mongodb-react.md)をご覧ください。

## <a name="video-walkthrough"></a>ビデオ チュートリアル

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>完全なプロジェクト 

このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="next-steps"></a>次のステップ

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Cosmos DB を使った MEAN.js アプリの作成手順を簡単に説明しました。 

引き続き Node.js Express アプリを作成します。次のチュートリアル パートに進んでください。

> [!div class="nextstepaction"]
> [Angular CLI で Node.js Express アプリを作成する](tutorial-develop-mongodb-nodejs-part2.md)
