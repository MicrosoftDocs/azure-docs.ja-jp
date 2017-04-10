---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: fba82c5c826da7d1912814b61c5065ca7f726011
ms.openlocfilehash: ad15b1695ad4697aebc38ac39723a1dd1647c466
ms.lasthandoff: 02/23/2017



---
# <a name="how-to-create-a-database-for-documentdb-using-the-azure-portal"></a>Azure ポータルを使用して DocumentDB のデータベースを作成する方法
Microsoft Azure DocumentDB を使用するには、 [DocumentDB アカウント](documentdb-create-account.md)、データベース、コレクション、およびドキュメントを用意する必要があります。 Azure Portal では、コレクションの作成と同時に DocumentDB データベースも作成されるようになりました。 

ポータルで DocumentDB データベースとコレクションを作成する方法については、「[Azure ポータルを使用して DocumentDB コレクションを作成する方法](documentdb-create-collection.md)」を参照してください。

## <a name="other-ways-to-create-a-documentdb-database"></a>DocumentDB データベースを作成するその他の方法
データベースは必ずしもポータルを使用して作成する必要はなく、[DocumentDB SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/mt489072.aspx) を使用して作成することもできます。 .NET SDK を使用してデータベースを操作する方法については、「 [データベースのサンプル](documentdb-dotnet-samples.md#database-examples)」を参照してください。 Node.js SDK を使用してデータベースを操作する方法については、「 [データベースのサンプル](documentdb-nodejs-samples.md#database-examples)」を参照してください。 

## <a name="next-steps"></a>次のステップ
データベースとコレクションを作成できたら、ポータルのドキュメント エクスプローラーを使用して [JSON ドキュメントを追加](documentdb-view-json-document-explorer.md)したり、DocumentDB データ移行ツールを使用してコレクションに[ドキュメントをインポート](documentdb-import-data.md)したり、いずれかの [DocumentDB SDK](documentdb-sdk-dotnet.md) を使用して CRUD 操作を実行できるようになります。 DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。 ドキュメントを作成、削除、更新、削除する方法が紹介されている .NET コード サンプルについては、「 [ドキュメントのサンプル](documentdb-dotnet-samples.md#document-examples)」を参照してください。 Node.js SDK を使用してドキュメントを操作する方法については、「 [ドキュメントのサンプル](documentdb-nodejs-samples.md#document-examples)」を参照してください。 

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](documentdb-sdk-dotnet.md) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#ExecutingSqlQueries)できます。 


