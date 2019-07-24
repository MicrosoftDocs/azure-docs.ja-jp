---
title: Azure で Google Cloud Platform アカウントを Cloudyn に接続する | Microsoft Docs
description: Google Cloud Platform アカウントを接続して、Cloudyn レポートにコストや使用状況のデータを表示します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 7f63293900e116fd3175b0ea6d704993a2dcf591
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051395"
---
# <a name="connect-a-google-cloud-platform-account"></a>Google Cloud Platform アカウントの接続

Cloudyn には既存の Google Cloud Platform アカウントを接続できます。 お客様のアカウントを Cloudyn に接続すると、Cloudyn レポートにコストや使用状況のデータを表示できるようになります。 この記事は、Google アカウントを構成して Cloudyn に接続する際に役立ちます。


## <a name="collect-project-information"></a>プロジェクト情報を収集する

最初に、プロジェクトに関する情報を収集します。

1. [https://console.cloud.google.com](https://console.cloud.google.com) で Google Cloud Platform コンソールにサインインします。
2. Cloudyn にオンボードするプロジェクト情報を確認し、**[プロジェクト名]** と **[プロジェクト ID]** をメモします。 この情報は、後続の手順のために手元に用意しておいてください。  
    ![Google Cloud Platform コンソールに示されたプロジェクト名とプロジェクト ID](./media/connect-google-account/gcp-console01.png)
3. 請求先が有効でなく、プロジェクトにリンクされていない場合は、請求先アカウントを作成します。 詳細については、「[新しい請求先アカウントの作成](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account)」を参照してください。

## <a name="enable-storage-bucket-billing-export"></a>ストレージ バケットの課金データのエクスポートを有効にする

Cloudyn は、ストレージ バケットから Google の課金データを取得します。 後で Cloudyn の登録中に使用できるように、**[バケット名]** と **[レポート接頭辞]** の情報を手元に用意しておきます。

Google Cloud Storage を使用して使用状況レポートを保存した場合は、料金が最小限に抑えられます。 詳細については、「[Google Cloud Storage の料金体系](https://cloud.google.com/storage/pricing)」を参照してください。

1. ファイルへの課金データのエクスポートを有効にしていない場合は、「[課金データのファイルへのエクスポートを有効にする方法](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file)」の手順に従ってください。 課金データのエクスポート形式には JSON または CSV を使用できます。
2. それ以外の場合、Google Cloud Platform コンソールで、**[お支払い]** > **[課金データのエクスポート]** の順に移動します。 課金データの **[バケット名]** と **[レポート接頭辞]** をメモしておきます。  
    ![[課金データのエクスポート] ページに示された課金データのエクスポート情報](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Google Cloud Platform API を有効にする

利用状況と資産の情報を収集するには、Cloudyn で次の Google Cloud Platform API が有効になっている必要があります。

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>API の有効化または確認

1. Google Cloud Platform コンソールで、Cloudyn に登録するプロジェクトを選択します。
2. **[API とサービス]** > **[ライブラリ]** の順に移動します。
3. 検索を使用して、上記の各 API を探します。
4. API ごとに、**[API enabled]\(API が有効\)** が表示されていることを確認します。 それ以外の場合は、**[有効にする]** をクリックします。

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Cloudyn に Google Cloud アカウントを追加する

1. Azure portal から Cloudyn ポータルを開くか、[https://azure.cloudyn.com](https://azure.cloudyn.com/) に移動してサインインします。
2. **[Settings]\(設定\)** (歯車記号) をクリックし、**[Cloud Accounts]\(クラウド アカウント\)** を選択します。
3. **[Accounts Management]\(アカウント管理\)** で **[Google Accounts]\(Google アカウント\)** タブを選択し、**[Add new +]\(新規追加 +\)** をクリックします。
4. **[Google Account Name]\(Google アカウント名\)** に請求先アカウントのメール アドレスを入力し、**[Next]\(次へ\)** をクリックします。
5. Google の認証ダイアログで、Google アカウントを選択または入力し、お使いのアカウントに cloudyn.com へのアクセスを**許可**します。
6. 前にメモした要求プロジェクト情報を追加します。 これには、**プロジェクト ID**、**プロジェクト**名、**課金データ**のバケット名、**課金データ ファイル**のレポート接頭辞が含まれます。その後、**[Save]\(保存\)** をクリックします。  
    ![Google プロジェクトを Cloudyn アカウントに追加する](./media/connect-google-account/add-project.png)

Google アカウントはアカウントの一覧に表示され、**[Authenticated]\(認証済み\)** と示されます。 その下に Google プロジェクトの名前と ID が表示され、緑のチェック マーク記号が付いています。 アカウントの状態は **[Completed]\(完了\)** と示されます。

数時間以内に、Cloudyn のレポートには Google のコストと使用状況に関する情報が表示されます。

## <a name="next-steps"></a>次の手順

- Cloudyn の詳細については、Cloudyn のチュートリアル「[使用状況とコストを確認する](./tutorial-review-usage.md)」に進んでください。
