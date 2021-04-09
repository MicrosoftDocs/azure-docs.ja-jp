---
title: Azure Marketplace でテナント ID、オブジェクト ID、パートナーの関連付けの詳細を検索する
description: Azure Marketplace でサブスクリプション ID におけるテナント ID、オブジェクト ID、パートナーの関連付けの詳細を検索する方法。
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109345"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>テナント ID、オブジェクト ID、パートナーの関連付けの詳細を検索する

この記事では、テナント ID、オブジェクト ID、パートナーの関連付けの詳細を、それぞれのサブスクリプション ID と共に検索する方法について説明します。

デバッグ支援に使用するために Azure Cloud Shell でこれらの項目のスクリーンショットを取得する必要がある場合は、[デバッグのためにテナント、オブジェクト、パートナー ID の関連付けを検索](#find-ids-for-debugging)する方法に関するページに移動してください。

>[!Note]
> これらの手順を行う権限があるのは、サブスクリプションの所有者だけです。

## <a name="find-tenant-id"></a>テナント ID を検索する

1. [Azure ポータル](https://ms.portal.azure.com/)にアクセスします。
2. **[Azure Active Directory]** を選択します。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Azure portal の [Azure Active Directory] アイコン。":::

3. **[概要]** を選択します。 テナント ID が **[基本情報]** の下に表示されるはずです。

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Azure portal で [グループ] を選択します。":::

## <a name="find-subscriptions-and-roles"></a>サブスクリプションとロールを検索する

1. Azure portal に移動し、上記の手順 1 と 2 の説明に従って **[Azure Active Directory]** を選択します。
2. **[サブスクリプション]** を選択します。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Azure portal での [サブスクリプション] アイコン。":::

3. サブスクリプションとロールを表示します。

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Azure portal での [サブスクリプション] 画面。":::

## <a name="find-partner-id"></a>パートナー ID を検索する

1. 前のセクションの説明に従って [サブスクリプション] ページに移動します。
2. サブスクリプションを選択します。
3. **[課金]** で、 **[パートナー情報]** を選択します。

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="左側のナビゲーション メニューの [パートナー情報]。":::

## <a name="find-user-object-id"></a>ユーザー (オブジェクト ID) を検索する

1. 適切な管理者権限を持つ目的のテナント内のアカウントを使用して [Office 365 管理者ポータル](https://portal.office.com/adminportal/home)にサインインします。
2. 左側のメニューの下部にある **[管理センター]** セクションを展開してから、[Azure Active Directory] オプションを選択して、新しいブラウザー ウィンドウで管理コンソールを起動します。
3. **[ユーザー]** を選択します。
4. 目的のユーザーを参照または検索してから、アカウント名を選択して、ユーザー アカウントのプロファイル情報を表示します。
5. オブジェクト ID は、右側の [ID] セクションにあります。

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory 管理センター。":::

6. 左側のメニューの **[アクセス制御 (IAM)]** を選択してから、 **[ロールの割り当て]** を選択することで、**ロールの割り当て** を検索します。

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Azure リソースに対するロールの割り当て。":::

## <a name="find-ids-for-debugging"></a>デバッグのために ID を検索する

このセクションでは、デバッグ目的でテナント、オブジェクト、パートナー ID の関連付けを検索する方法について説明します。

1. [Azure ポータル](https://ms.portal.azure.com/)にアクセスします。
2. 右上にある [PowerShell] アイコンを選択して Azure Cloud Shell を開きます。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="画面の右上にある [PowerShell] アイコン。":::

3. **[PowerShell]** を選択します。

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="PowerShell リンクを選択します。":::

4. **[サブスクリプション]** ボックスを選択してパートナーのリンク先を選び、次に **[ストレージの作成]** を選択します。 これは 1 回限りの操作です。既にストレージが設定されている場合は、次の手順に進んでください。

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="[ストレージの作成] ボタンを選択します。":::

5. ストレージを作成する (または既に用意されている) と、Azure Cloud Shell ウィンドウが開きます。

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Azure Cloud Shell ウィンドウ。":::

6. パートナーの関連付けの詳細については、次のコマンドを使用して拡張機能をインストールします。<br>`az extension add --name managementpartner`.<br>拡張機能が既にインストールされているかどうかは Azure Cloud Shell から通知があります。

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="拡張機能が既にインストールされていることを示す [Azure Cloud Shell] ウィンドウ。":::

7. 次のコマンドを使用して、パートナーの詳細を確認します。<br>`az managementpartner show --partner-id xxxxxx`<br>例: `az managementpartner show --partner-id 4760962`.<br>コマンドの結果のスクリーンショットをスナップします。次のようになります。

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="パートナー ID を表示するための前のコマンドの結果を示すサンプル画面。":::

>[!NOTE]
>複数のサブスクリプションでスクリーンショットが必要な場合は、次のコマンドを使用してサブスクリプションを切り替えます。<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>次の手順

- [サポートされている国とリージョン](sell-from-countries.md)