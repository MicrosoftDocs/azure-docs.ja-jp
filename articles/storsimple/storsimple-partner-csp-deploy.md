---
title: クラウド ソリューション プロバイダー プログラム用の StorSimple Virtual Array をデプロイする
description: CSP パートナーが顧客を追加または新しいサブスクリプションを既存の顧客に追加し、CSP に StorSimple Virtual Array をデプロイするサービスを作成する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021862"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>クラウド ソリューション プロバイダー プログラム用の StorSimple Virtual Array をデプロイする

## <a name="overview"></a>概要

StorSimple Virtual Array は、お客様のクラウド ソリューション プロバイダー (CSP) パートナーがデプロイできます。 CSP パートナーは、StorSimple デバイス マネージャー サービスを作成できます。 このサービスは、StorSimple Virtual Array および関連付けられている共有、ボリューム、バックアップをデプロイおよび管理するために使用できます。

この記事では、CSP パートナーが顧客を追加または新しいサブスクリプションを既存の顧客に追加し、CSP に StorSimple Virtual Array をデプロイするサービスを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。

- CSP プログラムに登録していること。
- 有効な[パートナー センター](https://partnercenter.microsoft.com/) ログイン資格情報があること。 資格情報を使用すると、Microsoft Partners Web にサインインして、新規顧客の追加、顧客の検索、顧客アカウントへの移動をパートナー ダッシュボードから行うことができます。 CSP は、Azure Portal で顧客に代わって StorSimple 管理者の役目を果たすことができます。
                             
## <a name="add-a-customer"></a>顧客の追加

顧客を追加すると、サブスクリプションが自動的に作成されます。 顧客を追加 (およびサブスクリプションを自動的に作成) するには、Microsoft Partners Web で次の手順を実行します。

1. [パートナー センター](https://partnercenter.microsoft.com/)に移動し、CSP の資格情報を使用してサインインします。 **[ダッシュボード]** をクリックします。

     ![パートナー センターのダッシュボード](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. 左側のウィンドウで、 **[顧客]** をクリックします。 右側のウィンドウで、 **[顧客の追加]** をクリックします。 顧客の詳細を入力します。 **[次へ: サブスクリプション]** をクリックして、顧客のサブスクリプションを作成します。

    ![顧客の追加](./media/storsimple-partner-csp-deploy/image2.png)

3.  **[Microsoft Azure]** プランを選択します。 ページの下部までスクロールし、 **[Review (レビュー)]** をクリックします。

    ![サブスクリプション情報のレビュー](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. 情報をレビューし、 **[送信]** をクリックします。

    ![サブスクリプションを送信する](./media/storsimple-partner-csp-deploy/image4.png)

5. 後で参照できるように確認情報を保存します。

    ![確認の保存](./media/storsimple-partner-csp-deploy/image5.png)

6. 追加した顧客を検索するか、その顧客に移動します。 **[会社名]** をクリックして、詳細にドリル ダウンします。

    ![顧客の検索](./media/storsimple-partner-csp-deploy/image6.png)  

7. 左側のウィンドウで、 **[サービス管理]** を選択します。 右側のウィンドウで、 **[サービスの管理]** の下にある **[Microsoft Azure の管理ポータル]** をクリックして、顧客の Azure 管理者としてサインインします。

    ![Azure Portal へのログイン](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple デバイス マネージャーを作成するには、 **[+ 新規]** をクリックし、 **[StorSimple 仮想デバイス シリーズ]** を検索するか、ここに移動します。 詳しくは、[StorSimple デバイス マネージャー サービスのデプロイ](storsimple-virtual-array-manage-service.md)に関するページをご覧ください。

    ![StorSimple デバイス マネージャー サービスの作成](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>サブスクリプションの追加

場合によっては、既存の顧客にサブスクリプションを追加することが必要になります。 既存の顧客にサブスクリプションを追加するには、Microsoft Partners Web で次の手順を実行します。

1. [パートナー センター](https://partnercenter.microsoft.com/)に移動し、CSP の資格情報を使用してサインインします。 **[ダッシュボード]** をクリックします。

     ![パートナー センターのダッシュボード](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. 左側のウィンドウで、 **[顧客]** をクリックします。 サブスクリプションに追加する顧客を検索するか、その顧客に移動します。 ![展開チェックマーク アイコン](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) アイコンをクリックして、顧客の会社名の行を展開します。 詳細で、 **[サブスクリプションの追加]** をクリックします。

    ![顧客](./media/storsimple-partner-csp-deploy/image10.png)

3. サブスクリプションの **[優先オファー]** で **[Microsoft Azure]** をオンにし、 **[送信]** をクリックします。 これにより、新しいサブスクリプションが作成されます。

    ![新しいサブスクリプションの追加](./media/storsimple-partner-csp-deploy/image11.png)

6. 新しいサブスクリプションが作成されたら、左側のウィンドウで **[<-- 顧客]** をクリックして、 **[顧客]** ページに戻ります。 サブスクリプションを作成した顧客を検索します。 **[会社名]** をクリックして、詳細にドリル ダウンします。

    ![顧客の検索](./media/storsimple-partner-csp-deploy/image6.png)  

7. 左側のウィンドウで、 **[サービス管理]** を選択します。 右側のウィンドウで、 **[サービスの管理]** の下にある **[Microsoft Azure の管理ポータル]** をクリックして、顧客の Azure 管理者としてサインインします。

    ![Azure Portal へのログイン](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple デバイス マネージャーを作成するには、 **[+ 新規]** をクリックし、 **[StorSimple 仮想デバイス シリーズ]** を検索するか、ここに移動します。 詳しくは、[StorSimple デバイス マネージャー サービスのデプロイ](storsimple-virtual-array-manage-service.md)に関するページをご覧ください。

    ![StorSimple デバイス マネージャー サービスの作成](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>次のステップ

- CSP の StorSimple について他に疑問がある場合は、[CSP の StorSimple: よく寄せられる質問](storsimple-partner-csp-faq.md)に関するページに進みます。
- StorSimple をデプロイする準備ができている場合は、[CSP での StorSimple のデプロイ](storsimple-partner-csp-deploy.md)に関するページに進みます。
