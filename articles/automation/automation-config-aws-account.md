---
title: アマゾン ウェブ サービスで Azure Automation Runbook を認証する
description: この記事では、アマゾン ウェブ サービスで Runbook を認証する方法について説明します。
keywords: AWS の認証, AWS の構成
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83837189"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>アマゾン ウェブ サービスで Runbook を認証する

アマゾン ウェブ サービス (AWS) のリソースに関する一般的なタスクを自動化する処理は、Azure の Automation Runbook で実現することができます。 Automation Runbook を使用すれば、Azure のリソースの場合と同様に、AWS でも多くのタスクを自動化することができます。 認証するには、Azure サブスクリプションが必要です。

## <a name="obtain-aws-subscription-and-credentials"></a>AWS サブスクリプションと資格情報を取得する

AWS で認証するには、AWS サブスクリプションを取得し、一連の AWS 資格情報を指定して Azure Automation から実行される Runbook を認証する必要があります。 具体的には資格情報として AWS アクセス キーと秘密鍵が必要になります。 「[AWS 資格情報の使用](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)」を参照してください。

## <a name="configure-automation-account"></a>Automation アカウントの構成

AWS での認証に既存の Automation アカウントを使用できます。 あるいは、あるアカウントを、AWS リソースを対象とする Runbook 専用とすることができます。 その場合、新しい [Automation アカウント](automation-create-standalone-account.md)を作成します。  

## <a name="store-aws-credentials"></a>AWS 資格情報を格納する

Azure Automation には AWS 資格情報を資産として格納する必要があります。 アクセス キーと秘密鍵を作成する方法については、「[AWS アカウントのアクセス キーを管理する](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)」を参照してください。 キーが利用できるとき、アクセス キー ID と秘密鍵 ID を安全な場所にコピーしておきます。 キー ファイルをダウンロードし、どこか安全な場所に保管できます。

## <a name="create-credential-asset"></a>資格情報資産を作成する

AWS セキュリティ キーを作成し、コピーしたら、Automation アカウントで資格情報資産を作成する必要があります。 この資産により、AWS キーを Runbook に安全に保管し、参照できます。 「[Azure ポータルで新しい資格情報資産を作成する](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal)」を参照してください。 該当するフィールドに次の AWS 情報を入力します。
    
* **[名前]**  -  「**AWScred**」にするか、命名規則に従って適切な値を入力します。
* **[ユーザー名]** - アクセス ID
* **[パスワード]** - 秘密鍵の名前 

## <a name="next-steps"></a>次のステップ

* AWS でタスクを自動化する Runbook を作成する方法については、「[Runbook を使用してアマゾン ウェブ サービスの VM をデプロイする](automation-scenario-aws-deployment.md)」を参照してください。