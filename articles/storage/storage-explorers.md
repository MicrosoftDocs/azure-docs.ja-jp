---
title: "Azure Storage 向けのツール | Microsoft Docs"
description: "Azure Storage のデータの表示/操作を可能にするツールの一覧。"
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: e4748642-98c4-437e-b0ed-4f9641c2e894
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 3203358dce9cba95d325ec786e7ba12dd45f5ca1
ms.openlocfilehash: eb7e798120ea9075dba92da76826ff621882c32a
ms.lasthandoff: 01/24/2017


---
# <a name="azure-storage-client-tools"></a>Azure Storage クライアント ツール
Azure Storage のユーザーのご要望に多いのが、Azure Storage クライアント ツールを使用したデータの表示や処理についてです。 次の表に、これを実現するツールの一覧を示します。 データ抽象化を列挙できる場合、またはデータ抽象化にアクセスできる場合は、各ブロックに “X” を入力しました。 ツールの有料/無料も記載されています。 “試用版” とは、無料試用版があることを示します。ただし、完全な製品は有料です。 “Y/N” は、バージョンの有料/無料を示します。

利用できる Azure Storage クライアント ツールのスナップショットのみを提供しました。 これらのツールは開発が続いており、機能が追加されることがあります。 修正や更新がございましたら、コメントを投稿してお知らせください。 ここに記載するべきツールがある場合もお知らせください。

**Microsoft Azure Storage クライアント ツール**

<table>
  <tr>
    <th rowspan="2">Azure Storage クライアント ツール</th>
    <th rowspan="2">ブロック BLOB</th>
    <th rowspan="2">ページ BLOB</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">テーブル</th>
    <th rowspan="2">キュー</th>
    <th rowspan="2">ファイル</th>
    <th rowspan="2">無料</th>
    <th colspan="4">プラットフォーム</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>Y</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Microsoft Azure ストレージ エクスプローラー</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio サーバー エクスプローラー</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
</table>

**サードパーティ製 Azure Storage クライアント ツール**

Microsoft では次のサードパーティ製ツールが主張する機能と品質を検証していません。このリストは Microsoft の推薦を暗に示すものではありません。

<table>
  <tr>
    <th rowspan="2">Azure Storage クライアント ツール</th>
    <th rowspan="2">ブロック BLOB</th>
    <th rowspan="2">ページ BLOB</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">テーブル</th>
    <th rowspan="2">キュー</th>
    <th rowspan="2">ファイル</th>
    <th rowspan="2">無料</th>
    <th colspan="4">プラットフォーム</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cloudportam.com/">Cloud Portam</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>試用版</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>試用版</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-explorer/introduction">Cerabrata: Azure Explorer</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td>○</td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://azurestorageexplorer.codeplex.com/">Azure Storage Explorer</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx">CloudBerry Explorer</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
    <td>○</td>
    <td>Y/N</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>試用版</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer、CloudXplorer、TableXplorer</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>試用版</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.codeplex.com/">Azure Web Storage Explorer</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>Y</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://zudio.co/">Zudio</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>試用版</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>

