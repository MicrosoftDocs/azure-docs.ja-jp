---
title: "DocumentDB: MongoDB 用 API とは | Microsoft Docs"
description: "DocumentDB: MongoDB 用 API について、また Azure クラウドで既存の MongoDB アプリケーションを簡単に実行する方法について説明します"
keywords: "MongoDB とは"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f173fa709f2a7a21042752ba4b5ac936d01fe300
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>DocumentDB: MongoDB 用 API とは

MongoDB 向けに作成されたアプリのデータ ストアとして DocumentDB データベースを使用できるようになりました。 つまり、MongoDB データベースの既存の[ドライバー](https://docs.mongodb.org/ecosystem/drivers/)を使用することで、MongoDB 用に記述されたアプリケーションが DocumentDB と通信し、MongoDB データベースではなく DocumentDB データベースを使用できるようになりました。 多くの場合、接続文字列を変更するだけで、MongoDB から DocumentDB に使用を切り替えることができます。 この機能を使用すると、既にある MongoDB のスキルとツールを無駄にすることなく、DocumentDB の完全に管理されたスケーラブルな NoSQL データベースを利用する MongoDB データベース アプリケーションを簡単に作成し、Azure クラウドで実行することができます。

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>DocumentDB: MongoDB 用 API を使用するメリット
**サーバー管理が不要** - DocumentDB は完全に管理されたサービスです。つまり、インフラストラクチャまたは仮想マシンを自分で管理する必要がありません。 DocumentDB は、30 以上の [Azure リージョン](https://azure.microsoft.com/regions/services/)で使用できます。

**無限のスケール** - スループットとストレージを分けてエラスティックにスケールできます。 容量を追加して&1; 秒あたり数百万件の要求に簡単に対応できます。

**エンタープライズ グレード** - DocumentDB では、ローカルとリージョンで障害が発生した場合に 99.99% の可用性とデータ保護を実現する、複数のローカル レプリカがサポートされます。 DocumentDB には、エンタープライズ グレードの[コンプライアンス認定](https://www.microsoft.com/trustcenter)とセキュリティ機能があります。 

**MongoDB の互換性** - DocumentDB: MongoDB 用 API は、MongoDB との互換性が確保されています。 既存のコード、アプリケーション、ドライバー、ツールを使用して DocumentDB を操作できます。 

詳細については、Scott Hanselman と DocumentDB プリンシパル エンジニアリング マネージャー Kirill Gavrylyuk による次の Azure Friday ビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>開始方法
[Azure Portal](https://portal.azure.com) で DocumentDB: MongoDB 用 API アカウントを作成し、接続を新しいアカウントにスワップします。 

"*これで終了です。*"

詳細な手順については、[アカウントの作成](documentdb-create-mongodb-account.md)に関する記事と[アカウントへの接続](documentdb-connect-mongodb-account.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

DocumentDB に関する情報: MongoDB 用 API は、DocumentDB ドキュメント全体に組み込まれていますが、開始するためのいくつかのヒントを次に示します。
* [MongoDB アカウントへの接続](documentdb-connect-mongodb-account.md)に関するチュートリアルに従って、アカウントの接続文字列の情報を取得する方法について学習します。
* [DocumentDB で MongoChef を使用](documentdb-mongodb-mongochef.md)に関するチュートリアルに従って、MongoChef で DocumentDB データベースと MongoDB アプリの接続を作成する方法について学習します。
* [MongoDB に対するプロトコルのサポートを使用した DocumentDB へのデータの移行](documentdb-mongodb-migrate.md) に関するチュートリアルに従って、データを MongoDB データベース用 API にインポートします。
* [Node.js](documentdb-mongodb-samples.md) を使用して、最初の MongoDB 用 API アプリを構築します。
* [NET](documentdb-mongodb-application.md) を使用して、最初の MongoDB 用 API Web アプリを構築します。
* [Robomongo](documentdb-mongodb-robomongo.md) を使用して、MongoDB 用 API アカウントに接続します。
* [GetLastRequestStatistics コマンドと Azure Portal のメトリック](documentdb-request-units.md#GetLastRequestStatistics)を使用して、操作で使用している RU の数を確認する方法について学習します。
* [グローバル分散アプリの読み取り設定を構成する](documentdb-distribute-data-globally.md#ReadPreferencesAPIforMongoDB)方法について学習します。


