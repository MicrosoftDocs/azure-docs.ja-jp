---
title: Azure AD のエンタイトルメント管理 (プレビュー) でアクセス パッケージへのアクセスを要求する - Azure Active Directory
description: マイ アクセス ポータルを使用して、Azure Active Directory のエンタイトルメント管理 (プレビュー) でアクセス パッケージへのアクセスを要求する方法を学習します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578f78d1853194cad000aa2e54b40d1ac5003544
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489042"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD のエンタイトルメント管理 (プレビュー) でアクセス パッケージへのアクセスを要求する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) のエンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="sign-in-to-the-my-access-portal"></a>マイ アクセス ポータルにサインインする

最初の手順は、アクセス パッケージへのアクセスを要求できる、マイ アクセス ポータルにサインインすることです。

**前提となるロール:** 要求元

1. 操作する、プロジェクトまたはビジネス マネージャーからのメールまたはメッセージを見つけます。 メールには、アクセスする必要があるアクセス パッケージへのリンクが含まれているはずです。 リンクは以下で始まります。

    `https://myaccess.microsoft.com`

1. リンクを開きます。

1. マイ アクセス ポータルにサインインします。

    必ず、組織のアカウントを使用してください。 わからない場合は、プロジェクトまたはビジネス マネージャーに確認してください。

## <a name="request-an-access-package"></a>アクセス パッケージを要求する

マイ アクセス ポータルでアクセス パッケージが見つかったら、要求を送信できます。

**前提となるロール:** 要求元

1. チェックマークをクリックして、アクセス パッケージを選択します。

    ![マイ アクセス ポータル - [アクセス パッケージ]](./media/entitlement-management-shared/my-access-access-packages.png)

1. **[アクセス権の要求]** をクリックして、[アクセス権の要求] ウィンドウを開きます。

1. **[業務上の正当な理由]** ボックスが表示された場合は、アクセスを必要とする正当な理由を入力します。

1. **[一定期間の要求ですか?]** が有効になっている場合は、 **[はい]** または **[いいえ]** を選択します。

1. 必要に応じて、開始日と終了日を指定します。

    ![マイ アクセス ポータル - [アクセス権の要求]](./media/entitlement-management-shared/my-access-request-access.png)

1. 完了したら、 **[送信]** をクリックして要求を送信します。

1. **[要求の履歴]** をクリックして、要求と状態のリストを表示します。

    アクセス パッケージに承認が必要な場合、要求はこの時点で承認待ち状態になります。

## <a name="cancel-a-request"></a>要求を取り消す

アクセス要求を送信し、要求がまだ**承認待ち**状態の場合、要求を取り消すことができます。

**前提となるロール:** 要求元

1. マイ アクセス ポータルの左側にある **[要求の履歴]** をクリックして、要求と状態のリストを表示します。

1. 取り消す要求の **[表示]** リンクをクリックします。

1. 要求がまだ**承認待ち**状態の場合は、 **[要求の取り消し]** をクリックして要求を取り消すことができます。

    ![マイ アクセス ポータル - [要求の取り消し]](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. **[要求の履歴]** をクリックして、要求が取り消されたことを確認します。

## <a name="select-a-policy"></a>ポリシーの選択

適用されるポリシーが複数あるアクセス パッケージへのアクセスを要求する場合は、ポリシーの選択を求められることがあります。 たとえば、アクセス パッケージ マネージャーは、社内の従業員の 2 つのグループ用に 2 つのポリシーを使用してアクセス パッケージを構成する場合があります。 最初のポリシーでは、60 日間アクセスを許可し、承認を要求する場合があります。 2 番目のポリシーでは、2 日間アクセスを許可し、承認を要求しない場合があります。 このシナリオが発生した場合は、使用するポリシーを選択する必要があります。

**前提となるロール:** 要求元

## <a name="next-steps"></a>次の手順

- [アクセス要求を承認または拒否する](entitlement-management-request-approve.md)
- [要求プロセスとメール通知](entitlement-management-process.md)
