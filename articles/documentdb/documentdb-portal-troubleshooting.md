---
title: "DocumentDB ポータルの問題のトラブルシューティング | Microsoft Docs"
description: "DocumentDB の Azure ポータルでの問題を解決する方法を確認します。"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 36ad9bf4-2617-4347-ba29-edebf62fc3ec
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d41cbded31d64525a3e04ef742091c8d87760416


---
# <a name="azure-documentdb-portal-troubleshooting-tips"></a>Azure DocumentDB ポータルのトラブルシューティングのヒント
この記事では、Azure ポータルの DocumentDB の問題を解決する方法について説明します。 

## <a name="resources-are-missing"></a>リソースが不足している
**症状**: データベースまたはコレクションが、ポータルのブレードに表示されません。

**ソリューション**: コレクションのスループットの最大クォータの範囲で動作するように、アプリケーションの使用量を削減します。 

**説明**: ポータルは、他のアプリケーションと同様に、DocumentDB データベースおよびコレクションの呼び出しを行います。 別のアプリケーションから行われる呼び出しのために要求が調整中の場合は、ポータルも調整され、リソースがポータルに表示されなくなる可能性があります。 問題を解決するには、スループットの使用量が高くなる原因に対処して、ポータルのブレードを最新の状態に更新します。 スループットの使用量の測定方法と削減方法の詳細については、[パフォーマンスに関するヒント](documentdb-performance-tips.md)の記事の「[スループット](documentdb-performance-tips.md#throughput)」のセクションを参照してください。

## <a name="pages-or-blades-wont-load"></a>ブレードまたはページが読み込まれない
**症状**: ポータルにページとブレードが表示されません。

**ソリューション**: コレクションのスループットの最大クォータの範囲で動作するように、アプリケーションの使用量を削減します。 

**説明**: ポータルは、他のアプリケーションと同様に、DocumentDB データベースおよびコレクションの呼び出しを行います。 別のアプリケーションから行われる呼び出しのために要求が調整中の場合は、ポータルも調整され、リソースがポータルに表示されなくなる可能性があります。 問題を解決するには、スループットの使用量が高くなる原因に対処して、ポータルのブレードを最新の状態に更新します。 スループットの使用量の測定方法と削減方法の詳細については、[パフォーマンスに関するヒント](documentdb-performance-tips.md)の記事の「[スループット](documentdb-performance-tips.md#throughput)」のセクションを参照してください。

## <a name="add-collection-button-is-disabled"></a>[コレクションの追加] ボタンが無効になっている
**症状**: データベース ブレードの **[コレクションの追加]** ボタンが無効になります。

**説明**: Azure サブスクリプションが、MSDN サブスクリプションから提供される無料クレジットなど、特典クレジットに関連付けられている場合、当月のクレジットの上限まですべて使用すると、DocumentDB に新たにコレクションを作成することができません。

**ソリューション**: アカウントの使用制限を削除します。

1. Azure Portal のジャンプバーで、**[サブスクリプション]** をクリックして、DocumentDB データベースに関連付けられているサブスクリプションをクリックします。次に、**[サブスクリプション]** ブレードで **[管理]** をクリックします。 
    ![DocumentDB には明確に定義された (緩やかな) 一貫性モデルが複数用意されており、その中から 1 つを選択できます。](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. 新しいブラウザー ウィンドウに、クレジットが残っていないことが表示されます。 **[使用制限の削除]** ボタンをクリックして、現在の請求期間または無期限の使用制限を削除します。 ウィザードを終了して、クレジット カード情報を追加または確認します。 
    ![DocumentDB には明確に定義された (緩やかな) 一貫性モデルが複数用意されており、その中から 1 つを選択できます。](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## <a name="query-explorer-completes-with-errors"></a>クエリ エクスプローラーがエラーで終了する
[クエリ エクスプローラーのトラブルシューティング](documentdb-query-collections-query-explorer.md#troubleshoot)に関するトピックをご覧ください。

## <a name="no-data-available-in-monitoring-tiles"></a>使用可能なデータが監視タイルに表示されない
[監視タイルのトラブルシューティング](documentdb-monitor-accounts.md#troubleshooting)に関するトピックをご覧ください。

## <a name="no-documents-returned-in-document-explorer"></a>ドキュメント エクスプローラーでドキュメントが返されない
[ドキュメント エクスプローラーのトラブルシューティング](documentdb-view-json-document-explorer.md#troubleshoot)に関するトピックをご覧ください。

## <a name="next-steps"></a>次のステップ
ポータルで問題が引き続き発生する場合は、電子メールを [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) に送信してサポートを依頼するか、**[参照]**、**[ヘルプとサポート]**、**[サポート リクエストの作成]** の順にクリックして、ポータルからサポート リクエストを送信します。




<!--HONumber=Nov16_HO3-->


