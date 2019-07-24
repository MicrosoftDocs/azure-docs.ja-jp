---
title: データ エンティティ | Microsoft Docs
description: データ エンティティの概要です。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446580"
---
# <a name="data-entities"></a>データ エンティティ

この記事では、データ エンティティを定義し、その概要を説明します。 データ エンティティの機能、サポートされるシナリオ、データ エンティティに使用されるカテゴリ、およびデータ エンティティを作成する方法に関する情報が含まれています。

## <a name="overview"></a>概要

データ エンティティはデータベース テーブルの物理的な実装から抽象化したものです。 たとえば、正規化テーブルでは、各顧客のデータの多くが顧客テーブルに格納されており、残りは小さな一連の関連テーブルに分散している場合があります。 この場合、顧客概念のデータ エンティティは 1 つの非正規化ビューとして表示され、各行に顧客テーブルと関連するテーブルからのすべてのデータが含まれています。 データ エンティティは、開発と統合を簡単にする形式に、ビジネスの概念をカプセル化します。 データ エンティティの抽象化された性質により、アプリケーションの開発とカスタマイズを単純化できます。 その後は、抽象化によってアプリケーション コードもバージョン間の物理テーブルの避けられないチャーンから隔離されます。

まとめると次のようになります。データ エンティティは、重要なデータの概念および機能を表すために、基礎となるテーブル スキーマの概念的な抽象化とカプセル化 (非正規化ビュー) を提供します。

## <a name="capabilities"></a>機能

データ エンティティには次の機能があります。

- AXD、データのインポート/エクスポート フレームワーク (DIXF) エンティティ、および集計クエリの、分岐して細分化された概念を、1 つの概念で置き換えます。
- ビジネス ロジックをキャプチャして、インポート/エクスポート、統合、およびプログラム可能性などのシナリオを有効にする 1 つのスタックを提供します。
- アプリケーション ライフサイクル管理 (ALM) およびデモ データ シナリオに関するデータ パッケージのエクスポートおよびインポートのための主要なメカニズムになります。
- OData サービスとして公開し、表形式の同期統合シナリオおよび Microsoft Office の統合で使用できます。

詳しくは、「[データ エンティティ](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities)」をご覧ください。
