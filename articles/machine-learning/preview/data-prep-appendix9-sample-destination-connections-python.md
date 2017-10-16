---
title: "Azure Machine Learning データ準備で可能な変換先/出力先の例 | Microsoft Docs"
description: "このドキュメントでは、Azure ML データ準備でのカスタム データ変換先/出力先の例を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-destination-connections-python"></a>変換先接続のサンプル (Python) 
この付録を読む前に、[Python 機能拡張の概要](data-prep-python-extensibility-overview.md)に関する記事を参照してください


### <a name="write-to-excel"></a>Excel への書き込み 


Excel に書き込むには、追加ライブラリが必要です。新しいライブラリの追加については、機能拡張の概要に記載されています。 `openpyxl` は追加する必要のあるライブラリです。

書き込む前に、他のいくつかの変更が必要になる場合があります。 データ準備で使用されるデータ型には、変換先の一部の形式でサポートされていないものがあります。 たとえば、"Error" オブジェクトが存在する場合、これらは正しく Excel にシリアル化されません。 したがって、Excel への書き込みを試みる前に、すべての列からエラーを削除する [Replace Error Values] (エラー値の置換) 変換を行う必要があります。

上記の作業がすべて終了していれば、次の行により、Excel ドキュメントの単一のシートにデータ テーブルが書き込まれます。 [Add a Write DataFlow (Script)] (データフローの書き出し (スクリプト)) 変換を追加して、次のコードを式セクションに入力します。


#### <a name="on-windows"></a>Windows の場合 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>macOS/OS X の場合 ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
