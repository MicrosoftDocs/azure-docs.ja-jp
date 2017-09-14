---
title: "Azure を対象とした MongoDB、Angular、Node のチュートリアル | Microsoft Docs"
description: "このチュートリアル シリーズでは、Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成する方法について、動画を交えながら説明しています。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: c6d31816b5919afb2cc67bc42321422183ab30df
ms.contentlocale: ja-jp
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db"></a>Angular と Azure Cosmos DB を使って MongoDB アプリを作成する 

複数のパートから成るこのチュートリアルでは、Express、Angular、Node.js (MEAN スタック) を使って記述された新しい [MongoDB API](mongodb-introduction.md) アプリを作成し、Azure Cosmos DB データベースに接続する方法を紹介します。 Azure Cosmos DB は、MongoDB クライアント接続をサポートしています。そのため MongoDB を Azure Cosmos DB に置き換えても、MongoDB アプリに使うコードはそのまま利用できるうえ、さまざまな利点がもたらされます。 Azure Cosmos DB によってもたらされる利点としては、クラウドへの容易なデプロイ、スケーリング、セキュリティのほか、世界規模でレプリケートされるデータ、マルチモデルのサポート、超高速の読み取りと書き込みなどが挙げられます。 

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を活かしたドキュメント データベース、キー/値データベース、グラフ データベースをすばやく作成、照会できます。 

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

