---
title: アマゾン ウェブ サービスで認証を構成する
description: この記事では、AWS リソースを管理する Azure Automation の Runbook 用に AWS 資格情報を作成し検証する方法について説明します。
keywords: AWS の認証, AWS の構成
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9bca59efc374fedf04600ae165f04215016dd4af
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850943"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>アマゾン ウェブ サービスによる Runbook の認証

アマゾン ウェブ サービス (AWS) のリソースに関する一般的なタスクを自動化する処理は、Azure の Automation Runbook で実現することができます。 Automation Runbook を使用すれば、Azure のリソースの場合と同様に、AWS でも多くのタスクを自動化することができます。 必要となるのは次の 2 点だけです。

* AWS サブスクリプションと一連の資格情報。 具体的には、AWS アクセス キーと秘密キーです。 詳細については、記事「[Using AWS Credentials (AWS 資格情報の使用)](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)」を参照してください。
* Azure サブスクリプションと Automation アカウント。

AWS で認証するには、一連の AWS 資格情報を指定して、Azure Automation から実行される Runbook を認証する必要があります。 Automation アカウントが作成済みで、それを AWS による認証に使用する場合は、次のセクションの手順に従うことができます。AWS リソースをターゲットとする Runbook 専用のアカウントにする場合は、最初に新しい [Automation アカウント](automation-offering-get-started.md)を作成し (サービス プリンシパルを作成するオプションはスキップします)、以下の手順を使用します。

## <a name="configure-automation-account"></a>Automation アカウントの構成

Azure Automation が AWS と通信する場合は、まず、AWS 資格情報を取得し、それを資産として Azure Automation に格納する必要があります。 AWS ドキュメント「[Managing Access Keys for your AWS Account (AWS アカウントのアクセス キーを管理する)](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)」に記載されている以下の手順を実行して、アクセス キーを作成し、**アクセス キー ID** と**シークレット アクセス キー**をコピーします (必要に応じて、キー ファイルをダウンロードし、どこか安全な場所に保存します)。

AWS セキュリティ キーを作成し、コピーしたら、Azure Automation アカウントで資格情報資産を作成し、それらを安全に格納し、Runbook で参照する必要があります。 「[Azure Automation での資格情報資産](shared-resources/credentials.md#to-create-a-new-credential-asset-with-the-azure-portal)」記事の**新しい資格情報資産の作成**に関するセクションの手順に従って、次の情報を入力します。

1. **[名前]** ボックスに「**AWScred**」と入力するか、または、命名規則に従って適切な値を入力します。
2. **[ユーザー名]** ボックスに**アクセス ID** を入力し、 **[パスワード]** および **[パスワードの確認]** ボックスに**シークレット アクセス キー**を入力します。

## <a name="next-steps"></a>次の手順

* AWS のタスクを自動化する Runbook を作成する方法については、[Amazon Web Services の VM デプロイの自動化](automation-scenario-aws-deployment.md)に関する記事をご覧ください。
