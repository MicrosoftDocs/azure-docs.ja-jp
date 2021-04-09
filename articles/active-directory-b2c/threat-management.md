---
title: リソースやデータに対する脅威を管理する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でのサービス拒否攻撃やパスワード攻撃を検出して軽減する手法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85384976"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でリソースやデータに対する脅威を管理する

Azure Active Directory B2C (Azure AD B2C) には、リソースやデータに対する脅威から保護するうえで役立つ組み込み機能があります。 これらの脅威には、サービス拒否攻撃やパスワード攻撃が含まれます。 サービス拒否攻撃により、目的のユーザーがリソースを使用できなくなることがあります。 パスワード攻撃は、リソースへの不正アクセスにつながります。

## <a name="denial-of-service-attacks"></a>サービス拒否攻撃

Azure AD B2C では、SYN Cookie を使用して SYN フラッド攻撃を防ぎます。 また、Azure AD B2C では、速度と接続の制限を使用して、サービス拒否攻撃から保護します。

## <a name="password-attacks"></a>パスワード攻撃

ユーザーが設定したパスワードには、合理的な複雑さが必要です。 Azure AD B2C は、パスワード攻撃に対する軽減策を備えています。 軽減策には、ブルートフォース パスワード攻撃やディクショナリ パスワード攻撃の検出が含まれます。 Azure AD B2C では、さまざまな信号を使用して、要求の整合性を分析します。 Azure AD B2C は、ハッカーやボットネットから目的のユーザーをインテリジェントに区別するように設計されています。

Azure AD B2C では高度な戦略を使用して、アカウントをロックします。 アカウントは、要求の IP と、入力したパスワードに基づいてロックされます。 また、ロックアウト期間は、攻撃されている可能性に応じて長くなります。 パスワードの試行に 10 回 (既定の試行回数のしきい値) 失敗した後、1 分間ロックアウトされます。 アカウントのロックが解除された後 (つまり、ロックアウト期間が過ぎてアカウントのロックがサービスによって自動的に解除された後)、次のログインに失敗すると、さらに 1 分間ロックアウトされ、ログインに失敗するたびにこの状態が続きます。 同じパスワードを繰り返し入力した場合は、複数回失敗したログインとしてカウントされません。

最初の 10 回のロックアウト期間は、1 分となります。 次の 10 回のロックアウト期間は若干長くなり、その後、10 回のロックアウト期間ごとに長くなります。 ロックアウト カウンターは、アカウントがロックされていない状態でログインに成功した後、0 にリセットされます。 ロックアウト期間は、最長で 5 時間続く場合があります。

## <a name="manage-password-protection-settings"></a>パスワード保護設定を管理する

ロックアウトのしきい値を含むパスワード保護設定を管理するには、以下の手順に従います。

1. [Azure ポータル](https://portal.azure.com)
1. 上部のメニューにある **[ディレクトリとサブスクリプション]** フィルターを使用して、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[セキュリティ]** で、**[認証方法 (プレビュー)]** を選択し、**[パスワード保護]** を選択します。
1. 必要なパスワード保護設定を入力し、**[保存]** を選択します。

    ![Azure portal の Azure AD 設定内パスワード保護ページ](./media/threat-management/portal-02-password-protection.png)
    <br />"***[パスワード保護]** 設定でロックアウトしきい値を 5 に設定*"

## <a name="view-locked-out-accounts"></a>ロックアウトされたアカウントを表示する

ロックアウトされたアカウントについての情報を取得するには、Active Directory の[サインイン アクティビティ レポート](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)を確認します。 **[状態]** で、**[失敗]** を選択します。 **[サインインのエラー コード]** が `50053` の失敗したサインイン試行は、ロックされたアカウントを示しています。

![ロックアウトされたアカウントを示している Azure AD サインイン レポートのセクション](./media/threat-management/portal-01-locked-account.png)

Azure Active Directory でサインイン アクティビティ レポートを表示する方法については、「[サインイン アクティビティ レポートのエラー コード](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)」を参照してください。
