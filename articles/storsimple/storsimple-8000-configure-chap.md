---
title: StorSimple 8000 シリーズ デバイスの CHAP の構成 | Microsoft Docs
description: StorSimple デバイスにチャレンジ ハンドシェイク認証プロトコル (CHAP) を構成する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766507"
---
# <a name="configure-chap-for-your-storsimple-device"></a>StorSimple デバイスの CHAP の構成

このチュートリアルでは、StorSimple デバイスの CHAP を構成する方法について説明します。 この記事の詳しい手順は、StorSimple 8000 シリーズ デバイスに適用されます。

CHAP は、チャレンジ ハンドシェイク認証プロトコルを意味します。 これは、サーバーがリモート クライアントの ID を検証する際に使用する認証スキームです。 この検証は、共有パスワードまたはシークレットに基づいて行われます。 CHAP には、単一方向 (一方向 CHAP) と双方向 (相互 CHAP) とがあります。 一方向 CHAP は、ターゲットがイニシエーターを認証する際に使用されます。 相互 CHAP やリバース CHAP では、ターゲットがイニシエーターを認証した後、イニシエーターがターゲットを認証します。 イニシエーターの認証は、ターゲットの認証なしに実装できますが、 ターゲットの認証は、イニシエーターも実装されている場合にのみ実装できます。

ベスト プラクティスとして、CHAP を使用して iSCSI セキュリティを強化することをお勧めします。

> [!NOTE]
> StorSimple デバイスでは現在 IPSEC がサポートされていないことに注意してください。

StorSimple デバイスの CHAP 設定は、次の方法で構成できます。

* 一方向認証または単一方向認証
* 双方向認証または相互認証、リバース認証

いずれの場合も、デバイスのポータルとサーバーの iSCSI イニシエーター ソフトウェアを構成する必要があります。 この構成の詳細な手順は、次のチュートリアルで説明します。

## <a name="unidirectional-or-one-way-authentication"></a>一方向認証または単一方向認証

一方向認証では、ターゲットがイニシエーターを認証します。 この認証では、StorSimple デバイスで CHAP イニシエーター設定を構成し、ホストで iSCSI イニシエーター ソフトウェアを構成することが必要になります。 StorSimple デバイスおよび Windows ホスト用の詳細な手順を次に説明します。

#### <a name="to-configure-your-device-for-one-way-authentication"></a>一方向認証用にデバイスを構成するには

1. Azure Portal で StorSimple デバイス マネージャー サービスに移動します。 **[デバイス]** をクリックし、CHAP を構成するデバイスを選択してクリックします。 **[デバイスの設定] > [セキュリティ]** に移動します。 **[セキュリティの設定]** ブレードで **[CHAP]** をクリックします。
   
    ![[CHAP イニシエーター]](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. **[CHAP]** ブレードと **[CHAP イニシエーター]** セクションで、次の手順を実行します。
   
   1. CHAP イニシエーターのユーザー名を指定します。
   2. CHAP イニシエーターのパスワードを指定します。
      
      > [!IMPORTANT]
      > CHAP のユーザー名は 233 文字未満にする必要があります。 CHAP のパスワードは 12 ～ 16 文字にする必要があります。 ユーザー名やパスワードをそれより長くすると、Windows ホストで認証エラーとなります。
   
   3. パスワードを確認入力します。

       ![CHAP イニシエーター 2](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. **[保存]** をクリックします。 確認メッセージが表示されます。 **[OK]** をクリックして変更を保存します。

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Windows ホスト サーバーで一方向認証を構成するには
1. Windows ホスト サーバーで、iSCSI イニシエーターを開始します。
2. **[iSCSI イニシエーターのプロパティ]** ウィンドウで、次の手順を実行します。
   
   1. **[探索]** タブをクリックします。
      
       ![[iSCSI イニシエーターのプロパティ]](./media/storsimple-configure-chap/IC740944.png)
   2. **[ポータルの探索]** をクリックします。
3. **[ターゲット ポータルの探索]** ダイアログ ボックスで、次の手順を実行します。
   
   1. デバイスの IP アドレスを指定します。
   2. **[詳細設定]** をクリックします。
      
       ![[ターゲット ポータルの探索]](./media/storsimple-configure-chap/IC740945.png)
4. **[詳細設定]** ダイアログ ボックスで、次の手順を実行します。
   
   1. **[CHAP ログオンを有効にする]** チェック ボックスをオンにします。
   2. **[名前]** フィールドに、Azure portal で CHAP イニシエーターに指定したユーザー名を入力します。
   3. **[ターゲット シークレット]** フィールドに、Azure portal で CHAP イニシエーターに指定したパスワードを入力します。
   4. **[OK]** をクリックします。
      
       ![詳細設定 (全般)](./media/storsimple-configure-chap/IC740946.png)
5. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[ターゲット]** タブに、デバイスの状態が **[接続中]** として表示されます。 StorSimple 1200 デバイスを使用する場合、各ボリュームは iSCSI ターゲットとしてマウントされます。 そのため、ボリュームごとに手順 3 と手順 4 を繰り返す必要があります。
   
    ![個別のターゲットとしてマウントされたボリューム](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > iSCSI の名前を変更した場合は、新しい名前が新しい iSCSI のセッションに使用されます。 新しい設定は、ログオフしてもう一度ログオンするまで、既存のセッションに使用されません。

Windows ホスト サーバーで CHAP を構成する方法の詳細については、「 [追加の考慮事項](#additional-considerations)」を参照してください。

## <a name="bidirectional-or-mutual-authentication"></a>双方向認証または相互認証

双方向認証では、ターゲットがイニシエーターを認証して、イニシエーターがターゲットを認証します。 この手順では、ユーザーがデバイスで CHAP イニシエーター設定とリバース CHAP 設定を構成し、ホストで iSCSI イニシエーター ソフトウェアを構成する必要があります。 デバイスと Windows ホストで相互認証を構成する手順を次に説明します。

#### <a name="to-configure-your-device-for-mutual-authentication"></a>相互認証用にデバイスを構成するには

1. Azure Portal で StorSimple デバイス マネージャー サービスに移動します。 **[デバイス]** をクリックし、CHAP を構成するデバイスを選択してクリックします。 **[デバイスの設定] > [セキュリティ]** に移動します。 **[セキュリティの設定]** ブレードで **[CHAP]** をクリックします。
   
    ![[CHAP ターゲット]](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. このページで下へスクロールし、 **[CHAP ターゲット]** セクションで次の手順を実行します。
   
   1. デバイスの **[リバース CHAP ユーザー名]** を指定します。
   2. デバイスの **[リバース CHAP パスワード]** を指定します。
   3. パスワードを確認入力します。
3. **[CHAP イニシエーター]** セクションで次の手順を実行します。
   
   1. デバイスの **[ユーザー名]** を指定します。
   2. デバイスの **[パスワード]** を指定します。
   3. パスワードを確認入力します。

       ![CHAP イニシエーター 3](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. **[保存]** をクリックします。 確認メッセージが表示されます。 **[OK]** をクリックして変更を保存します。

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Windows ホスト サーバーで双方向認証を構成するには

1. Windows ホスト サーバーで、iSCSI イニシエーターを開始します。
2. **[iSCSI イニシエーターのプロパティ]** ウィンドウで、 **[構成]** タブをクリックします。
3. **[CHAP]** をクリックします。
4. **[iSCSI イニシエーターの相互 CHAP シークレット]** ダイアログ ボックスで、次の手順を実行します。
   
   1. Azure Portal で構成した **[リバース CHAP のパスワード]** を入力します。
   2. **[OK]** をクリックします。
      
       ![[iSCSI イニシエーターの相互 CHAP シークレット]](./media/storsimple-configure-chap/IC740949.png)
5. **[ターゲット]** タブをクリックします。
6. **[接続]** ボタンをクリックします。 
7. **[ターゲットへの接続]** ダイアログ ボックスで、 **[詳細設定]** をクリックします。
8. **[詳細プロパティ]** ダイアログ ボックスで、次の手順を実行します。
   
   1. **[CHAP ログオンを有効にする]** チェック ボックスをオンにします。
   2. **[名前]** フィールドに、Azure portal で CHAP イニシエーターに指定したユーザー名を入力します。
   3. **[ターゲット シークレット]** フィールドに、Azure portal で CHAP イニシエーターに指定したパスワードを入力します。
   4. **[相互認証の実行]** チェック ボックスをオンにします。
      
       ![詳細設定 (相互認証)](./media/storsimple-configure-chap/IC740950.png)
   5. **[OK]** をクリックして、CHAP 構成を完了します。

Windows ホスト サーバーで CHAP を構成する方法の詳細については、「 [追加の考慮事項](#additional-considerations)」を参照してください。

## <a name="additional-considerations"></a>その他の注意点

**クイック接続** 機能は、CHAP が有効になっている接続をサポートしません。 CHAP を有効にした場合、 **[ターゲット]** タブで利用できる **[接続]** ボタンを使用して、ターゲットに接続できることを確認します。

![Connect to target](./media/storsimple-configure-chap/IC740947.png)

表示される **[ターゲットへの接続]** ダイアログ ボックスで、 **[この接続をお気に入りのターゲットの一覧に追加する]** チェック ボックスをオンにします。 このように選択すると、コンピューターを再起動するたびに、iSCSI のお気に入りのターゲットへの接続の復元が試行されます。

## <a name="errors-during-configuration"></a>構成中のエラー

CHAP の構成が正しくない場合、 **"認証エラー"** というエラー メッセージが表示される可能性があります。

## <a name="verification-of-chap-configuration"></a>CHAP の構成の確認

次の手順を実行することで、CHAP が使用されていることを確認します。

#### <a name="to-verify-your-chap-configuration"></a>CHAP の構成を確認するには
1. **[お気に入りのターゲット]** をクリックします。
2. 認証を有効にしたターゲットを選択します。
3. **[詳細]** をクリックします。
   
    ![iSCSI initiator properties favorite targets](./media/storsimple-configure-chap/IC740951.png)
4. **[お気に入りのターゲットの詳細]** ダイアログ ボックスで、 **[認証]** フィールドのエントリを確認します。 正常に構成されている場合、" **CHAP**" と表示されます。
   
    ![Favorite target details](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>次のステップ

* [StorSimple のセキュリティの詳細](storsimple-8000-security.md)
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)の詳細

