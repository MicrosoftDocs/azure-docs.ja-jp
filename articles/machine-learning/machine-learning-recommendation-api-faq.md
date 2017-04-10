---
title: "Machine Learning Recommendations API の設定と使用 | Microsoft Docs"
description: "Azure Machine Learning で作成された Microsoft Recommendations API の FAQ"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 1ffc3c16-e040-4225-9d72-105129938dfa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: luisca
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-datamarket-deprecation
translationtype: Human Translation
ms.sourcegitcommit: 9ac2a1bf5987fc6fc30e20a1b4a10340eeed3825
ms.openlocfilehash: 15cf891b9c31e1f2e2b108e631f32883ce2f36a7
ms.lasthandoff: 01/05/2017


---
# <a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>Machine Learning Recommendations API の設定と使用に関する FAQ
**Recommendations とは何ですか。**

> [!NOTE]
> このバージョンの代わりに Recommendations API Cognitive Service の使用を開始することをお勧めします。 このサービスは Recommendations Cognitive Service に置き換えられ、新機能はすべて Cognitive Service で開発されるようになります。 Cognitive Service には、バッチ処理のサポート、API エクスプローラーの改善、API サーフェスの簡素化、より一貫性のあるサインアップおよび課金方法などの新機能が含まれています。
> 詳細については、「 [Migrating to the new Cognitive Service](http://aka.ms/recomigrate)
> 
> 

顧客への製品やサービスのクロスセルやアップセルのために推奨機能を利用している組織や事業に対して、Azure Machine Learning の Recommendations サービスは、セルフサービスの推奨エンジンを提供します。 これは、コア アルゴリズムとして行列因子分解を使用した協調フィルタリングの実装です。 アプリケーション開発者は REST Api を使用してRecommendations にアクセスできます。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Recommendations では何ができますか。**

Recommendations は、1 つの項目または一連の項目を入力として受け取り、関連する推奨の一覧を返します。 たとえば、オンライン小売業者の顧客が製品をクリックします。 オンライン小売業者が Recommendations への入力としてその製品を送信すると、製品一覧が返されます。業者は、一覧のどの製品を顧客に表示するかを決定します。 Recommendations を使用してオンライン ストアを最適化したり、社内の販売部門やコール センターに知らせたりすることもできます。

**使用制限はありますか。**

Recommendations には次の使用制限があります。

* サブスクリプションごとのモデルの最大数: 10
* カタログが保持できる項目の最大数: 100,000
* 保持される使用状況ポイントの最大数は ~5,000,000 です。 新しいデータがアップロードまたは報告されると、最も古いデータが削除されます。
* 電子メールで送信できるデータ (例: カタログ データのインポート、使用データのインポート) の最大サイズは 200 MB です。
* アクティブではない Recommendations モデルのビルドの&1; 秒あたりのトランザクション数 (TPS) は ~2 TPS です。 アクティブな推奨モデルのビルドは、最大 20 TPS を保持できます。

## <a name="purchase-and-billing"></a>購入と請求
**開始期間中の Recommendations の料金はいくらですか。**

Recommendations は、サブスクリプション ベースのサービスです。 課金は、1 か月あたりのトランザクションのボリュームに基づいて行われます。 料金情報については、Microsoft Azure Marketplace の [プランに関するページ](https://datamarket.azure.com/dataset/amla/recommendations) をご確認ください。

**Recommendations の追跡やユーザー アクティビティの格納に関連する料金はありますか。**

現時点ではありません。

**Recommendations には、無料試用版がありますか。**

1 か月あたり 10,000 トランザクションに制限された無料試用版があります。

**Recommendations への請求はいつ行われますか。**

有料サブスクリプションは、月額料金のサブスクリプションです。 有料サブスクリプションを購入すると、最初の使用月からすぐに課金されます。 サブスクリプション ページのプランに表示される金額 (さらに適用される税金) が課金されます。 この月額料金は、サブスクリプションを取り消すまで、最初の購入時と同じ日付で毎月発生します。 

**上位のサービスへのアップグレード方法を教えてください。**

Microsoft Azure Marketplace の [プランに関するページ](https://datamarket.azure.com/dataset/amla/recommendations) からサブスクリプションを購入または更新できます。

サブスクリプションをアップグレードする場合:

* 古いサブスクリプションに残っているトランザクションは、新しいサブスクリプションには追加されません。 
* 古いサブスクリプションで未使用のトランザクションがある場合でも、新しいサブスクリプションの全額をお支払いください。

サブスクリプションのアップグレード プロセス:

* [プランに関するページ](https://datamarket.azure.com/dataset/amla/recommendations)に移動します。
* まだサインインしていない場合は、Marketplace にサインインします。
* 右側のペインに、使用可能なすべてのプランが一覧表示されます。 アップグレードするプランのラジオ ボタンをクリックします。
* アップグレードする場合は、 **[OK]**をクリックします。 アップグレードしない場合は、 **[取り消し]**をクリックします。

**重要** 請求と使用に関係しますので、アップグレードする前に、ダイアログ ボックスに表示された内容をよくご確認ください。

**Recommendations のサブスクリプションはいつ終了しますか。**

取り消した時点で、お客様のサブスクリプションが終了します。 サブスクリプションを取り消す場合は、以下の手順をご覧ください。

**Recommendations のサブスクリプションを取り消す方法を教えてください。**

サブスクリプションを取り消すには、次の手順を使用します。 現在のサブスクリプションが有料サブスクリプションの場合、お客様のサブスクリプションは現在の請求期間が終了するまで有効です。 すぐに取り消しを有効にする必要がある場合は、 [Microsoft サポート](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)にお問い合わせください。

**注** 請求期間前の取り消し、または請求期間内の未使用トランザクションに対する払い戻しはありません。

* [プランに関するページ](https://datamarket.azure.com/dataset/amla/recommendations)に移動します。
* まだサインインしていない場合は、Marketplace にサインインします。
* データ セットの名前と状態の右側にある **[取り消し]** をクリックします。 現在の請求期間が終了するまで、またはトランザクションの上限に達するまで、このサブスクリプションを使用することができます。

新しいサブスクリプションを購入するためにサブスクリプションを今すぐ取り消す場合は、 [Microsoft サポート](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)でチケットを申請してください。

## <a name="getting-started-with-recommendations"></a>Recommendations の概要
**Recommendations は私に適していますか。** 

Machine Learning の Recommendations サービスは、顧客への製品やサービスのクロスセルやアップセルのために推奨機能を利用している組織や事業を対象にしています。 顧客対応のための Web サイト、営業スタッフ、社内営業スタッフ、またはコール センターがあり、数十以上の製品やサービスのカタログを提供している場合、Recommendations を使用することによって収益が向上する可能性があります。 

Recommendations は簡単に利用できるように設計されています。 現在の API ベースのバージョンには、基本的なプログラミング スキルが必要です。 サポートが必要な場合は、お客様の Web サイトを作成したベンダーにお問い合わせください。 社内に IT 部門がある、または社内開発者がいらっしゃる場合は、それらの方に支援を依頼することをお勧めします。 

**Recommendations を設定するための前提条件とは何ですか。**

Recommendations には、お客様のカタログに関するユーザーの選択内容のログが必要です。 このようなログがなくても、顧客対応の Web サイトを運営している場合は、Recommendations がユーザー アクティビティを収集できます。 

Recommendations には、お客様の製品やサービスのカタログも必要です。 カタログがない場合は、Recommendations が実際の顧客の使用状況データを使用して、カタログを抽出することができます。 暗黙のカタログには、ユーザー トランザクションの一部として報告されていない項目は含まれません。

**Recommendations の最初の設定方法を教えてください。**

Recommendations を[サブスクライブ](https://datamarket.azure.com/dataset/amla/recommendations)したら、「[Azure Machine Learning Recommendations – クイック スタート ガイド](machine-learning-recommendation-api-quick-start-guide.md)」にある API ドキュメントを使用してサービスを設定する必要があります。

**API ドキュメントはどこで入手できますか。** 

API ドキュメントは、「[Azure Machine Learning Recommendations - クイック スタート ガイド](machine-learning-recommendation-api-quick-start-guide.md)」から入手できます。。

**Recommendations にカタログや使用状況データをアップロードする場合にはどのようなオプションがありますか。**

カタログや使用状況データのアップロードには&2; つのオプションがあります。CRM システムまたはその他のログからこれらのデータをエクスポートして Recommendations にアップロードするか、ユーザー アクティビティを追跡するタグを Web サイトに追加することができます。 後者を使用する場合、データは Azure に格納されます。

## <a name="maintenance-and-support"></a>メンテナンスとサポート
**データ セットの大きさはどの程度まで許容されますか。**

各データ セットには、最大 100,000 のカタログ項目、および最大 2,048 MB の使用状況データを保持できます。
さらに、サブスクリプションには最大 10 個のデータ セット (モデル) を含めることができます。

**どこで Recommendations についてのテクニカル サポートを受けられますか。**

テクニカル サポートは、「 [Microsoft Azure サポート](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) 」サイトで利用できます。

**使用条件はどこで入手できますか。**

「[Microsoft Azure Machine Learning Recommendations API Terms of Service](https://datamarket.azure.com/dataset/amla/recommendations#terms)」をご覧ください。


