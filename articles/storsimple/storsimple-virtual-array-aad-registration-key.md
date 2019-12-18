---
title: StorSimple Virtual Array の新しい認証 | Microsoft Docs
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 723d5e969ba2f635724ffa50d562a7abaf936dcf
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517135"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>StorSimple の新しい認証を使用する

## <a name="overview"></a>概要

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

StorSimple デバイス マネージャー サービスは Microsoft Azure で実行され、複数の StorSimple Virtual Array に接続します。 これまで、StorSimple デバイス マネージャー サービスは、StorSimple デバイスへのサービスの認証に Access Control Service (ACS) を使用しました。 ACS メカニズムは間もなく非推奨となり、Azure Active Directory (AAD) 認証に置き換えられる予定です。

この記事に記載されている内容は、両方の StorSimple 1200 シリーズ Virtual Array にのみ適用されます。 この記事では、StorSimple デバイスに適用される AAD 認証と関連付けられている新しいサービス登録キー、およびファイアウォール規則の変更について詳しく説明します。

AAD 認証は、更新プログラム 1 以降を実行している StorSimple Virtual Array (モデル 1200) で発生します。

AAD 認証の導入により、以下で変更が発生します。

- ファイアウォール規則の URL パターン。
- サービス登録キー。

以降のセクションでは、こうした変更について詳しく説明します。

## <a name="url-changes-for-aad-authentication"></a>AAD 認証に伴う URL 変更

サービスによって AAD ベースの認証が確実に使用されるように、すべてのユーザーが、ファイアウォール規則に新しい認証 URL を含める必要があります。

StorSimple Virtual Array を使用する場合は、次の URL がファイアウォール規則に含まれていることを確認します。

| URL パターン                         | クラウド | コンポーネント/機能         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |AAD 認証サービス      |
| `https://login.microsoftonline.us` | 米国政府 |AAD 認証サービス      |

StorSimple Virtual Array の URL パターンの完全な一覧については、「[ファイアウォール ルールの URL パターン](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)」を参照してください。

廃止日が過ぎた後、認証 URL がファイアウォール規則に含まれていない場合、StorSimple デバイスがサービスで認証できなかったことを示す重要なアラートが、ユーザーに表示されます。 サービスは、デバイスと通信できなくなります。 このアラートが表示されると、ユーザーは新しい認証 URL を含める必要があります。 アラートの詳細については、[アラートを使用した StorSimple デバイスの監視](storsimple-virtual-array-manage-alerts.md#networking-alerts)に関するトピックをご覧ください。

## <a name="device-version-and-authentication-changes"></a>デバイスのバージョンと認証の変更

StorSimple Virtual Array を使用している場合は、次の表を使用して、実行する必要があるアクションを、デバイス ソフトウェア バージョンに基づいて判断します。

| デバイスの状況  | 実行するアクション                                    |
|----------------------------|--------------------------------------------------------------|
| 更新プログラム 1.0 以降が実行中で、オフライン。 <br> URL がホワイトリストに登録されていないことを示すアラートが表示される。| 1.認証 URL を含めるようにファイアウォール規則を変更します。 [認証 URL](#url-changes-for-aad-authentication) に関するセクションをご覧ください。 <br> 2.[サービスから AAD 登録キーを取得します](#aad-based-registration-keys)。 <br> 手順 3.手順 1. ～ 5. を実行して、[仮想アレイの Windows PowerShell インターフェイスに接続します](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)。<br> 4.`Invoke-HcsReRegister` コマンドレットを使用して、Windows PowerShell でデバイスを登録します。 前の手順で取得したキーを指定します。|
| 更新プログラム 1.0 以降が実行中で、デバイスはオンライン。| 操作は必要ありません。                                       |
| 更新プログラム 0.6 以前が実行中で、デバイスはオフライン。 | 1.[カタログ サーバーを使用して更新プログラム 1.0 をダウンロードします](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix)。<br>2.[ローカル Web UI を使用して更新プログラム 1.0 を適用します](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix)。<br>3.[サービスから AAD 登録キーを取得します](#aad-based-registration-keys)。 <br>4.手順 1. ～ 5. を実行して、[仮想アレイの Windows PowerShell インターフェイスに接続します](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)。<br>5.`Invoke-HcsReRegister` コマンドレットを使用して、Windows PowerShell でデバイスを登録します。 前の手順で取得したキーを指定します。|
| 更新プログラム 0.6 以前が実行中で、デバイスはオンライン | 認証 URL を含めるようにファイアウォール規則を変更します。<br> Azure Portal を使用して Update 1.0 をインストールします。 |

## <a name="aad-based-registration-keys"></a>AAD ベースの登録キー

StorSimple Virtual Array の更新プログラム 1.0 以降、新しい AAD ベースの登録キーが使用されます。 この登録キーを使用して、StorSimple デバイス マネージャー サービスをデバイスに登録します。

更新プログラム 0.6 以前 を実行している StorSimple Virtual Array を使用している場合は、新しい AAD サービス登録キーを使用することはできません。 サービス登録キーを再生成する必要があります。 キーを再生成した場合、その新しいキーは、その後のすべてのデバイスを登録するときに使用されます。 以降、以前のキーは無効になります。

- 新しい AAD 登録キーは 3 日後に期限が切れます。
- AAD 登録キーは、更新プログラム 1 以降を実行している StorSimple 1200 シリーズ Virtual Array でのみ動作します。 StorSimple 8000 シリーズ デバイスの AAD 登録キーは機能しません。
- AAD 登録キーは、対応する ACS 登録キーよりも長くなっています。

AAD サービス登録キーを生成するには、次の手順を実行します。

#### <a name="to-generate-the-aad-service-registration-key"></a>AAD サービス登録キーを生成するには

1. **StorSimple デバイス マネージャー**で、 **[管理]&gt;** **[キー]** の順に移動します。
    
    ![[キー] に移動する](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. **[キーの生成]** をクリックします。

    ![[再生成] をクリック](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. 新しいキーをコピーします。 以前のキーは動作しません。

    ![再生成を確認する](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>次の手順

* [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md) をデプロイする方法の詳細を確認します
