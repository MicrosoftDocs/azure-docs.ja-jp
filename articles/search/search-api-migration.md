---
title: 最新の Azure Search Service REST API バージョンへのアップグレード - Azure Search
description: API バージョンの違いを確認し、既存のコードを最新の Azure Search Service REST API バージョンに移行するために必要なアクションについて説明します。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 23003859b9a75fb986fe65f5528004f3dd150f9b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633046"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>最新の Azure Search Service REST API バージョンへのアップグレード
[Azure Search サービス REST API](https://docs.microsoft.com/rest/api/searchservice/) の以前のバージョンを使用している場合、この記事を参考にして、次に一般公開される API バージョンの 2017-11-11 にアップグレードできます。

バージョン 2017-11-11 の REST API には、前のバージョンからの変更点がいくつか含まれています。 大半は下位互換性を保つための変更であるため、使用していたバージョンに応じて、コードの変更は最小限で済むはずです。 新しいバージョンの API を使用するようにコードを変更する方法については、「[アップグレードの手順](#UpgradeSteps)」を参照してください。

> [!NOTE]
> Azure Search サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートします。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>バージョン 2017-11-11 の新機能
バージョン 2017-11-11 は、Azure Search サービス REST API の最新の一般公開リリースです。 このバージョンの API には、次の新機能が含まれます。

* [シノニム](search-synonyms.md)。 シノニムの新機能を使用すると、同じ用語を定義し、クエリのスコープを拡張できます。
* [テキスト blob の効率的なインデックス作成をサポートします](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText)。 Azure Blob インデクサーの新しい `text`解析モードは、インデックス作成のパフォーマンスを大幅に向上させます。
* [サービス統計情報 API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics) この新しい API を使用して、Azure Search の現在の使用状況やリソースの制限を表示します。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順
GA バージョン 2015-02-28 または 2016-09-01 からアップグレードする場合、コードの変更は、バージョン番号の変更以外はほぼ必要ありません。 次の状況に該当する場合のみ、コードを変更する必要があります。

* API 応答で認識できないプロパティが返されるとコードが失敗する。 既定では、アプリケーションは、認識できないプロパティを無視する必要があります。
* コードが API 要求を保持し、新しい API バージョンへの再送信を試行する。 たとえば、この状況は、アプリケーションが Search API から返される継続トークンを保持する場合に発生する可能性があります (詳細については、[Search API リファレンス](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)のページの `@search.nextPageParameters` を参照してください)。

このような状況のいずれかが当てはまる場合は、それに応じてコードを変更する必要があります。 それ以外の場合は、バージョン 2017-11-11 の[新機能](#WhatsNew)の使用を開始するのでない限り、変更する必要はありません。

Preview API バージョンからアップグレードする場合も上記の説明が適用されますが、バージョン 2017-11-11 では使用できないプレビュー機能があることに注意する必要があります。

* JSON 配列を含む CSV ファイルと BLOB に対するAzure Blob Storage インデクサーのサポート。
* "More like this" クエリ

コードでこれらの機能を使用している場合は、それらの使用を削除しないと、2017-11-11 にアップグレードすることはできません。

> [!IMPORTANT]
> プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。
> 
> 

## <a name="conclusion"></a>まとめ
Azure Search サービス REST API の使用に関する詳細については、MSDN の最近更新された[API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)を参照してください。

Azure Search に関するご意見をお待ちしております。 問題が発生した場合は、[Azure Search の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)または [StackOverflow](https://stackoverflow.com/)でご質問ください。 StackOverflow で Azure Search に関する質問を行う場合は、必ず `azure-search` タグを付けてください。

Azure Search をお使いいただきありがとうございます。

