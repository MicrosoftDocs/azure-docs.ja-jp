---
title: "Azure Portal を使用して DocumentDB アカウントを管理する | Microsoft Docs"
description: "Azure ポータルを使用して DocumentDB アカウントを管理する方法について説明します。 Azure ポータルを使用してアカウントを表示、コピー、削除、およびアカウントにアクセスする方法について説明したガイドを紹介します。"
keywords: "Azure ポータル, documentdb, azure, Microsoft azure"
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 3dbf57923e104715d34d1131d16acb6bc6c51637
ms.openlocfilehash: ee1668dce71a1f5d7b91e839670b69c9527c4892
ms.lasthandoff: 02/15/2017


---
# <a name="how-to-manage-a-documentdb-account"></a>DocumentDB アカウントの管理方法
Azure ポータルでの、グローバルな整合性の設定、キーの操作、DocumentDB アカウントの削除の方法について説明します。

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>DocumentDB 整合性の設定を管理する
アプリケーションのセマンティクスに応じて、適切な整合性レベルを選択します。 [整合性レベルを使用した DocumentDB の可用性とパフォーマンスの最大化][consistency]に関するページを参考にして、DocumentDB で利用できる整合性レベルについて把握してください。 DocumentDB の整合性、可用性、パフォーマンスは、お使いのデータベース アカウントで利用できるすべての整合性レベルで保証されます。 データベース アカウントに高い整合性レベルを設定するには、データを&1; つの Azure リージョンに限定し、グローバルで使用できないようにする必要があります。 一方で、低い整合性レベル (制限のある陳腐化、セッション、または最終的) に設定した場合、任意の数の Azure リージョンをデータベース アカウントに関連付けることができます。 次に、データベース アカウントの既定の整合性レベルを選択するための簡単な手順を説明します。 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>DocumentDB アカウントの既定の整合性を指定するには
1. [Azure ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。
2. アカウントのブレードで、 **[既定の整合性]**をクリックします。
3. **[既定の一貫性]** ブレードで、新しい一貫性レベルを選択し、**[保存]** をクリックします。
    ![既定の一貫性セッション][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>アクセス キーを表示、コピー、および再生成する
DocumentDB アカウントを作成すると、2 つのマスター アクセス キーが生成され、DocumentDB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、DocumentDB アカウントを中断することなくキーを再生成できます。 

[Azure Portal](https://portal.azure.com/) で、**[DocumentDB アカウント]** ブレードのリソース メニューにある **[キー]** ブレードにアクセスし、DocumentDB アカウントにアクセスするときに使用するアクセス キーの表示、コピー、再生成を行います。

![Azure ポータルのスクリーン ショット、[キー] ブレード](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> **[キー]** ブレードには、 [データ移行ツール](documentdb-import-data.md)からアカウントに接続する際に使用できるプライマリおよびセカンダリ接続文字列も含まれています。
> 
> 

読み取り専用キーもこのブレードで入手できます。 読み取りとクエリは読み取り専用操作ですが、作成、削除、置換はそうではありません。

### <a name="copy-an-access-key-in-the-azure-portal"></a>Azure ポータルでアクセス キーをコピーする
**[キー]** ブレードで、コピー対象のキーの右側にある **[コピー]** ボタンをクリックします。

![Azure ポータルの [キー] ブレードでアクセス キーを表示およびコピーする](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>アクセス キーを再生成する
接続のセキュリティを高めるために、DocumentDB アカウントのアクセス キーは定期的に変更する必要があります。 片方のアクセス キーで DocumentDB アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは&2; つ割り当てられます。

> [!WARNING]
> アクセス キーを再生成すると、現在のキーに依存するすべてのアプリケーションが影響を受けます。 アクセス キーを使用して DocumentDB アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。
> 
> 

DocumentDB アカウントを使用するアプリケーションまたはクラウド サービスがある場合、キーを再生成すると、キーを切り替えない限り接続が失われます。 次の手順は、キーの切り替えに含まれるプロセスの概要を示します。

1. DocumentDB アカウントのセカンダリ アクセス キーを参照するように、アプリケーション コードのアクセス キーを更新します。
2. DocumentDB アカウントのプライマリ アクセス キーを再生成します。 [Azure ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。
3. **[DocumentDB アカウント]** ブレードで、**[キー]** をクリックします。
4. **[キー]** ブレードで、[再生成] ボタンをクリックしてから **[OK]** をクリックして、新しいキーを生成することを確認します。
    ![アクセス キーを再生成する](./media/documentdb-manage-account/regenerate-keys.png)
5. (再生成してから約 5 分後に) 新しいキーが使用できることを確認したら、新しいプライマリ アクセス キーを参照するようにアプリケーション コードのアクセス キーを更新します。
6. セカンダリ アクセス キーを再生成します。
   
    ![アクセス キーを再生成する](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> 新しく生成されたキーを使用して DocumentDB アカウントにアクセスできるようになるまで、数分かかることがあります。
> 
> 

## <a name="get-the--connection-string"></a>接続文字列を取得する
接続文字列を取得するには、次の操作を行います。 

1. [Azure Portal](https://portal.azure.com)で、DocumentDB アカウントにアクセスします。
2. リソース メニューの **[キー]**をクリックします。
3. **[プライマリ接続文字列]** ボックスまたは **[セカンダリ接続文字列]** ボックスの横にある **[コピー]** ボタンをクリックします。 

[DocumentDB データベース移行ツール](documentdb-import-data.md)で接続文字列を使用する場合は、接続文字列の末尾にデータベース名を追加します。 `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> DocumentDB アカウントを削除する
使用しなくなった DocumentDB アカウントを Azure Portal で削除するには、アカウント名を右クリックし、**[アカウントの削除]** をクリックします。

![Azure ポータルで DocumentDB アカウントを削除する方法](./media/documentdb-manage-account/deleteaccount.png)

1. [Azure ポータル](https://portal.azure.com/)で、削除する DocumentDB アカウントにアクセスします。
2. **[DocumentDB アカウント]** ブレードで、アカウントを右クリックし、**[アカウントの削除]** をクリックします。 
3. 確認ブレードで、DocumentDB アカウント名を入力して、そのアカウントを削除することを確認します。
4. **[削除]** ボタンをクリックします。

![Azure ポータルで DocumentDB アカウントを削除する方法](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>次のステップ
[DocumentDB アカウントの使用方法](http://go.microsoft.com/fwlink/p/?LinkId=402364)について取り上げます。

<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/

