
---
title: "Mobile Engagement Export API の概要"
description: "お使いのユーザー デバイスによって生成された生データをエクスポートして、自社のツールで活用する方法の基本について説明します。"
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: 
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
translationtype: Human Translation
ms.sourcegitcommit: b1e2ddb613e713972820621d0fa836c41eb61f91
ms.openlocfilehash: 346e0e480ff84ee849f135a7605d27df9e32f966


---
# <a name="mobile-engagement-export-api-overview"></a>Mobile Engagement Export API の概要
## <a name="introduction"></a>はじめに
このドキュメントでは、お使いのユーザー デバイスによって生成された生データをエクスポートして、自社のツールで活用する方法の基本について説明します。

## <a name="pre-requisites"></a>前提条件
Mobile Engagement から生データをエクスポートするには、次のことが必要です。

* API を使用するための API 認証の設定 ( [認証の手動設定に関する記事](mobile-engagement-api-authentication-manual.md)を参照)
* REST API または [.net SDK](mobile-engagement-dotnet-sdk-service-api.md)のいずれかの使用
* Azure Storage のアカウント

> [!NOTE]
> また、マイクロソフトでは開発期間中に [Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)を使用することをお勧めしています。Microsoft Azure ストレージ エクスプローラーには Azure Storage と対話するための簡易的な UI が用意されています。
> 
> 

## <a name="what-can-be-exported"></a>エクスポートできる項目
Mobile Engagement では数多くの型のデータを収集できるため、これらの異なるデータ型に適したさまざまな種類のエクスポートがあります。
エクスポートには、主に次の 2 つの種類があります。

* スナップショット: 主に状態を表し、履歴のない Mobile Engagement のデータのエクスポートに使用されます。 これには、タグ (アプリ情報)、トークン、プッシュ キャンペーンのフィードバックなどが含まれます。 結果として、これらの種類のエクスポートは日付に関連付けられていません。
* 履歴: この種類のエクスポートは、イベントやアクティビティなどの時間の経過と共に蓄積されるデータに使用されます。

考えられるすべてのエクスポートを以下の表に示します。

| エクスポートの種類 | データ型 | 説明 |
| --- | --- | --- |
| スナップショット |プッシュ |deviceid/userid 別のプッシュ キャンペーンのフィードバックのエクスポートを生成します。 |
| スナップショット |タグ |各デバイスに関連付けられているタグ (アプリ情報) のエクスポートを生成します。 |
| スナップショット |デバイス |テクニカル (モデル、ロケール、タイムゾーンなど)、タグ、最初の発生時間など、デバイスに関するほとんどのデータのエクスポートを生成します。 |
| スナップショット |トークン |すべての有効なトークンのエクスポートを生成します。 |
| 履歴 |アクティビティ |指定の期間における各デバイスのすべてのアクティビティのエクスポートを生成します。 |
| 履歴 |イベント |指定の期間における各デバイスのすべてのアクティビティのエクスポートを生成します。 |
| 履歴 |Job |指定の期間における各デバイスのすべてのジョブのエクスポートを生成します。 |
| 履歴 |エラー |指定の期間における各デバイスのすべてのエラーのエクスポートを生成します。 |

## <a name="how-does-it-work"></a>それはどのように機能しますか?
エクスポートは多くの時間がかかるタスクであり、大きなデータ ファイルが生成される可能性があります。 その理由から、呼び出してすぐにダウンロードするファイルが返されることはありません。
Mobile Engagement からデータをエクスポートするには、API を介して**エクスポート ジョブ**を生成する必要があります。一般的には、次を指定します。

* エクスポートの種類 (スナップショットまたは履歴)
* データ型
* (書き込みアクセス権を持つ有効な SAS を含む) **Azure Storage コンテナー**。ここにエクスポートの結果が書き込まれます。
* たとえば、コンテナーの URL パラメーターの例は次のようになります。https://[StorageAccountName].blob.core.windows.net/[ContainerName]?[SASWritePermissionsToken]  

実際の例を次に示します。 https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

ジョブが開始するまでにしばらく時間がかかる場合があるほか、実行時間も小さなアプリでは数秒、ユーザーやアクティビティの数が多いアプリでは数時間かかることがあるためご注意ください。

ジョブが作成されると、そのステータスを見てまだ実行中であるか完了しているかを確認できます。

ジョブが成功すると、結果のデータ ファイルが指定のストレージ コンテナーに格納されます。




<!--HONumber=Jan17_HO3-->


