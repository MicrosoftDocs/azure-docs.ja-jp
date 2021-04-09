---
title: Microsoft Azure Data Box Disk の自己管理型の発送 | Microsoft Docs in data
description: Azure Data Box Disk デバイスの自己管理型の発送ワークフローについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526332"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Azure portal で Azure Data Box Disk の自己管理型の発送を使用する

この記事では、Azure Data Box Disk の注文、受け取り、および返却を行う、自己管理型の発送タスクについて説明します。 Azure portal を使用して Data Box Disk を管理できます。

## <a name="prerequisites"></a>前提条件

自己管理型の発送は、[Azure Data Box Disk の注文](data-box-disk-deploy-ordered.md)時にオプションとして利用できます。 自己管理型の発送は、次のリージョンでのみご利用いただけます。

* 米国政府
* イギリス
* 西ヨーロッパ
* オーストラリア
* 日本
* シンガポール
* 韓国
* 南アフリカ
* インド (プレビュー)

## <a name="use-self-managed-shipping"></a>自己管理の出荷の使用

Data Box Disk を注文するときに、自己管理型の発送オプションを選択できます。

1. Azure Data Box Disk の注文の **[連絡先の詳細]** で、 **[+ 配送先住所の追加]** を選択します。

   ![[連絡先の詳細] 手順を示す [注文] ウィザードのスクリーンショット。[配送先住所の追加] オプションが強調表示されています。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 発送の種類を選択するときに、 **[Self-managed shipping]\(自己管理型の発送\)** オプションを選択します。 このオプションは、「前提条件」で説明されているように、サポートされているリージョンでのみ使用できます。

3. 発送先住所を入力したら、確認して注文を完了する必要があります。

   ![[配送先住所の追加] ダイアログ ボックスのスクリーンショット。[出荷方法] オプションと [配送先住所の追加] オプションが強調表示されています。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. デバイスの準備が完了し、電子メール通知を受け取ったら、受け取りをスケジュールします。 Azure Data Box Disk の注文の **[概要]** に移動し、 **[Schedule pickup]\(受け取りのスケジュールを設定\)** を選択します。

   ![Data Box デバイスの受け取りを注文する](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** の説明に従ってください。 認証コードを取得する前に、[adbops@microsoft.com](mailto:adbops@microsoft.com) にメールを送信して、リージョンのデータセンターからデバイスを受け取りのスケジュールを設定する必要があります。

   ![集荷のスケジュール](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. デバイスの受け取りをスケジュールすると、 **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** に認証コードが表示されます。

   ![[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\) ダイアログボックスのスクリーンショット。[引き取りの認証コード] テキスト ボックスが強調表示されています。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   この認証コードをメモしておきます。

   セキュリティ要件に従って、受け取りをスケジュールするときに、受け取りを行う人物の名前を指定する必要があります。

   また、データセンターに受け取りに行くユーザーの詳細を指定する必要もあります。 お客様または連絡担当者は政府発行の写真付きの ID を携帯する必要があり、これはデータセンターで検証されます。

   デバイスを受け取りに行くユーザーにも認証コードが必要です。 認証コードは、受け取りまたは返却に固有であり、データセンターで検証されます。

7. デバイスをデータセンターから受け取った後、注文は自動的に **[Picked up]\(受け取り済み\)** 状態になります。

   ![集荷されました](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. デバイスを受け取ったら、サイトのデータを Data Box Disk にコピーできます。 データのコピーが完了したら、Data Box Disk の発送の準備をします。

   データのコピーが終了したら、操作部門に連絡して返却の予定をスケジュールします。 データセンターにディスクを返却しに来る人物の詳細を共有する必要があります。 データセンターは、返却時に認証コードを確認する必要もあります。 返却の承認コードは、Azure portal の **[Schedule drop off]\(返却のスケジュールを設定\)** にあります。

   > [!NOTE]
   > この認証コードは、メールで共有しないでください。 これは、データセンターでの返却時にのみ検証されます。

9. 返却の予定を受け取ると、Azure portal に表示されるその注文の状態が **[Ready to receive at Azure datacenter]\(Azure データセンターで受け取る準備が完了しました\)** になります。

   ![[配送先住所の追加] ダイアログ ボックスのスクリーンショット。[出荷方法] オプションと [配送先住所の追加] オプションが強調表示されています。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. ID と認証コードが検証され、データセンターにデバイスを返却すると、注文ステータスが **[Received]\(返却済み\)** になります。

    ![返却完了](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. デバイスを返却すると、データのコピーが続行されます。 コピーが完了すると、注文が完了します。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure portal を使用して Azure Data Box Disk をデプロイする](data-box-disk-quickstart-portal.md)
