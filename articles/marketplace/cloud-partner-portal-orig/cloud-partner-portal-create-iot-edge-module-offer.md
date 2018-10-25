---
title: IoT Edge モジュール プランの作成 | Microsoft Docs
description: Marketplace で新規 IoT Edge モジュールを公開する方法。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: d3cc1a09963c5f7fee613af24c63fd15b1cfffee
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807226"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Cloud パートナー ポータルで新規 IoT Edge モジュールを公開する方法

この記事では、新規 IoT Edge モジュール プランを公開する手順について説明します。

## <a name="prerequisites"></a>前提条件

次の前提条件は、IoT Edge モジュールの Azure Marketplace への公開に適用されます。

-   [Cloud パートナー ポータル (CPP)](https://cloudpartner.azure.com/#alloffers) にアクセスします。 詳細については、[公開ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)を参照してください。

-   Azure Container Registry で IoT Edge モジュールをホストします。

-   IoT Edge モジュールのメタデータを用意します (完全に網羅されていないリストを含む)。

    -   タイトル

    -   説明 (基本的な HTML 形式)

    -   png イメージ形式のロゴ (40 ピクセル x 40 ピクセル、90 ピクセル x 90 ピクセル、115 ピクセル x 115 ピクセル、255 ピクセル x 115 ピクセルのサイズ)。

    -   使用条件とプライバシー ポリシー

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>CPP に IoT Edge モジュールをリストするための準備
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>IoT Edge モジュールの新規プランの作成 

次の手順に従って、IoT Edge モジュールをリストするための準備を行います。

-   [CPP アカウント](https://cloudpartner.azure.com/)にサインインします。

>[!Note]
>Cloud パートナー ポータルの一般情報については、[ドキュメントの学習](https://cloudpartner.azure.com/#learn)を参照してください

-   **[新しいプラン]** を選択し、**[IoT Edge モジュール]** を選択します。

>[!NOTE]
>IoT Edge モジュールは、IoT Edge で実行できるように特別に作られたコンテナーです。 IoT Edge モジュールで対応するシナリオは、IoT Edge のコンテキストで意味を成す必要があります。 IoT Edge デバイスへのデプロイを簡単にする既定の構成設定も含みます。 コンテナーには、edgeHub や IoT Hub と通信できるようにするため、IoT Edge モジュール SDK が含まれる場合があります。

### <a name="define-your-offer-settings"></a>プラン設定の定義

[プラン設定] タブで、プランに関する情報を入力します。

![プラン ID](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   **[プラン ID]** は、CPP でプランを一意に識別し、顧客向けの URL で使用される場合があります。

-   **[名前]** は、CPP でこのプランを参照するために、自分にのみ表示されます。

### <a name="create-one-or-more-skus"></a>1 つ以上の SKU の作成

各 SKU が 1 つのコンテナー イメージに対応します。 少なくとも 1 つの SKU を作成する必要があり、複数の SKU を追加できます。 SKU は 2 つの部分に分かれています。

-   SKU メタデータ

-   コンテナー メタデータ

**SKU を作成するには:**

**[SKU]** タブを選択し、**[新しい SKU]** を選択します。

![新しい SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

この SKU メタデータには、必須の以下のフィールドが含まれます。
- SKU ID - 一意の識別子。
- タイトル - SKU のタイトル (最大 50 文字)。
- 概要 - SKU の短い説明 (最大 100 文字)。
- 説明 - 長い説明。
- この SKU を非表示にする - 既定値は **[いいえ]** です。
   
![SKU の詳細](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>IoT Edge モジュール メタデータとコンテナー レジストリ

IoT Edge モジュールのメタデータには、Azure Container Registry (ACR) に格納されているイメージ参照情報が含まれています。 Azure Marketplace によってイメージがパブリック マーケットプレース レジストリにコピーされ、認定後に顧客向けに利用可能になります。 IoT Edge モジュール イメージを使用するためのすべてのユーザー要求は、Marketplace コンテナー レジストリで処理されます。

![コンテナー イメージ](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**コンテナー イメージ**

イメージ レポジトリの詳細には、次の必須フィールドがあります。

-   **[サブスクリプション ID]** – ACR レジストリが存在する Azure サブスクリプション ID です。

-   **[リソース グループ名]** – ACR レジストリのリソース グループ名です。

-   **[レジストリ名]** – ACR レジストリの名前です。

-   **[リポジトリ名]** – リポジトリ名です。 一度設定すると、この値を後から変更することはできません。 名前は一意にする必要があります。お使いのアカウントのその他のプランと同じ名前にならないようにするためです。

-   **[ユーザー名]** – ACR に関連付けられているユーザー名 (管理者ユーザー名) です。

-   **[パスワード]** – ACR に関連付けられているパスワードです。

    >[!Note]
    >公開プロセスで説明した ACR にパートナーがアクセスできるようにするため、ユーザー名とパスワードが必要です。

IoT Edge モジュール イメージを公開する際、1 つ以上のイメージ タグを指定することもできます。 テスト中にモジュールを簡単に識別できるようにするため、必ず 'latest' タグ (既定値) をモジュールに追加してください。

次の IoT Edge モジュール仕様を指定することもできます。

-   **createOptions** - この IoT Edge モジュールをすぐに開始できるように渡す既定値 createOptions。

-   **twin:** - IoT モジュール SDK を使用する場合に、この IoT Edge モジュールをすぐに開始できるように渡す既定値 twin。

-   **routes:** - IoT モジュール SDK を使用する場合に、この IoT Edge モジュールをすぐに開始できるように渡す既定値 routes。

### <a name="describe-your-iot-edge-module-for-your-customers"></a>顧客向けの IoT Edge モジュールの説明

[マーケットプレース] タブで、使用するマーケティング固有のコンテンツを追加します。 この情報は、Azure Marketplace でパブリックに表示され、リストされるものです。

![IoT Edge モジュールの概要](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **[タイトル]** - 公開されている IoT Edge モジュールのタイトル。

-   **[概要]** - 参照するページなど、ほとんどのページで一般向けに公開される IoT Edge モジュールの概要。

-   **[Long summary]\(概要 \(長文\)\)** - モジュールが注目されているときに一般向けに公開される IoT Edge モジュールの概要。 

-   **[説明]** - 製品の詳細ページで一般向けに公開される IoT Edge モジュールの説明  (基本的な HTML タグを使用して説明の書式を設定することができます)。

-   **[Marketing identifier]\(マーケティング識別子\)** - 製品の URL を作成するために使用する一意の識別子。 この URL は、*azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier* の形式になります。

-   **[Preview subscription Id]\(プレビュー サブスクリプション ID\)** - これらのサブスクリプションへのアクセスを持つユーザーは、認定手順の後に、公開されるまで IoT Edge モジュールを表示できるようになります。

-   **[役に立つリンク]** - 製品の詳細ページに表示されるリンクを最大 10 個まで追加することができます。

-   **[Suggested categories]\(推奨されるカテゴリ\)** - 最大 5 個までカテゴリを選択します。 これらは製品の詳細ページに表示されます。 現時点では、すべての IoT Edge モジュールは、参照ページの *[モノのインターネット] \> [IoT Edge モジュール]* カテゴリの下に表示されます。

-   **[ロゴ]** - IoT Edge モジュールのロゴ イメージを PNG 形式でアップロードします。 40 ピクセル x 40 ピクセル、90 ピクセル x 90 ピクセル、115 ピクセル x 115 ピクセル、255 ピクセル x 115 ピクセルのサイズを使用します。

-   **[スクリーンショット]** - スクリーンショットは製品の詳細ページに表示されます。 その IoT Edge モジュール自体について、またそのしくみについて視覚的に伝えるのに適した方法です。 たとえば、アーキテクチャ ダイアグラムやユース ケースの例を表示できます。

-   **[ビデオ]** - ビデオは製品の詳細ページに表示されます。 その IoT Edge モジュール自体について、またそのしくみについて視覚的に伝えるのに適した方法です。

-   **[リード管理]** - 製品に関心を示すすべての潜在顧客を収集するためのシステムを選択できます。

-   **[プライバシー]** - プライバシー ポリシーを指す URL を用意する必要があります。

-   **[使用条件]** - 使用条件を用意する必要があります。 HTML タグを使用して、このページの書式を設定したり、他のいずれかのページをポイントしたりすることができます。

### <a name="enter-your-support-contact-information"></a>サポートの連絡先情報の入力

[サポート] タブで、**[Engineering Contact]\(エンジニア リングの連絡先\)** と **[カスタマー サポート]** の情報を入力します。

![サポートの連絡先](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>IoT Edge モジュールの認定

必要なすべての情報を入力した後、**[公開]** を選択すると、認定のために IoT Edge モジュールが送信されます。 認定プロセスの手順を示すタイムラインが表示されます。

**モジュールの検証**

モジュールは、マイクロソフトの認定チームによって検証されます。 モジュールが認定されると、モジュールをテストするためのプライベート リンクが表示されます。 テスト後に変更する必要がある場合は、プランのメタデータを編集し、認定チームにモジュールを再送信します。 

## <a name="publish-your-iot-edge-module"></a>IoT Edge モジュールの公開

テストを完了し、公開する準備が整ったら、**[Go Live]\(公開\)** を選択し、IoT Edge モジュールを公開します。

>[!Important]
>IoT Edge モジュールを Ignite イベントで発表したい場合は、モジュールを 2018 年 9 月 23 日までに公開する必要があります。
