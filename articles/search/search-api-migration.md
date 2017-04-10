---
title: "Azure Search サービス REST API バージョン 2016-09-01 へのアップグレード | Microsoft Docs"
description: "Azure Search サービス REST API バージョン 2016-09-01 へのアップグレード"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 7d45759915f38ba4337b745eb2b28dcbc72dbbe0
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f

---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Azure Search サービス REST API バージョン 2016-09-01 へのアップグレード
バージョン 2015-02-28 または 2015-02-28-Preview 以前の [Azure Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を使用している場合、この記事を参考にして、次に一般公開される API バージョンの 2016-09-01 にアップグレードできます。

バージョン 2016-09-01 の REST API には、前のバージョンからの変更点がいくつか含まれています。 大半は下位互換性を保つための変更であるため、使用していたバージョンに応じて、コードの変更は最小限で済むはずです。 新しいバージョンの API を使用するようにコードを変更する方法については、「[アップグレードの手順](#UpgradeSteps)」を参照してください。

> [!NOTE]
> Azure Search サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートします。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>バージョン 2016-09-01 の新機能
バージョン 2016-09-01 は、Azure Search サービス REST API の 2 回目の一般公開リリースです。 このバージョンの API には、次の新機能が含まれます。

* [カスタム アナライザー](https://aka.ms/customanalyzers): テキストをインデックス可能で検索可能なトークンに変換するプロセスを制御できます。
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) および [Azure Table Storage](search-howto-indexing-azure-tables.md) インデクサー: Azure Storage から Azure Search へのデータのインポートを、スケジュールに従って、または要求に応じて簡単に実行できます。
* [フィールド マッピング](search-indexer-field-mappings.md): インデクサーが Azure Search にデータをインポートする方法をカスタマイズできます。
* Etag: インデックス、インデクサー、およびデータ ソースの定義を同時実行セーフ方式で更新できます。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順
バージョン 2015-02-28 からアップグレードする場合、コードの変更は、バージョン番号の変更以外はほぼ必要ありません。 次の状況に該当する場合のみ、コードを変更する必要があります。

* API 応答で認識できないプロパティが返されるとコードが失敗する。 既定では、アプリケーションは、認識できないプロパティを無視する必要があります。
* コードが API 要求を保持し、新しい API バージョンへの再送信を試行する。 たとえば、この状況は、アプリケーションが Search API から返される継続トークンを保持する場合に発生する可能性があります (詳細については、[Search API リファレンス](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)のページの `@search.nextPageParameters` を参照してください)。

このような状況のいずれかが当てはまる場合は、それに応じてコードを変更する必要があります。 それ以外の場合は、バージョン 2016-09-01 の[新機能](#WhatsNew)の使用を開始するのでない限り、変更する必要はありません。

バージョン 2015-02-28-Preview からアップグレードする場合も上記の説明が適用されますが、バージョン 2016-09-01 では使用できないプレビュー機能があることに注意する必要があります。

* JSON 配列を含む CSV ファイルと BLOB に対するAzure Blob Storage インデクサーのサポート。
* シノニム
* "More like this" クエリ

コードでこれらの機能を使用している場合は、それらの使用を削除しないと、2016-09-01 にアップグレードすることはできません。

> [!IMPORTANT]
> プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。
> 
> 

## <a name="conclusion"></a>まとめ
Azure Search サービス REST API の使用に関する詳細については、MSDN の最近更新された[API リファレンス](https://msdn.microsoft.com/library/azure/dn798935.aspx)を参照してください。

Azure Search に関するご意見をお待ちしております。 問題が発生した場合は、[Azure Search の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)または [StackOverflow](http://stackoverflow.com/)でご質問ください。 StackOverflow で Azure Search に関する質問を行う場合は、必ず `azure-search` タグを付けてください。

Azure Search をお使いいただきありがとうございます。




<!--HONumber=Jan17_HO2-->


