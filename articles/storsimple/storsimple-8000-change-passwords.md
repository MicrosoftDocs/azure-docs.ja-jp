---
title: StorSimple パスワードを変更する | Microsoft Docs
description: StorSimple デバイス マネージャー サービスを使用して、StorSimple Snapshot Manager のパスワードとデバイス管理者のパスワードを変更する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023756"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>StorSimple デバイス マネージャー サービスを使用した StorSimple のパスワードの変更

## <a name="overview"></a>概要
Azure Portal の **[デバイスの設定]** オプションには、StorSimple デバイス マネージャー サービスの管理対象の StorSimple デバイスで再構成することができるすべてのデバイス パラメーターが含まれます。 このチュートリアルでは、 **[デバイスの設定]** の **[セキュリティ]** オプションを使用して、デバイス管理者のパスワードまたは StorSimple Snapshot Manager のパスワードを変更する方法について説明します。

## <a name="change-the-device-administrator-password"></a>デバイス管理者のパスワードの変更
Windows PowerShell インターフェイスを使用して StorSimple デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。 サービスに初めて StorSimple デバイスを登録した場合、このインターフェイスの初期パスワードは *Password1* です。 データのセキュリティ上、登録プロセスの最後にこのパスワードを変更することが必須となっています。 このパスワードを変更せずに登録プロセスを終了することはできません。 詳細については、「 [手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください。

登録時に Windows PowerShell インターフェイスを使用して最初に設定したパスワードは、後で Azure Portal から変更できます。 デバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### <a name="to-change-the-device-administrator-password"></a>デバイス管理者のパスワードを変更するには
1. StorSimple デバイス マネージャー サービスに移動し、 **[デバイス]** をクリックします。

2. デバイスの表形式の一覧で、パスワードを変更するデバイスを選択してクリックします。

    ![StorSimple デバイス マネージャー サービスを示すスクリーンショット。 [管理] の [デバイス] が選択されています。 デバイスの一覧で、1 つのデバイスが選択されています。](./media/storsimple-8000-change-passwords/changepwd1.png)

3. **[設定]** ブレードで、 **[デバイスの設定] > [セキュリティ]** に移動します。

    ![デバイス マネージャー サービスの [設定] ブレードを示すスクリーンショット。 [デバイスの設定] で [セキュリティ] が選択されています。](./media/storsimple-8000-change-passwords/changepwd2.png)

4. **[セキュリティ設定]** ブレードで、 **[パスワード]** をクリックしてデバイスの管理者パスワードを変更します。

    ![[セキュリティ設定] ブレードを示すスクリーンショット。 [パスワード] ボタンが強調表示されています。](./media/storsimple-8000-change-passwords/changepwd3.png)

5. **[パスワード]** ブレードで、管理者のパスワードを 8 ～ 15 文字で指定します。 大文字、小文字、数字、特殊文字を 3 つ以上組み合わせたパスワードを使用する必要があります。

6. パスワードを確認入力します。

    ![[パスワード] ブレードを示すスクリーンショット。 [デバイス管理パスワード] では、[新しいパスワード] ボックスと [パスワードの確認] ボックスに値が入力されています。](./media/storsimple-8000-change-passwords/changepwd4.png)

7. **[保存]** をクリックし、確認を求められたら、 **[はい]** をクリックします。

    ![[パスワード] ブレードを示すスクリーンショット。 [保存] ボタンが強調表示されています。](./media/storsimple-8000-change-passwords/changepwd6.png)

これでデバイス管理者のパスワードが更新されました。 この変更されたパスワードを使用して、Windows PowerShell インターフェイスにアクセスすることができます。

## <a name="set-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager のパスワードを設定する
StorSimple Snapshot Manager ソフトウェアは Windows ホスト上に常駐し、管理者が、ローカル スナップショットとクラウド スナップショットの形式で StorSimple デバイスのバックアップを管理することを可能にします。

StorSimple Snapshot Manager でデバイスを構成するとき、ストレージ デバイスを認証するためのパスワードとデバイスの IP アドレスを入力するように求められます。

StorSimple Snapshot Manager のパスワードは、Azure Portal で設定または変更できます。 StorSimple Snapshot Manager のパスワードを設定または変更するには、次の手順を実行します。

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager のパスワードを設定するには
1. StorSimple デバイス マネージャー サービスに移動し、 **[デバイス]** をクリックします。

2. デバイスの表形式の一覧で、StorSimple Snapshot Manager のパスワードを設定または変更するデバイスを選択してクリックします。

     ![StorSimple デバイス マネージャー サービスを示すスクリーンショット。 [管理] の [デバイス] が選択されています。 デバイスの一覧で、1 つのデバイスが選択されています。](./media/storsimple-8000-change-passwords/changepwd1.png)

3. **[設定]** ブレードで、 **[デバイスの設定] > [セキュリティ]** に移動します。

     ![デバイス マネージャー サービスの [設定] ブレードを示すスクリーンショット。 [デバイスの設定] で [セキュリティ] が選択されています。](./media/storsimple-8000-change-passwords/changepwd2.png)

4. **[セキュリティ設定]** ブレードで、 **[パスワード]** をクリックして StorSimple Snapshot Manager のパスワードを設定または変更します。

     ![[セキュリティ設定] ブレードを示すスクリーンショット。 [パスワード] ボタンが強調表示されています。](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. **[パスワード]** ブレードで、14 ～ 15 文字のパスワードを入力します。 大文字、小文字、数字、特殊文字を 3 つ以上組み合わせたパスワードを使用してください。

6. パスワードを確認入力します。

     ![[パスワード] ブレードを示すスクリーンショット。 [Snapshot Manager のパスワード] では、[新しいパスワード] ボックスと [パスワードの確認] ボックスに値が入力されています。](./media/storsimple-8000-change-passwords/changepwd5.png)

7. **[保存]** をクリックし、確認を求められたら、 **[はい]** をクリックします。

     ![[パスワード] ブレードを示すスクリーンショット。 [保存] ボタンが強調表示されています。](./media/storsimple-8000-change-passwords/changepwd6.png)

StorSimple Snapshot Manager のパスワードが更新されました。

## <a name="next-steps"></a>次のステップ
* [StorSimple のセキュリティの詳細](storsimple-8000-security.md)
* [デバイスの構成を変更する方法の詳細](storsimple-8000-modify-device-config.md)
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)の詳細

