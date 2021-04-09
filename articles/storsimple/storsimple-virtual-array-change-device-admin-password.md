---
title: StorSimple Virtual Array デバイス管理者のパスワードを変更する |Microsoft Docs
description: Azure Portal または StorSimple Virtual Array の Web UI を使用して、デバイス管理者のパスワードを変更する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bc8846d546faec194617ccb753cdbd105e16bf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85513622"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>StorSimple デバイス マネージャー を介して StorSimple Virtual Array デバイス管理者のパスワードを変更する

## <a name="overview"></a>概要

Windows PowerShell インターフェイスを使用して StorSimple Virtual Array にアクセスする際、デバイス管理者のパスワードの入力が求められます。 StorSimple デバイスを初めてプロビジョニングして起動したときの既定のパスワードは *Password1* です。 データのセキュリティ上、既定のパスワードは初回サインイン時にリセットされ、パスワードを変更するよう求められます。

デバイスを運用環境にデプロイした後は、ローカル Web UI または Azure Portal を使用して、いつでもデバイス管理者のパスワードを変更できます。 それぞれの手順については、この記事で説明します。

 ![[デバイス] ブレード](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Azure Portal を使用してパスワードを変更する

Azure Portal を使用してデバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Azure Portal を使用してデバイス管理者のパスワードを変更するには

1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックします。次に、**[管理]** セクションで、**[デバイス]** をクリックします。 **[デバイス]** ブレードが開き、StorSimple Virtual Array デバイスの一覧が表示されます。

2. **[デバイス]** ブレードで、パスワードを変更する必要があるデバイスをダブルクリックします。

3. デバイスの **[設定]** ブレードで、**[セキュリティ]** をクリックします。

4. **[セキュリティ設定]** ブレードで、次の操作を行います。
   
   1. **[デバイス管理者パスワード]** セクションまでスクロールします。 管理者のパスワードは、8 ～ 15 文字で指定してください。
   2. パスワードを確認入力します。
   3. ブレードの上部にある **[保存]** をクリックします。

これで、デバイス管理者のパスワードが変更されました。 この変更されたパスワードを使用して、デバイスにローカルにアクセスすることができます。

![[セキュリティ設定] ブレード](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>ローカル Web UI を使用してパスワードを変更する

ローカル Web UI を使用してデバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>ローカル Web UI を使用してデバイス管理者のパスワードを変更するには

1. ローカル Web UI で、デバイスの **[メンテナンス]**  >  **[パスワードの変更]** をクリックします。
   
    ![password1 の変更](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. **現在のパスワード** を入力します。
3. **新しいパスワード** を入力します。 パスワードは 8 文字以上で、 大文字、小文字、数字、および特殊文字の 4 種類の文字のうち 3 種類の文字を含める必要があります。
   
    直近 24 個のパスワードは使用できません。
4. 確認のためにパスワードを再入力します。
   
    ![password2 の変更](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. ページの下部にある **[適用]** をクリックします。 これで、新しいパスワードが適用されました。 パスワードの変更が失敗した場合は、次のエラーが表示されます。
   
    ![パスワード エラー](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    パスワードが正常に更新されると、その旨が通知されます。 この変更されたパスワードを使用して、デバイスにローカルにアクセスすることができます。


## <a name="next-steps"></a>次のステップ
[StorSimple Virtual Array を管理する](storsimple-ova-web-ui-admin.md)方法を確認します。

