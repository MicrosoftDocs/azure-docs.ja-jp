---
title: ネットワーク ルーティング設定の構成
titleSuffix: Azure Storage
description: Azure ストレージ アカウントに対してネットワークのルーティング優先設定を構成し、インターネット経由でクライアントからアカウントにネットワーク トラフィックをルーティングする方法を指定します。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743545"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Azure Storage に対してネットワークのルーティング優先設定を構成する

この記事では、ストレージ アカウントに対して、ネットワークのルーティング優先設定とルート固有のエンドポイントを構成する方法について説明します。 

ネットワークのルーティング優先設定では、インターネット経由でクライアントからアカウントにネットワーク トラフィックをルーティングする方法を指定します。 ルート固有のエンドポイントは、Azure Storage によってお使いのストレージ アカウント用に作成される新しいエンドポイントです。 これらのエンドポイントでは、既定のルーティング優先設定を変更せずに、目的のパス経由でトラフィックがルーティングされます。 詳細については、「[Azure Storage のネットワーク ルーティング優先設定](network-routing-preference.md)」を参照してください。

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>既定のパブリック エンドポイントのルーティング優先設定を構成する

既定では、ストレージ アカウントのパブリック エンドポイントのルーティング優先設定は、Microsoft グローバル ネットワークに設定されています。 ストレージ アカウントのパブリック エンドポイントに対する既定のルーティング優先設定として、Microsoft グローバル ネットワーク ルーティングとインターネット ルーティングのどちらかを選択できます。 これら 2 種類のルーティングの違いについて詳しくは、「[Azure Storage のネットワーク ルーティング優先設定](network-routing-preference.md)」を参照してください。 

ルーティング優先設定をインターネット ルーティングに変更するには:

1. ポータルでストレージ アカウントに移動します。

2. **[設定]** で、 **[ネットワーク]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[ネットワーク] メニュー オプション](./media/configure-network-routing-preference/networking-option.png)

3.  **[ファイアウォールと仮想ネットワーク]** タブの **[ネットワーク ルーティング]** で、 **[ルーティングの優先順位]** 設定を **[Internet routing]\(インターネット ルーティング\)** に変更します。

4.  **[保存]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![インターネット ルーティング オプション](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>ルート固有のエンドポイントを構成する

ルート固有のエンドポイントを構成することもできます。 たとえば、既定のエンドポイントのルーティング優先設定を "*インターネット ルーティング*" に設定してから、インターネット上のクライアントとストレージ アカウントとの間のトラフィックを Microsoft グローバル ネットワーク経由でルーティングできるようにする、ルート固有のエンドポイントを公開できます。

1.  ポータルでストレージ アカウントに移動します。

2.  **[設定]** で、 **[ネットワーク]** を選択します。

3.  **[ファイアウォールと仮想ネットワーク]** タブの **[Publish route-specific endpoints]\(ルート固有のエンドポイントを公開する\)** の下で、ルート固有のエンドポイントのルーティング優先設定を選択し、 **[保存]** をクリックします。 この優先設定は、ルート固有のエンドポイントにのみ影響します。 この優先設定は、既定のルーティング優先設定には影響しません。  

    次の図は、選択された **[Microsoft network routing]\(Microsoft ネットワーク ルーティング\)** オプションを示しています。

    > [!div class="mx-imgBorder"]
    > ![[Microsoft network routing]\(Microsoft ネットワーク ルーティング\) オプション](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>ルート固有のエンドポイントのエンドポイント名を確認する

ルート固有のエンドポイントを構成した場合は、ストレージ アカウントのプロパティでそのエンドポイントを確認することができます。

1.  **[設定]** の **[プロパティ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[プロパティ] メニュー オプション](./media/configure-network-routing-preference/properties.png)

2.  ルーティング優先設定がサポートされているサービスごとに、**Microsoft ネットワーク ルーティング** エンドポイントが表示されます。 次の図は、BLOB とファイル サービスのエンドポイントを示しています。

    > [!div class="mx-imgBorder"]
    > ![ルート固有のエンドポイントの Microsoft ネットワーク ルーティング オプション](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>関連項目

- [ネットワーク ルーティング設定](network-routing-preference.md)
- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](storage-network-security.md)
- [BLOB ストレージのセキュリティに関する推奨事項](../blobs/security-recommendations.md)
