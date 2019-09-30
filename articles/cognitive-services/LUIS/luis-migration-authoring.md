---
title: オーサリングのために Azure リソースに移行する
titleSuffix: Azure Cognitive Services
description: Azure オーサリング リソース キーに移行します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 8e732496c4523646f02ef461b7d3c52314c28dae
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932826"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure リソース オーサリング キーに移行する

Language Understanding (LUIS) のオーサリング認証が、メール アカウントから Azure リソースに変更されました。 

## <a name="why-migrate"></a>移行する理由

オーサリングに Azure リソースを使用すると、そのリソースの所有者として、オーサリングするためのアクセスを制御できます。 オーサリング リソースを作成して名前を付けることで、さまざまな作成者のグループを管理できます。 

たとえば、オーサリングしている LUIS アプリが 2 種類あり、それぞれメンバーが異なる場合、異なるオーサリング リソースを 2 つ作成して、共同作成者を割り当てることができます。 Azure のオーサリング リソースは、認証を制御します。 

> [!Note]
> 移行前は、共同作成者は "_コラボレーター_" と呼ばれます。 移行後は、Azure のロール "_共同作成者_" が同じ機能として使用されます。

## <a name="what-is-migrating"></a>移行とは

次のものが移行に当たります。

* LUIS のすべてのユーザー、所有者、共同作成者。
* **すべて**のアプリ。
* **一方向の**移行である。

所有者は移行するアプリのサブセットを選択できません。また、プロセスを元に戻すことはできません。 

次のものは移行に当たりません。 

* コラボレーターを収集し、Azure のオーサリング リソースに自動的に移動または追加するプロセス。 この手順はアプリの所有者が実行する必要があります。 この手順には、適切なリソースへのアクセス許可が必要です。
* 予測ランタイム リソースを作成して割り当てるプロセス。 予測ランタイム リソースが必要な場合、これは[別のプロセス](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)であり、かつ不変です。 

## <a name="how-are-the-apps-migrating"></a>アプリはどのように移行されるか

移行プロセスは [LUIS ポータル](https://www.luis.ai)に用意されています。 

次の場合に移行するように求められます。

* オーサリングするアプリがメール認証システム上にある。
* かつアプリの所有者である。 

ウィンドウをキャンセルすることで、移行プロセスを遅らせることができます。 移行が完了するまで、または移行の期限が過ぎるまで、定期的に移行するように求められます。 移行プロセスは上部のナビゲーション バーのロック アイコンから始めることができます。

## <a name="migration-for-the-app-owner"></a>アプリ所有者による移行

### <a name="before-you-migrate"></a>移行の前に

* **必要に応じて**、各アプリをエクスポートするかエクスポート [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) を使用して、LUIS ポータルのアプリの一覧からアプリをバックアップします。
* **必要に応じて**、各アプリのコラボレーターの一覧を保存します。 このメールの一覧は、移行プロセスの一部として提供されています。
* **必須**。[Azure サブスクリプション](https://azure.microsoft.com/free/)が必要です。 サブスクリプション プロセスの一部では、課金情報が必要です。 ただし、LUIS を使用する際には、無料 (`F0`) 価格レベルを使用できます。 

**LUIS アプリの作成は無料です**。これは `F0` レベルの印で確認できます。 価格レベルの詳細については[こちら](luis-boundaries.md#key-limits)をご覧ください。

Azure サブスクリプションをお持ちでない場合は、[サインアップ](https://azure.microsoft.com/free/)してください。 

### <a name="migration-steps"></a>移行の手順

[これらの移行手順](luis-migration-authoring-steps.md)に従います。

### <a name="after-you-migrate"></a>移行を完了した後 

移行プロセスが完了すると、すべての LUIS アプリが 1 つの LUIS オーサリング リソースに割り当てられます。

_LUIS ポータル_の **[Manage]\(管理\) -> [Azure resources]\(Azure リソース\)** ページで、さらに多くのオーサリング リソースを作成して割り当てることができます。 

_Azure portal_ のオーサリング リソースの **[Access Control (IAM)]** ページから、そのリソースの共同作成者を追加できます。 詳細については、[共同作成者アクセスの追加](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)に関するページを参照してください。

|ポータル|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* 予測リソースとオーサリング リソースを作成します。<br>* 共同作成者を割り当てます。|
|[LUIS](https://www.luis.ai)|* 新しいオーサリング リソースに移行します。<br>* **[Manage]\(管理\) -> [Azure resources]\(Azure リソース\)** ページから、アプリに予測リソースとオーサリング リソースを割り当てる、またはアプリからそれらの割り当てを解除します。| 

## <a name="migration-for-the-app-contributor"></a>アプリ共同作成者による移行

コラボレーター/共同作成者を含め、LUIS の全ユーザーを移行する必要があります。 

### <a name="before-the-app-is-migrated"></a>アプリを移行する前

自身がコラボレーターであるアプリをエクスポートしてから、アプリを LUIS にインポートすることもできます。 インポート プロセスにより新しいアプリが新しいアプリ ID で作成され、自身がその所有者になります。

### <a name="after-the-app-is-migrated"></a>アプリを移行した後

アプリの所有者に依頼し、[Azure オーサリング リソースに自分の電子メールをコラボレーターとして追加](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)してもらう必要があります。 

移行プロセスの後、自分が所有するアプリはすべて、LUIS ポータルの **[マイ アプリ]** ページで利用できます。  

## <a name="next-steps"></a>次の手順

* [アプリをオーサリング リソースに移行する方法](luis-migration-authoring-steps.md)