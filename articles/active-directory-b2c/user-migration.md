---
title: ユーザーの移行方法
titleSuffix: Azure AD B2C
description: 事前移行またはシームレスな移行の方法を使用して、別の ID プロバイダーのユーザー アカウントを Azure AD B2C に移行します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2d4a61f653c5bedb31223d2eb3d37b92a076821
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010169"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>ユーザーを Azure AD B2C に移行する

別の ID プロバイダーから Azure Active Directory B2C (Azure AD B2C) に移行する場合、既存のユーザー アカウントの移行も必要になることがあります。 ここでは *事前移行* と *シームレスな移行* という 2 つの移行方法について説明します。 どちらの方法でも、[Microsoft Graph API](microsoft-graph-operations.md) を使用して Azure AD B2C にユーザー アカウントを作成するアプリケーションまたはスクリプトを記述する必要があります。

## <a name="pre-migration"></a>事前移行

事前移行フローでは、移行アプリケーションはユーザー アカウントごとに次の手順を実行します。

1. 古い ID プロバイダーから、現在の資格情報 (ユーザー名とパスワード) を含むユーザー アカウントを読み取ります。
1. 現在の資格情報を使用して、対応するアカウントを Azure AD B2C ディレクトリに作成します。

事前移行フローは、次の 2 つの状況のいずれかで使用します。

- ユーザーのプレーンテキストの資格情報 (ユーザー名とパスワード) にアクセスできる。
- 資格情報が暗号化されているが、復号化できる。

プログラムによってユーザー アカウントを作成する方法の詳細については、「[Microsoft Graph を使用して Azure AD B2C ユーザー アカウントを管理する](microsoft-graph-operations.md)」を参照してください。

## <a name="seamless-migration"></a>シームレスな移行

古い ID プロバイダーのプレーンテキスト パスワードにアクセスできない場合は、シームレスな移行フローを使用します。 たとえば、次のような場合です。

- パスワードが (ハッシュ関数を使用した場合のように) 一方向の暗号化形式で格納されている。
- パスワードがレガシの ID プロバイダーによって、ユーザーがアクセスできない方法で格納されている。 たとえば、ID プロバイダーが Web サービスを呼び出して資格情報を検証している場合。

シームレスな移行フローではユーザー アカウントの事前移行がやはり必要ですが、その後、[カスタム ポリシー](custom-policy-get-started.md)を使用して [REST API](custom-policy-rest-api-intro.md) (ユーザーが作成したもの) へのクエリを実行し、最初のサインイン時に各ユーザーのパスワードを設定します。

このため、シームレスな移行フローには、*事前移行* と *資格情報の設定* の 2 つのフェーズがあります。

### <a name="phase-1-pre-migration"></a>フェーズ 1:事前移行

1. 移行アプリケーションは、古い ID プロバイダーからユーザー アカウントを読み取ります。
1. 移行アプリケーションによって、Azure AD B2C ディレクトリに対応するユーザー アカウントが作成されますが、ユーザーが生成する "*ランダムなパスワードが設定されます*"。

### <a name="phase-2-set-credentials"></a>フェーズ 2:資格情報の設定

アカウントの事前移行が完了した後、カスタム ポリシーと REST API は、ユーザーがサインインしたときに次のことを実行します。

1. 入力した電子メール アドレスに対応する Azure AD B2C ユーザー アカウントを読み取ります。
1. ブール型の拡張属性を評価して、アカウントに移行のフラグが設定されているかどうかを確認します。
    - 拡張属性が `True` を返す場合は、REST API を呼び出して、レガシ ID プロバイダーに対してパスワードを検証します。
      - REST API によってパスワードが正しくないと判断された場合は、わかりやすいエラーをユーザーに返します。
      - REST API によってパスワードが正しいと判断された場合は、パスワードを Azure AD B2C アカウントに書き込み、ブール型の拡張属性を `False` に変更します。
    - ブール型拡張属性が `False` を返す場合は、通常どおりサインイン プロセスを続行します。

カスタム ポリシーと REST API の例については、GitHub の[シームレスなユーザー移行サンプル](https://aka.ms/b2c-account-seamless-migration)を参照してください。

![ユーザー移行に対するシームレスな移行アプローチのフローチャート図](./media/user-migration/diagram-01-seamless-migration.png)<br />*図:シームレスな移行フロー*

## <a name="best-practices"></a>ベスト プラクティス

### <a name="security"></a>セキュリティ

シームレスな移行方法では、独自のカスタム REST API を使用して、レガシ ID プロバイダーに対してユーザーの資格情報を検証します。

**REST API をブルート フォース攻撃から保護する必要があります。** 攻撃者は、ユーザーの資格情報を最終的に当てるために、複数のパスワードを送信することがあります。 このような攻撃を防ぐには、サインイン試行回数が特定のしきい値に達したときに、REST API への要求の送信を停止します。 また、Azure AD B2C と REST API 間の通信もセキュリティで保護します。 お使いの RESTful API を実稼働用にセキュリティで保護する方法については、[RESTful API のセキュリティ保護](secure-rest-api.md)に関するページを参照してください。

### <a name="user-attributes"></a>ユーザー属性

レガシ ID プロバイダーのすべての情報を Azure AD B2C ディレクトリに移行する必要はありません。 移行する前に、Azure AD B2C に格納する適切なユーザー属性のセットを特定します。

- Azure AD B2C に格納 **するもの**
  - ユーザー名、パスワード、電子メール アドレス、電話番号、メンバーシップ番号/識別子。
  - プライバシー ポリシーと使用許諾契約書の同意マーカー。
- Azure AD B2C に格納 **しないもの**
  - クレジット カード番号、社会保障番号 (SSN)、医療記録、あるいは政府機関または業界のコンプライアンス対応団体によって規制されているその他のデータなどの機密データ。
  - マーケティングまたはコミュニケーション設定、ユーザーの行動、および分析情報。

### <a name="directory-clean-up"></a>ディレクトリのクリーンアップ

移行プロセスを開始する前に、この機会を利用してディレクトリをクリーンアップしてください。

- Azure AD B2C に格納されるユーザー属性のセットを特定し、必要なものだけを移行します。 必要に応じて、ユーザーに関するより多くのデータを格納するための[カスタム属性](user-flow-custom-attributes.md)を作成できます。
- 複数の認証ソースを持つ環境 (たとえば、各アプリケーションが独自のユーザー ディレクトリを持つ) から移行する場合は、Azure AD B2C の 1 つの統合アカウントに移行します。
- 複数のアプリケーションのユーザー名が異なる場合は、ID コレクションを使用して、これらすべてを Azure AD B2C のユーザー アカウントに格納できます。 パスワードについては、ユーザーにパスワードを選択させてディレクトリに設定させます。 たとえば、シームレスな移行では、選択したパスワードだけを Azure AD B2C アカウントに格納する必要があります。
- 使用されていないユーザー アカウントを移行前に削除するか、古いアカウントを移行しないでください。

### <a name="password-policy"></a>パスワード ポリシー

移行するアカウントのパスワード強度が Azure AD B2C によって適用される[強力なパスワード強度](../active-directory/authentication/concept-sspr-policy.md)より弱い場合は、強力なパスワードという要件を無効にすることができます。 詳細については、「[パスワード ポリシー プロパティ](user-profile-attributes.md#password-policy-attribute)」をご参照ください。

## <a name="next-steps"></a>次のステップ

GitHub の [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) リポジトリには、シームレスな移行のカスタム ポリシーの例と REST API コード サンプルが含まれています。

[シームレスなユーザー移行のカスタム ポリシーおよび REST API コード サンプル](https://aka.ms/b2c-account-seamless-migration)
