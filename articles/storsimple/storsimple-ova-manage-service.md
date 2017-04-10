---
title: "StorSimple Virtual Array の StorSimple Manager サービスのデプロイ | Microsoft Docs"
description: "Azure クラシック ポータルで StorSimple Manager サービスを作成および削除する方法、さらにサービス登録キーを管理する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 40084881-ad8d-440f-a365-68259beee616
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/19/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5c2a46dcf43287db2a2960af7993ae83558faadf


---
# <a name="deploy-the-storsimple-manager-service-for-storsimple-virtual-array"></a>StorSimple Virtual Array の StorSimple Manager サービスのデプロイ
## <a name="overview"></a>概要
StorSimple Manager サービスは Microsoft Azure で実行され、複数の StorSimple デバイスに接続します。 サービスを作成したら、それを使用して、ブラウザーで実行される Microsoft Azure クラシック ポータルでデバイスを管理できます。 このポータルを使用すると、StorSimple Manager サービスに接続されているすべてのデバイスを 1 か所から集中的に監視できるので、管理の負荷を最小限に抑えることができます。

StorSimple Manager のランディング ページには、StorSimple ストレージ デバイスの管理に使用できるすべての StorSimple Manager サービスの一覧が示されています。 各 StorSimple Manager について、以下の情報が [StorSimple Manager] ページに表示されます。

* **[名前]** – StorSimple Manager サービスの作成時に付けられた名前。 サービス名は、サービスの作成後に変更することはできません。
* **[状態]** – サービスの状態。**アクティブ**、**作成中**、または**オンライン**のいずれか。
* **[場所]** – StorSimple デバイスがデプロイされる地理的な場所。
* **[サブスクリプション]** – サービスに関連付けられている課金サブスクリプション。

[StorSimple Manager] ページで実行できる一般的なタスクは以下のとおりです。

* サービスの作成
* サービスの削除
* サービス登録キーを取得する
* サービス登録キーを再生成する

このチュートリアルでは、これらの各タスクの実行方法を説明します。 この記事に記載されている内容は、StorSimple Virtual Array にのみ適用されます。 StorSimple 8000 シリーズの詳細については、「 [StorSimple Manager サービスをデプロイする](storsimple-manage-service.md)」を参照してください。

## <a name="create-a-service"></a>サービスの作成
StorSimple デバイスをデプロイする場合は、 **[簡易作成]** オプションを使用して、StorSimple Manager サービスを作成します。 サービスを作成する上で必要なものは次のとおりです。

* エンタープライズ契約によるサブスクリプション
* アクティブな Microsoft Azure ストレージ アカウント
* アクセス管理で使用する課金情報

サービスの作成時に既定のストレージ アカウントを生成することもできます。

単一のサービスで複数のデバイスを管理できます。 ただし、1 つのデバイスを複数のサービスに割り当てることはできません。 大企業は、そのようなサービス インスタンスを複数持つことで、さまざまなサブスクリプション、組織、デプロイの場所に対応することができます。  

> [!NOTE]
> StorSimple 8000 シリーズ デバイスと StorSimple Virtual Array の管理には、StorSimple Manager サービスの個別のインスタンスが必要です。
> 
> 

サービスを作成するには、次の手順を実行します。

[!INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## <a name="delete-a-service"></a>サービスの削除
サービスを削除する際は、接続されているデバイスでそのサービスが使用されていないことを先に確認します。 サービスが使用されている場合は、該当するデバイスを非アクティブにします。 非アクティブ化操作によってデバイスとサービス間の接続は切断されますが、クラウド内のデバイスのデータは保持されます。 

> [!IMPORTANT]
> いったんサービスを削除すると、その操作を元に戻すことはできません。 
> 
> 

サービスを削除するには次の手順を実行します。

### <a name="to-delete-a-service"></a>サービスを削除するには
1. **[StorSimple Manager サービス]** ページで、削除するサービスをクリックします。
2. ページの下部にある **[削除]** をクリックします。
3. 確認通知が表示されたら、 **[はい]** をクリックします。 サービスが削除されるまで数分かかる場合があります。

## <a name="get-the-service-registration-key"></a>サービス登録キーを取得する
正常にサービスを作成した後、そのサービスに StorSimple デバイスを登録します。 初めて StorSimple デバイスを登録する場合は、サービス登録キーが必要です。 既存の StorSimple サービスに追加のデバイスを登録するには、登録キーとサービス データ暗号化キー (最初のデバイスの登録中に生成される) の両方が必要です。 サービス データ暗号化キーの詳細については、 [ローカル Web UI からのサービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)に関するトピックを参照してください。 

次の手順を実行して、サービス登録キーを取得します。

[!INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

サービス登録キーは、安全な場所に保管してください。 このキーは、サービスに追加のデバイスを登録するときにサービス データ暗号化キーと共に必要です。 サービス登録キーを取得したら、StorSimple 用 Windows PowerShell インターフェイスを介してデバイスを構成する必要があります。

## <a name="regenerate-the-service-registration-key"></a>サービス登録キーを再生成する
キー ローテーションを実行する必要があるとき、またはサービス管理者の一覧が変更されたときは、サービス登録キーを再生成する必要があります。 キーを再生成した場合、新しいキーはその後のデバイスの登録に対してのみ使用されます。 既に登録されているデバイスは、再生成の影響を受けません

次の手順を実行して、サービス登録キーを再生成します。

### <a name="to-regenerate-the-service-registration-key"></a>サービス登録キーを再生成するには
1. **[StorSimple Manager サービス]** ページで、**[登録キー]** をクリックします。
2. **[サービス登録キー]** ダイアログ ボックスで、**[再生成]** をクリックします。
3. 確認メッセージが表示されます。 **[OK]** をクリックして、再生成を続行します。
4. 新しいサービス登録キーが表示されます。
5. このキーをコピーし、このサービスに新しいデバイスを登録するときのために保存します。
6. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-manage-service/image7.png)  をクリックして、このダイアログ ボックスを閉じます。

## <a name="next-steps"></a>次のステップ
* [StorSimple Virtual Array の使用を開始する](storsimple-ova-deploy1-portal-prep.md) 方法を確認します。
* [StorSimple デバイスを管理する](storsimple-ova-web-ui-admin.md)方法を確認します。




<!--HONumber=Nov16_HO3-->


