---
title: Azure DevTest Labs のラボへお知らせを投稿する | Microsoft Docs
description: Azure DevTest Labs で、ラボへの最近の変更や追加事項をユーザーに通知する、カスタムお知らせを既存のラボに投稿する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 72c6ed7ab691ece094673ff5a158ffa769a9f7a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270769"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs のラボへお知らせを投稿する

ラボの管理者は、最近の変更や、ラボへの追加事項についてユーザーに通知するため、既存のラボへカスタムお知らせを投稿できます。 たとえば、次のような情報をユーザーに通知できます。

- 使用可能な新しい VM サイズ
- 現在使用できないイメージ
- ラボ ポリシーの更新事項

投稿されたお知らせは、ラボの [概要] ページに表示され、ユーザーが選択すると詳細が表示されます。

お知らせ機能は一時的な通知を目的としています。  不要になったら、お知らせを簡単に無効にすることができます。

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>既存のラボにお知らせを投稿する手順

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. 必要に応じて、 **[All Services]\(その他のサービス\)** を選択し、一覧から **[DevTest Labs]** を選択します。 (お使いのラボは、 **[すべてのリソース]** の [ダッシュボード] に既に表示されている場合があります)。
1. ラボの一覧から、お知らせを投稿するラボを選択します。
1. ラボの **[概要]** で、 **[Configuration and policies]\(構成とポリシー\)** を選択します。

    ![[Configuration and policies]\(構成とポリシー\) ボタン](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. **[設定]** の左側にある **[Lab announcement]\(ラボのお知らせ\)** を選択します。

    ![[Lab announcement]\(ラボのお知らせ\) ボタン](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. このラボのユーザーへのメッセージを作成するには **[有効]** を **[はい]** に設定します。

1. **有効期限日** を入力して、その日を過ぎると通知がユーザーに表示されないようにできます。 有効期限日を入力しない場合、通知は無効にするまで残ります。

   > [!NOTE]
   > 有効期限日が過ぎた通知は、ユーザーに表示されなくなります。ただし、 **[ラボのお知らせ]** ウィンドウには残ります。 それを編集または再有効化して、再度アクティブにすることができます。
   >
   >

1. **[お知らせのタイトル]** と **[お知らせのテキスト]** を入力します。

   タイトルは 100 文字まで入力でき、ラボの [概要] ページに表示されます。 ユーザーがタイトルを選択すると、お知らせのテキストが表示されます。

   お知らせのテキストでは、マークダウンが使用できます。 お知らせのテキストを入力すると同時に、画面の下部にある [プレビュー] 領域でメッセージを確認できます。

    ![メッセージを作成するラボのお知らせ画面。](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. お知らせを投稿する準備ができたら、 **[保存]** を選択します。

このお知らせをラボのユーザーに表示する必要がなくなったら、 **[Lab announcement]\(ラボのお知らせ\)** ページに戻り、 **[有効]** を **[いいえ]** に設定します。 有効期限日を指定した場合は、その日付を過ぎると通知が自動的に無効になります。

## <a name="steps-for-users-to-view-an-announcement"></a>ユーザーがお知らせを表示するための手順

1. [[Azure ポータル]](https://go.microsoft.com/fwlink/p/?LinkID=525040) から、ラボを選択します。

1. ラボにお知らせが投稿された場合は、ラボの [概要] ページの上部に情報通知が表示されます。 この情報通知は、お知らせの作成時に指定されたお知らせのタイトルです。

    ![[概要] ページのラボのお知らせ](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. ユーザーは、メッセージを選択して、お知らせ全体を表示できます。

    ![ラボのお知らせの詳細](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
次の例に示すように、Azure Resource Manager テンプレートの一部としてお知らせを指定できます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

次のいずれかの方法を使用して、Azure Resource Manager テンプレートをデプロイできます。

- [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>次のステップ
* ラボのポリシーを設定または変更した場合は、ユーザーに通知するお知らせを投稿することをお勧めします。 カスタマイズしたポリシーを使用して、サブスクリプションに制限と規則を適用する方法については[ポリシーとスケジュールの設定](devtest-lab-set-lab-policy.md)に関するページを参照してください。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を検索します。
