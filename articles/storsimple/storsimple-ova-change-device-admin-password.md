---
title: "StorSimple 仮想デバイスの管理者パスワードを変更する |Microsoft Docs"
description: "Azure クラシック ポータルまたは StorSimple Virtual Array の Web UI を使用して、デバイス管理者のパスワードを変更する方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 52acde45-ae29-4edb-9377-46918ab7eef8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85fe4dae6abffebd9d68f3b4110dd45338f35bef


---
# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>StorSimple Virtual Array デバイス管理者パスワードを変更する
## <a name="overview"></a>概要
Windows PowerShell インターフェイスを使用して StorSimple 仮想デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。 StorSimple デバイスを初めてプロビジョニングして起動したときの既定のパスワードは *Password1*です。 データのセキュリティ上、既定のパスワードは初回サインイン時にリセットされ、パスワードを変更するよう求められます。

デバイスを運用環境にデプロイした後は、ローカル Web UI または Azure クラシック ポータルを使用して、いつでもデバイス管理者のパスワードを変更できます。 それぞれの手順については、この記事で説明します。

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Azure クラシック ポータルを使用してパスワードを変更する
Azure クラシック ポータルを使用してデバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Azure クラシック ポータルを使用してデバイス管理者のパスワードを変更するには
1. ポータルで、デバイスの **[デバイス]** > **[構成]** をクリックします。
2. **[デバイス管理者パスワード]** セクションまでスクロールします。 管理者のパスワードは、8 ～ 15 文字で指定してください。 大文字、小文字、数字、および特殊文字を組み合わせたパスワードを使用する必要があります。
3. パスワードを確認入力します。
4. ページの下部にある **[保存]** をクリックします。

これでデバイス管理者のパスワードが更新されました。 この変更されたパスワードを使用して、デバイスにローカルにアクセスすることができます。

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>StorSimple Virtual Array の Web UI を使用してパスワードを変更する
ローカル Web UI を使用してデバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>ローカル Web UI を使用してデバイス管理者のパスワードを変更するには
1. ローカル Web UI で、デバイスの **[メンテナンス]** > **[パスワードの変更]** をクリックします。
   
    ![password1 の変更](./media/storsimple-ova-change-device-admin-password/image40.png)
2. **現在のパスワード**を入力します。
3. **新しいパスワード**を入力します。 パスワードは 8 文字以上で、 大文字、小文字、数字、および特殊文字の 4 種類の文字のうち 3 種類の文字を含める必要があります。
   
    直近 24 個のパスワードは使用できません。
4. 確認のためにパスワードを再入力します。
   
    ![password2 の変更](./media/storsimple-ova-change-device-admin-password/image41.png)
5. ページの下部にある **[適用]**をクリックします。 新しいパスワードが適用されます。 パスワードの変更が失敗した場合は、次のエラーが表示されます。
   
    ![パスワード エラー](./media/storsimple-ova-change-device-admin-password/image42.png)
   
    パスワードが正常に更新されると、通知が表示されます。 この変更されたパスワードを使用して、デバイスにローカルにアクセスすることができます。

## <a name="next-steps"></a>次のステップ
[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。




<!--HONumber=Nov16_HO3-->


