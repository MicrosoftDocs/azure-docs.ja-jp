---
title: "Azure Log Analytics でログ クエリを作成および編集するためのポータル | Microsoft Docs"
description: "この記事では、Azure Log Analytics でのログ検索の作成および編集に使用できるポータルについて説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 29dfa31d38f85574f84ed351bc5c26224b1a7e16
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Azure Log Analytics でログ クエリを作成および編集するためのポータル

> [!NOTE]
> この記事では、新しいクエリ言語を使用した Azure Log Analytics のポータルについて説明します。  新しい言語の詳細、およびワークスペースのアップグレード手順については、「[Upgrade your Azure Log Analytics workspace to new log search (新しいログ検索への Azure Log Analytics ワークスペースのアップグレード)](log-analytics-log-search-upgrade.md)」を参照してください。  
>
> ワークスペースが新しいクエリ言語にアップグレードされていない場合は、「[ログ検索を使用してデータを探す](log-analytics-log-searches.md)」で、ログ検索ポータルの現在のバージョンを確認してください。

Log Analytics のさまざまな場所でログ検索を使用して、ワークスペースからデータを取得します。  クエリの実際の作成および編集については、返されたデータを対話的に操作するだけでなく、次の 2 つのオプションを使用することができます。  

## <a name="log-search-portal"></a>ログ検索ポータル
ログ検索ポータルには、Azure Portal または OMS ポータルからアクセスできます。  このポータルは、1 行で作成できる基本的なクエリに適しています。  ログ検索ポータルは、外部ポータルを起動しなくても使用できます。また、このポータルを使用して、警告ルールの作成、コンピューター グループの作成、クエリ結果のエクスポートなど、さまざまな機能をログ検索で実行することができます。  

ログ検索ポータルには、クエリ言語について熟知していなくても使用できるクエリ編集機能が複数用意されています。  こうした機能の概要については、「[ログ検索ポータルを使用して Azure Log Analytics でログ検索を作成する](log-analytics-log-search-log-search-portal.md)」を参照してください。


![ログ検索ポータル](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>高度な分析ポータル
高度な分析ポータルは、ログ検索ポータルで使用できない高度な機能を提供する専用ポータルです。  たとえば、複数行のクエリを編集したり、コードを選択的に実行したりできます。また、状況依存の Intellisense、スマート分析などの機能もあります。  高度な分析ポータルは、ログ検索として保存された、または他の Log Analytics 要素にコピーして貼り付けられた複雑なクエリの設計に最適です。  高度な分析ポータルは、ログ検索ポータル内のリンクから起動します。

![高度な分析ポータル](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


高度な分析ポータルは、高度な機能を備えているため、通常は、主要なクエリ作成および編集ツールとして使用します。  クエリが想定どおりに動作していことを確認したら、そのクエリをコピーして、ログ検索ポータル、ビュー デザイナーなどの他の場所に貼り付けます。  ただし、高度な分析ポータルでは複数行のクエリがサポートされているため、このポータルからクエリをコピーするときは、次の点を考慮する必要があります。

- クエリを他の場所にコピーして貼り付ける前に、コメントを削除する必要があります。  行にコメントを追加するには、先頭に 2 つのスラッシュ (//) を追加します。  複数行のクエリを 1 行に貼り付けるときは、改行を削除します。  コメントが含まれていると、最初のコメントの後のすべての文字が、コメントの一部と見なされます。


## <a name="next-steps"></a>次のステップ

- [ログ検索ポータル](log-analytics-log-search-log-search-portal.md)または[高度な分析ポータル](https://go.microsoft.com/fwlink/?linkid=856587)でのクエリ作成に関するチュートリアルを実行します。
- 新しいクエリ言語を使用した[クエリ記述のチュートリアル](https://go.microsoft.com/fwlink/?linkid=856078)を確認します。

