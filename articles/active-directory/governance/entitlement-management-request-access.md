---
title: アクセス パッケージを要求する - Azure AD エンタイトルメント管理
description: マイ アクセス ポータルを使用して、Azure Active Directory のエンタイトルメント管理でアクセス パッケージへのアクセスを要求する方法を学習します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996646"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理でアクセス パッケージへのアクセスを要求する

Azure AD のエンタイトルメント管理では、アクセス パッケージにより、そのアクセス パッケージの有効期間中のアクセスを自動的に管理するリソースとポリシーの 1 回限りのセットアップが可能になります。 

アクセス パッケージ マネージャーは、アクセス パッケージにアクセスするにはユーザーの承認を必要とするポリシーを構成できます。 アクセス パッケージへのアクセスが必要なユーザーは、アクセスを取得するための要求を送信できます。 この記事では、アクセス要求を送信する方法について説明します。

## <a name="sign-in-to-the-my-access-portal"></a>マイ アクセス ポータルにサインインする

最初の手順は、アクセス パッケージへのアクセスを要求できる、マイ アクセス ポータルにサインインすることです。

**事前に必要なロール:** 要求元

1. 操作する、プロジェクトまたはビジネス マネージャーからのメールまたはメッセージを見つけます。 メールには、アクセスする必要があるアクセス パッケージへのリンクが含まれているはずです。 リンクは `myaccess` で始まり、ディレクトリ ヒントを含み、アクセス パッケージ ID で終了します。  (米国政府の場合、代わりにドメインが `https://myaccess.microsoft.us` になることがあります。)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. リンクを開きます。

1. マイ アクセス ポータルにサインインします。

    必ず、組織 (職場または学校) のアカウントを使用してください。 わからない場合は、プロジェクトまたはビジネス マネージャーに確認してください。

## <a name="request-an-access-package"></a>アクセス パッケージを要求する

マイ アクセス ポータルでアクセス パッケージが見つかったら、要求を送信できます。

**事前に必要なロール:** 要求元

1. リストでアクセス パッケージを見つけます。  必要に応じて、検索文字列を入力して、 **[名前]** 、 **[カタログ]** 、または **[リソース]** フィルターを選択すると検索できます。

    ![マイ アクセス ポータル - リソース検索](./media/entitlement-management-request-access/my-access-resource-search.png)

1. チェックマークをクリックして、アクセス パッケージを選択します。

1. **[アクセスの要求]** をクリックして、[アクセスの要求] ウィンドウを開きます。

    ![マイ アクセス ポータル - [アクセス パッケージ]](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. **[業務上の正当な理由]** ボックスが表示された場合は、アクセスを必要とする正当な理由を入力します。

1. **[一定期間の要求ですか?]** が有効になっている場合は、 **[はい]** または **[いいえ]** を選択します。

1. 必要に応じて、開始日と終了日を指定します。

    ![マイ アクセス ポータル - [アクセスの要求]](./media/entitlement-management-shared/my-access-request-access.png)

1. 完了したら、 **[送信]** をクリックして要求を送信します。

1. **[要求の履歴]** をクリックして、要求と状態のリストを表示します。

    アクセス パッケージに承認が必要な場合、要求はこの時点で承認待ち状態になります。

### <a name="select-a-policy"></a>ポリシーの選択

適用されるポリシーが複数あるアクセス パッケージへのアクセスを要求する場合は、ポリシーの選択を求められることがあります。 たとえば、アクセス パッケージ マネージャーは、社内の従業員の 2 つのグループ用に 2 つのポリシーを使用してアクセス パッケージを構成する場合があります。 最初のポリシーでは、60 日間アクセスを許可し、承認を要求する場合があります。 2 番目のポリシーでは、2 日間アクセスを許可し、承認を要求しない場合があります。 このシナリオが発生した場合は、使用するポリシーを選択する必要があります。

![マイ アクセス ポータル - [アクセスの要求] - 複数のポリシー](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>要求元情報を入力する

アクセス パッケージへのアクセスを要求できます。その場合、アクセス パッケージへのアクセスが許可されるためには、その前に、業務上の正当な理由および追加の要求元情報が必要になります。 アクセス パッケージにアクセスするために必要なすべての要求元情報を入力してください。

![マイ アクセス ポータル - [アクセスの要求] - 要求元情報を入力する](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>要求を再送信する

アクセス パッケージへのアクセスを要求すると、要求が拒否されたり、承認者が時間内に応答しない場合は、要求が期限切れになったりすることがあります。 アクセスが必要な場合は、再試行して、要求を再送信することができます。 次の手順では、アクセス要求を再送信する方法について説明します。

**事前に必要なロール:** 要求元

1. **マイ アクセス** ポータルにサインインします。

1. 左側のナビゲーション メニューから **[要求の履歴]** をクリックします。

1. 要求を再送信しているアクセス パッケージを検索します。

1. チェック マークをクリックして、アクセス パッケージを選択します。

1. 選択したアクセス パッケージの右側にある青い **[表示]** リンクをクリックします。
    
    ![アクセス パッケージと [表示] リンクを選択します](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    ウィンドウが開き、アクセス パッケージの要求履歴が表示されます。
    
    ![[再送信] ボタンを選択します](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. ウィンドウの下部にある **[再送信]** ボタンをクリックします。

## <a name="cancel-a-request"></a>要求を取り消す

アクセス要求を送信し、要求がまだ **承認待ち** 状態の場合、要求を取り消すことができます。

**事前に必要なロール:** 要求元

1. マイ アクセス ポータルの左側にある **[要求の履歴]** をクリックして、要求と状態のリストを表示します。

1. 取り消す要求の **[表示]** リンクをクリックします。

1. 要求がまだ **承認待ち** 状態の場合は、**[要求の取り消し]** をクリックして要求を取り消すことができます。

    ![マイ アクセス ポータル - [要求の取り消し]](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. **[要求の履歴]** をクリックして、要求が取り消されたことを確認します。

## <a name="next-steps"></a>次の手順

- [アクセス要求を承認または拒否する](entitlement-management-request-approve.md)
- [要求プロセスとメール通知](entitlement-management-process.md)
