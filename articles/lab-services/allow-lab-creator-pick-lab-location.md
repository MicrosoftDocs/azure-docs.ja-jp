---
title: ラボ作成者が Azure Lab Services で場所を選択できるようにする
description: この記事では、ラボ アカウント管理者が、ラボ作成者に対してラボの場所を選択できるようにする方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444201"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>ラボ作成者が Azure Lab Services でラボ用の場所を選択できるようにする
Azure Lab Services では、ラボ アカウントの所有者は、ラボ作成者 (教師) が作成するラボの場所を選択できるようにすることができます。 この場所は、ラボ アカウントの場所と異なっていてもかまいません。 場所は、Azure リージョンのグループです。 たとえば、米国の場所は、米国東部、米国西部などのリージョンのグループです。 

ラボ アカウントの所有者としてラボ アカウントを作成するとき、およびラボ アカウントを作成した後で (つまり既存のラボ アカウントで)、 **[ラボ作成者にラボの場所の選択を許可する]** を選択できます。 

## <a name="at-the-time-of-lab-account-creation"></a>ラボ アカウントを作成するとき
ラボ アカウントを作成するとき、最初の画面 ( **[基本]** タブ) にこのオプションが表示されます。 

![ラボを作成するときにオプションを有効にする](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

**[詳細]** タブでラボ アカウントに対してピア仮想ネットワークを選択した場合、このオプションは無効になります。  

![ピア仮想ネットワークが有効になっている場合](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>ラボ アカウントを作成した後
ラボ アカウントを作成した後は、次の手順のようにして、このオプションを有効または無効にすることができます。 

1. **[ラボ アカウント]** ページで、左側のメニューの **[ラボの設定]** を選択します。
2. ラボ作成者がラボの場所を選択できるようにする場合は、 **[ラボ作成者にラボの場所の選択を許可する]** オプションをオンにします。 無効にすると、ラボ アカウントが存在している同じ場所にラボが自動的に作成されます。 
    
    **[仮想ネットワークのピアリング]** フィールドで仮想ネットワークを選択した場合、このフィールドは無効になっています。 ピア仮想ネットワーク内のリソースにアクセスするためには、ラボ アカウント内のラボがラボ アカウントと同じリージョンに存在する必要があるためです。 
1. ツールバーの **[保存]** を選択します。 

    ![ラボの設定](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>仮想ネットワークも、場所の選択も許可されていない
このシナリオでは、 **[ラボ作成者にラボの場所の選択を許可する]** オプション有効にしてありません。 

![ラボの場所がない](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

その場合は、ラボ作成者 (教師) に対して、ラボの場所を選択するオプションが表示されません。 使用可能なすべてのサイズ オプションに対して、1 時間あたりの料金が表示されます。 ラボを作成すると、ラボ アカウントが存在する Azure リージョンと同じ場所の Azure リージョンに作成されます。 たとえば、ラボ アカウントが **米国西部** にある場合、ラボは **米国中南部** に作成される可能性はありますが、**カナダ東部** には作成されません。 その場所に作成されることを除き、リージョンの選択に関してはいかなる保証もありません。 現在サイズが制限されている場合、ラボ作成者には、通常はサポートされていても現在は使用できないサイズを確認できるチェック ボックスが表示されます。 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>仮想ネットワーク内で、場所の選択が許可されていない
このシナリオでは、ラボ アカウントに対してピア仮想ネットワークが選択されているため、 **[ラボ作成者にラボの場所の選択を許可する]** オプションは無効になります。 その場合、ラボ作成者には前のオプションと同じ画面が表示されます。 すべての VM は仮想ネットワークと同じ Azure リージョンに存在する必要があるため、ラボは仮想ネットワークと同じ Azure リージョンに作成されます。 その特定のリージョンでサイズが制限されている場合、サイズは使用不可として表示されます。 

## <a name="location-selection-is-enabled"></a>場所の選択が有効になっている
**[ラボ作成者にラボの場所の選択を許可する]** を選択した場合、ラボ作成者 (教師) には、ラボを作成するときに場所を選択するオプションが表示されます。 

![ラボの場所を選択する](./media/allow-lab-creator-pick-lab-location/location-selection.png)

そのサイズがあるすべての場所の価格の範囲が表示され、ラボ作成者は場所を選択できます。 ラボは、その場所に対応するいずれかの Azure リージョンに作成されます。

場所が制限されている場合は、既定では一覧に表示されません。 ドロップダウン リストを展開し、 **[このサイズを使用できない場所を表示する]** を選択します。 

![利用できない場所を表示する](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>コスト
以前の価格は、ラボ用に選択した VM のサイズに基づいていました。 現在の価格は、オペレーティング システム (OS)、サイズ、場所の組み合わせに基づいています。 

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボのネットワークとピア仮想ネットワークを接続する](how-to-connect-peer-virtual-network.md)
- [ラボに共有イメージ ギャラリーをアタッチする](how-to-attach-detach-shared-image-gallery.md)
- [ユーザーをラボ所有者として追加する](how-to-add-user-lab-owner.md)
- [ラボのファイアウォール設定の表示](how-to-configure-firewall-settings.md)
- [ラボのその他の設定を構成する](how-to-configure-lab-accounts.md)