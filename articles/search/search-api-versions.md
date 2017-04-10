---
title: "Azure Search の API バージョン | Microsoft Docs"
description: "Azure Search REST API のバージョン ポリシーと .NET SDK のクライアント ライブラリ"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 0458053a-164e-4682-a802-00097ecde981
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 7d45759915f38ba4337b745eb2b28dcbc72dbbe0
ms.openlocfilehash: a14131455ad94cbc4b729077568b12043401c08e
ms.lasthandoff: 01/14/2017

---

# <a name="api-versions-in-azure-search"></a>Azure Search の API バージョン
Azure Search は、機能の更新を定期的にロールアウトします。 このような更新が発生すると、下位互換性を維持するために、API の新しいバージョンの公開が必要になる場合があります (毎回とは限りません)。 新しいバージョンが発行されると、お客様は検索サービスの更新内容をコードに統合するタイミングと方法を管理することができます。

原則として、新しい API バージョンの発行は、必要な場合にのみ行うようにしています。お客様側で新しいバージョンを使用できるようにコードをアップグレードする作業が必要になる可能性があるからです。 下位互換性が保たれなくなる形で API の一部の要素を変更する必要がある場合に限り、新しいバージョンを発行します。 下位互換性は、既存の機能に変更を加えたことや、新機能において既存の API のセキュリティ構成を変更したことが原因で維持できなくなることがあります。

SDK 更新プログラムの場合も同じルールに従っています。 Azure Search SDK は[セマンティック バージョニング](http://semver.org/) ルールに従います。そのため、バージョンにはメジャー、マイナー、およびビルド番号という 3 つの部分があります (たとえば、1.1.0)。 SDK の新しいメジャー バージョンは、下位互換性が保たれなくなる変更が生じた場合にのみリリースされます。 下位互換性が保たれる機能更新の場合は、マイナー バージョンがインクリメントされ、バグ修正の場合は、ビルド バージョンのみがインクリメントされます。

> [!NOTE]
> Azure Search サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートします。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

## <a name="snapshot-of-current-versions"></a>現在のバージョンのスナップショット
Azure Search とのすべてのプログラミング インターフェイスの現行バージョンのスナップショットを次に示します。

| インターフェイス | 最新のメジャー バージョン | 状態 |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |3.0 |一般公開、2016年 11 月にリリース済み |
| [.NET SDK のプレビュー](https://aka.ms/search-sdk-preview) |2.0-preview |2016 年 8 月にリリースのプレビュー |
| [サービス REST API](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |一般公開 |
| [サービス REST API プレビュー](search-api-2015-02-28-preview.md) |2015-02-28-Preview |Preview |
| [.NET 管理 SDK](https://aka.ms/search-mgmt-sdk) |2015-08-19 |一般公開 |
| [管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |一般公開 |

REST API の場合は、各呼び出しに対して `api-version` を含める必要があります。 これにより、容易にプレビュー API などの特定のバージョンを対象にすることができます。 次の例に、 `api-version` パラメーターを指定する方法を示します。

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> 各要求で `api-version` を指定するとしても、すべての API 要求で同じバージョンを使用することをお勧めします。 このことは、特に、新しい API バージョンが、以前のバージョンで認識されない属性または操作を導入している場合に当てはまります。 API バージョンを混在させると、意図しない結果を招くおそれがあるので、お勧めしません。
>
> サービス REST API と管理 REST API は、それぞれ別々にバージョン管理されます。 バージョン番号の類似性は偶然によるものです。

一般公開 (またはGA) の API は、実稼働環境で使用でき、Azure サービス レベル アグリーメントの対象です。 プレビュー バージョンには、必ずしも GA バージョンに移行されるとは限らない実験用の機能が含まれています。 **実稼働アプリケーションではプレビュー API を使用しないことを強くお勧めします。**

## <a name="about-preview-and-generally-available-versions"></a>プレビュー バージョンと一般公開バージョンについて
Azure Search の場合は常に、まず REST API によって、次に .NET SDK のプレリリース版によって実験機能を事前に公開します。

プレビュー機能は必ずしも GA リリースに移行されるとは限りません。 GA バージョンの機能は安定しており、小規模の下位互換性のある修正プログラムと機能強化以外は変更される可能性は少ないですが、プレビュー機能は機能の設計や実装についてのフィードバックを収集する目的で、テストや実験的な目的に使用できます。

ただし、プレビュー機能は変更される可能性があるため、プレビュー バージョンに依存した実稼働コードの作成はお勧めしません。 古いプレビュー バージョンを使用している場合は、一般公開 (GA) バージョンに移行することをお勧めします。

.NET SDK の場合: コード移行のガイダンスについては、 [.NET SDK のアップグレード](search-dotnet-sdk-migration.md)に関する記事を参照してください。

一般的な可用性とは、Azure Search がサービス レベル アグリーメント (SLA) の下で稼働することを意味します。 SLA については、 [Azure Search のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)に関する記事を参照してください。

