---
title: Azure の詳細な使用状況を理解する | Microsoft Docs
description: Azure サブスクリプションの詳細な使用状況 CSV の各セクションを読み、理解する方法について説明します
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: a143fc6d9dbd78ae365f943a00ac9f8492d5e51c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897148"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Microsoft Azure の詳細な利用料金の用語を理解する 

詳細な利用料金 CSV ファイルには、現在の請求期間を対象に毎日測定された利用料金が含まれています。 

詳細な使用状況ファイルを取得するには、「[Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)」をご覧ください。
このファイルは、スプレッドシート アプリケーションで開くことができるコンマ区切り値 (.csv) ファイル形式で入手できます。 使用可能なバージョンが 2 つ表示される場合は、バージョン 2 をダウンロードします。 こちらが最新のファイル形式になります。

利用料金は、サブスクリプションの**月額**料金の合計です。 利用料金では、クレジットや割引は考慮されません。

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>詳細な使用状況ファイルの用語と説明 (詳細)

次のセクションでは、バージョン 2 の詳細な使用状況ファイルに表示される重要な用語について説明します。

### <a name="statement"></a>ステートメント

詳細な使用状況 CSV ファイルの最上部のセクションには、月の請求期間で使用したサービスが表示されます。 次の表では、このセクションに表示される用語と説明の一覧を示します。

| 用語 | 説明 |
| --- | --- |
|請求期間 |メーターが使用された請求期間 |
|測定カテゴリ |使用状況の最上位レベルのサービスを示します |
|測定サブカテゴリ |Azure サービスの種類を定義します (この定義は料金に影響することがあります) |
|測定名 |消費しているメーターの測定単位を示します |
|測定リージョン |データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します |
|SKU |各 Azure メーターの一意のシステム ID を示します |
|単位 |サービスが課金される単位を特定します。 たとえば、GB、時間数、10,000 単位などです。 |
|消費量 |請求期間中に使用されたメーターの量 |
|含まれる量 |現在の請求期間内に無料で含まれるメーターの量 |
|超過量 |消費量と含まれる量の差が表示されます。 この差異について課金されます。 プランに含まれる量がない従量課金制では、この合計は消費量と同じになります。 |
|コミットメント以内 |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額から差し引かれるメーター料金が表示されます。 メーター料金は発生順にコミットメント額から差し引かれます。 |
|通貨 |現在の請求期間で使用する通貨 |
|超過料金 |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額を超えたメーター料金が表示されます |
|コミットメント レート |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額の合計に基づいてコミットメント レートが表示されます |
|料金 |課金対象項目ごとに請求される価格 |
|値 |[超過量] 列に [単価] 列を掛けた料金を示します。 消費量が含まれる量を超過していない場合、この列の料金は発生しません。 |

### <a name="daily-usage"></a>毎日の使用状況

CSV ファイルの毎日の使用状況セクションには、請求金額に影響する使用状況の詳細が表示されます。 次の表では、このセクションに表示される用語と説明の一覧を示します。

| 用語 | 説明 |
| --- | --- |
|使用日 |メーターが使用された日付 |
|測定カテゴリ |この使用量の対象となる最上位レベルのサービスを示します |
|測定 ID |課金測定の識別子。課金データの料金計算に使用されます |
|測定サブカテゴリ |料金に影響する Azure サービスの種類を定義します |
|測定名 |消費しているメーターの測定単位を示します |
|測定リージョン |データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します |
|単位 |メーターが課金される単位を示します。 たとえば、GB、時間数、10,000 単位などです。 |
|消費量 |その日に消費されたメーターの量 |
|リソースの場所 |メーターが実行されているデータ センターを示します |
|使用サービス |使用した Azure プラットフォーム サービス |
|リソース グループ |デプロイされたメーターが実行されるリソース グループ。 <br/><br/>詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」を参照してください。 |
|インスタンス ID | メーターの識別子。 <br/><br/> 識別子には、メーターの作成時に指定した名前が含まれています。 リソース名または完全修飾リソース ID のいずれかです。 詳細については、[Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources) に関するページを参照してください。 |
|Tags | ユーザーがメーターに割り当てるタグです。 タグは、課金記録のグループ化に使用できます。<br/><br/>たとえば、タグを使用して、メーターを使用する部門ごとにコストを配分することができます。 タグの生成をサポートするサービスは、仮想マシン、記憶域、および [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources) を使用してプロビジョニングされたネットワーク サービスです。 詳細については、[タグを使用した Azure リソースの整理](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)に関するページを参照してください。 |
|追加情報 |サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。 |
|サービス情報 1 |サブスクリプションでサービスが属しているプロジェクトの名前 |
|サービス情報 2 |これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>詳細な使用状況ファイルの料金が正しいことを確認する方法
詳細な使用状況ファイルに詳細を確認したい料金がある場合は、「[Microsoft Azure の請求書を理解する](./billing-understand-your-bill.md)」をご覧ください。

## <a name="external"></a>外部サービスの料金について
外部サービス (Marketplace 注文とも呼ばれます) は、独立系サービス ベンダーによって提供され、個別に課金されます。 料金は、Azure の請求書には表示されません。 詳細については、「[Azure 外部サービスの課金について](billing-understand-your-azure-marketplace-charges.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
