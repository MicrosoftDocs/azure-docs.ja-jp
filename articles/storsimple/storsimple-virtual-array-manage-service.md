---
title: StorSimple デバイス マネージャー サービスをデプロイする | Microsoft Docs
description: Azure Portal で StorSimple デバイス マネージャー サービスを作成および削除する方法と、サービス登録キーを管理する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 92041be4d3d5ef4d980c17fa0a5870b740c1784a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995015"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>StorSimple Virtual Array の StorSimple デバイス マネージャー サービスをデプロイする

## <a name="overview"></a>概要

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

StorSimple デバイス マネージャー サービスは Microsoft Azure で実行され、複数の StorSimple デバイスに接続します。 サービスを作成したら、それを使用して、ブラウザーで実行される Microsoft Azure ポータルでデバイスを管理できます。 このポータルを使用すると、StorSimple デバイス マネージャー サービスに接続されているすべてのデバイスを 1 か所から集中的に監視でき、管理の負荷を最小限に抑えることができます。

StorSimple デバイス マネージャー サービスに関連する一般的なタスクは次のとおりです。

* サービスの作成
* サービスの削除
* サービス登録キーを取得する
* サービス登録キーを再生成する

このチュートリアルでは、それぞれのタスクの実行方法について説明します。 この記事に記載されている内容は、StorSimple Virtual Array にのみ適用されます。 StorSimple 8000 シリーズの詳細については、「 [StorSimple Manager サービスをデプロイする](./storsimple-8000-manage-service.md)」を参照してください。

## <a name="create-a-service"></a>サービスの作成

サービスを作成する上で必要なものは次のとおりです。

* エンタープライズ契約によるサブスクリプション
* アクティブな Microsoft Azure ストレージ アカウント
* アクセス管理で使用する課金情報

サービスの作成時にストレージ アカウントを生成するように選択することもできます。

単一のサービスで複数のデバイスを管理できます。 ただし、1 つのデバイスを複数のサービスに割り当てることはできません。 大企業は、そのようなサービス インスタンスを複数持つことで、さまざまなサブスクリプション、組織、デプロイの場所に対応することができます。

> [!NOTE]
> StorSimple 8000 シリーズ デバイスと StorSimple Virtual Array の管理には、StorSimple デバイス マネージャー サービスの個別のインスタンスが必要です。


サービスを作成するには、次の手順を実行します。

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>サービスの削除

サービスを削除する際は、接続されているデバイスでそのサービスが使用されていないことを先に確認します。 サービスが使用されている場合は、該当するデバイスを非アクティブにします。 非アクティブ化操作によってデバイスとサービス間の接続は切断されますが、クラウド内のデバイスのデータは保持されます。

> [!IMPORTANT]
> いったんサービスを削除すると、その操作を元に戻すことはできません。 このサービスを利用していたデバイスを別のサービスで使用するには、デバイスを出荷時の状態にリセットする必要があります。 このとき、デバイス上のローカル データや構成は失われます。
 

サービスを削除するには次の手順を実行します。

#### <a name="to-delete-a-service"></a>サービスを削除するには

1. **[すべてのリソース]** に移動します。 StorSimple デバイス マネージャー サービスを検索します。 削除するサービスを選択します。
   
    ![削除するサービスの選択](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. サービス ダッシュボードに移動して、サービスに接続されているデバイスがないことを確認します。 このサービスに登録されているデバイスがない場合は、バナー メッセージにもその旨が示されます。 **[削除]** をクリックします。
   
    ![サービスの削除](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. 確認を求めるメッセージが表示されたら、その確認通知で **[はい]** をクリックします。 
   
    ![サービス削除の確定](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. サービスが削除されるまで数分かかる場合があります。 サービスが正常に削除されると、その旨が通知されます。
   
    ![サービス削除に成功](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

サービスの一覧が更新されます。

 ![更新されたサービスの一覧](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>サービス登録キーを取得する
正常にサービスを作成した後、そのサービスに StorSimple デバイスを登録します。 初めて StorSimple デバイスを登録する場合は、サービス登録キーが必要です。 既存の StorSimple サービスに追加のデバイスを登録するには、登録キーとサービス データ暗号化キー (最初のデバイスの登録中に生成される) の両方が必要です。 サービス データ暗号化キーの詳細については、「 [StorSimple セキュリティ](./storsimple-8000-security.md)」をご覧ください。 登録キーを取得するには、サービスの **[キー]** ブレードにアクセスします。

次の手順を実行して、サービス登録キーを取得します。

#### <a name="to-get-the-service-registration-key"></a>サービス登録キーを取得するには
1. **[StorSimple デバイス マネージャー]** ブレードで、 **[管理]&gt;** **[キー]** の順に移動します。
   
   ![キー ブレード](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **[キー]** ブレードに、サービス登録キーが表示されます。 コピー アイコンを使用して、登録キーをコピーします。 

サービス登録キーは、安全な場所に保管してください。 このキーは、サービスに追加のデバイスを登録するときにサービス データ暗号化キーと共に必要です。 サービス登録キーを取得したら、StorSimple 用 Windows PowerShell インターフェイスを介してデバイスを構成する必要があります。

## <a name="regenerate-the-service-registration-key"></a>サービス登録キーを再生成する
キー ローテーションを実行する必要があるとき、またはサービス管理者の一覧が変更されたときは、サービス登録キーを再生成する必要があります。 キーを再生成した場合、新しいキーはその後のデバイスの登録に対してのみ使用されます。 既に登録されているデバイスは、再生成の影響を受けません

次の手順を実行して、サービス登録キーを再生成します。

#### <a name="to-regenerate-the-service-registration-key"></a>サービス登録キーを再生成するには
1. **[StorSimple デバイス マネージャー]** ブレードで、 **[管理]&gt;** **[キー]** の順に移動します。
   
   ![再度 [キー] ブレード](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **[キー]** ブレードで、 **[再生成]** をクリックします。
   
   ![[再生成] をクリック](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. **[サービス登録キーの再生成]** ブレードで、キーを再生成するときに必要な操作を確認します。 このサービスに登録されている以降のデバイスはすべて、新しい登録キーを使用します。 **[再生成]** をクリックして確定します。 登録が完了すると、その旨が通知されます。
   
   ![キーの再生成の確定](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. 新しいサービス登録キーが表示されます。
   
    ![追加のキーの再生成の確定](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   このキーをコピーし、このサービスに新しいデバイスを登録するときのために保存します。

## <a name="next-steps"></a>次のステップ
* StorSimple Virtual Array の[使用を開始](storsimple-virtual-array-deploy1-portal-prep.md)する方法を確認します。
* [StorSimple デバイスを管理する](storsimple-ova-web-ui-admin.md)方法を確認します。