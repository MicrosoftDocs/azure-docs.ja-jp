---
title: Azure で StorSimple 8000 デバイス マネージャー サービスの新しい認証を使用する | Microsoft Docs
description: サービスに対して AAD ベースの認証を使用して、新しい登録キーを生成し、デバイスの手動登録を実行する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 88badf6de5e7181763dd72c7a51262d16697bffd
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963491"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>StorSimple の新しい認証を使用する

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要

StorSimple デバイス マネージャー サービスは Microsoft Azure で実行され、複数の StorSimple デバイスに接続します。 これまで、StorSimple デバイス マネージャー サービスは、StorSimple デバイスへのサービスの認証に Access Control Service (ACS) を使用しました。 ACS メカニズムは間もなく非推奨となり、Azure Active Directory (AAD) 認証に置き換えられる予定です。 詳細については、次の ACS 廃止と AAD 認証の使用に関するお知らせをご覧ください。

- [Azure ACS の後継、Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Microsoft Access Control Service に対する今後の変更](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

この記事では、StorSimple デバイスに適用される AAD 認証と、これに関連付けられている新しいサービス登録キー、およびファイアウォール規則の変更について詳しく説明します。 この記事に記載されている内容は、StorSimple 8000 シリーズ デバイスにのみ適用されます。

AAD 認証は、Update 5 以降を実行している StorSimple 8000 シリーズ デバイスで行われます。 AAD 認証の導入により、以下で変更が発生します。

- ファイアウォール規則の URL パターン。
- サービス登録キー。

以降のセクションでは、こうした変更について詳しく説明します。

## <a name="url-changes-for-aad-authentication"></a>AAD 認証に伴う URL 変更

サービスによって AAD ベースの認証が確実に使用されるように、すべてのユーザーが、ファイアウォール規則に新しい認証 URL を含める必要があります。

StorSimple 8000 シリーズを使用する場合は、次の URL がファイアウォール規則に含まれていることを確認します。

| URL パターン                         | クラウド | コンポーネント/機能         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |AAD 認証サービス      |
| `https://login.microsoftonline.us` | 米国政府 |AAD 認証サービス      |

StorSimple 8000 シリーズ デバイスの URL パターンの完全な一覧については、「[ファイアウォール ルールの URL パターン](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)」を参照してください。

廃止日が過ぎた後、認証 URL がファイアウォール規則に含まれていない場合、デバイスが Update 5 を実行しているときは、URL のアラートがユーザーに表示されます。 ユーザーは新しい認証 URL を含める必要があります。 Update 5 より前のバージョンがデバイスで実行されている場合、ユーザーにはハートビート アラートが表示されます。 いずれの場合も、StorSimple デバイスはサービスを認証できず、サービスはデバイスと通信できません。

## <a name="device-version-and-authentication-changes"></a>デバイスのバージョンと認証の変更

StorSimple 8000 シリーズ デバイスを使用している場合は、次の表を使用して、実行する必要があるアクションを、動作しているデバイス ソフトウェア バージョンに基づいて判断します。

| デバイスの状況| 実行するアクション                                    |
|--------------------------|------------------------|
| Update 5 以降が実行中で、デバイスはオフライン。 <br> URL がホワイトリストに登録されていないことを示すアラートが表示される。|1.認証 URL を含めるようにファイアウォール規則を変更します。 [認証 URL](#url-changes-for-aad-authentication) に関するセクションをご覧ください。<br>2.[サービスから AAD 登録キーを取得します](#aad-based-registration-keys)。<br>3.[StorSimple 8000 シリーズ デバイスの Windows PowerShell インターフェイスに接続します](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)。<br>4.`Redo-DeviceRegistration` コマンドレットを使用して、Windows PowerShell でデバイスを登録します。 前の手順で取得したキーを指定します。|
| Update 5 以降が実行中で、デバイスはオンライン。| 操作は必要ありません。                                       |
| Update 4 以前が実行中で、デバイスはオフライン。 |1.認証 URL を含めるようにファイアウォール規則を変更します。<br>2.[カタログ サーバーを使用して Update 5 をダウンロードします](storsimple-8000-install-update-5.md#download-updates-for-your-device)。<br>3.[修正プログラムを使用して Update 5 を適用します](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix)。<br>4.[サービスから AAD 登録キーを取得します](#aad-based-registration-keys)。<br>5.[StorSimple 8000 シリーズ デバイスの Windows PowerShell インターフェイスに接続します](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)。 <br>6.`Redo-DeviceRegistration` コマンドレットを使用して、Windows PowerShell でデバイスを登録します。 前の手順で取得したキーを指定します。|
| Update 4 以前が実行中で、デバイスはオンライン。 |認証 URL を含めるようにファイアウォール規則を変更します。<br> Azure Portal を使用して Update 5 をインストールします。              |
| Update 5 より前のバージョンの出荷時の設定にリセット済み。      |ポータルには AAD ベースの登録キーが表示されますが、デバイスでは古いソフトウェアが実行されています。 前述の、デバイスで Update 4 以前が実行されている場合のシナリオの手順を実行します。              |

## <a name="aad-based-registration-keys"></a>AAD ベースの登録キー

StorSimple 8000 シリーズ デバイスの Update 5 以降は、新しい AAD ベースの登録キーが使用されます。 この登録キーを使用して、StorSimple デバイス マネージャー サービスをデバイスに登録します。

Update 4 以前を実行している StorSimple 8000 シリーズ デバイス (アクティブ化されている以前のデバイスを含む) を使用している場合は、新しい AAD サービス登録キーを使用することはできません。
このシナリオでは、サービス登録キーを再生成する必要があります。 キーを再生成した場合、その新しいキーは、その後のすべてのデバイスを登録するときに使用されます。 以降、以前のキーは無効になります。

- 新しい AAD 登録キーは 3 日後に期限が切れます。
- AAD 登録キーは、Update 5 以降を実行している StorSimple 8000 シリーズ デバイスでのみ動作します。
- AAD 登録キーは、対応する ACS 登録キーよりも長くなっています。

AAD サービス登録キーを生成するには、次の手順を実行します。

#### <a name="to-generate-the-aad-service-registration-key"></a>AAD サービス登録キーを生成するには

1. **StorSimple デバイス マネージャー**で、 **[管理]&gt;** **[キー]** の順に移動します。 検索バーを使用して、"_キー_" を検索することもできます。
    
2. **[キーの生成]** をクリックします。

    ![[再生成] をクリック](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. 新しいキーをコピーします。 以前のキーは動作しなくなります。

    ![再生成を確認する](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > StorSimple 8000 シリーズ デバイスに登録されているサービスで StorSimple Cloud Appliance を作成する場合は、作成中に登録キーを生成しないでください。 作成が完了するまで待ち、その後、登録キーを生成します。

## <a name="next-steps"></a>次の手順

* [StorSimple 8000 シリーズ デバイス](storsimple-8000-deployment-walkthrough-u2.md)をデプロイする方法に関する詳細を確認します。

