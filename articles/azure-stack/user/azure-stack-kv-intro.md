---
title: "Azure Stack Key Vault の概要 | Microsoft Docs"
description: "Azure Stack Key Vault によるキーとシークレットの管理方法について説明します"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: sngun
ms.openlocfilehash: ecb542e967669fc4e4465ae59b3e9c37e4a5c332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Azure Stack での Key Vault の概要

## <a name="before-you-start"></a>開始する前に
この記事では、以下を前提としています。

* ユーザーは、Key Vault サービスを含むプランをサブスクライブする必要があります。  
* [Azure Stack で使うように PowerShell を構成します](azure-stack-powershell-configure-user.md)。 
 
## <a name="key-vault-basics"></a>Key Vault の基礎
Azure Stack の Key Vault は、クラウド アプリケーションやサービスで使われる暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Key Vault を使うと、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、.pfx ファイル、パスワードなど) を暗号化できます。

Key Vault は、キー管理プロセスを合理化し、データにアクセスして暗号化するキーの制御を維持できます。 開発者は、開発やテスト用のキーを数分で作成し、それらをシームレスに実稼働キーに移行できます。 セキュリティ管理者は、必要に応じて、キーに権限を付与する (取り消す) ことができます。

Azure Stack サブスクリプションを持つユーザーはだれでも、Key Vault を作成して使うことができます。 Key Vault は開発者とセキュリティ管理者にとってメリットがありますが、組織の他の Azure Stack サービスを管理するオペレーターが実装して管理できます。 たとえば、Azure Stack オペレーターは、Azure Stack のサブスクリプションを使ってサインインし、キーを保存する組織用のコンテナーを作成して、以下の運用タスクを担当することができます。

* キーやシークレットの作成やインポート
* キーやシークレットの取り消しや削除
* キーおよびシークレットを管理または使うことができるように、ユーザーまたはアプリケーションに対する Key Vault へのアクセスの許可
* キーの使用状況の構成 (署名や暗号化など)

オペレーターは、開発者にアプリケーションから呼び出す URI を提供し、セキュリティ管理者にキーの使用状況のログ情報を提供することができます。

開発者は、API を使用してキーを直接管理することもできます。 詳細については、Key Vault 開発者ガイドを参照してください。

## <a name="scenarios"></a>シナリオ
次の表では、Key Vault が開発者やセキュリティ管理者のニーズを満たすのに役立ついくつかのシナリオを示します。

### <a name="developer-for-an-azure-stack-application"></a>Azure Stack アプリケーションの開発者
**問題**: 署名と暗号化にキーを使う Azure Stack のアプリケーションを作成したいが、ソリューションが地理的に分散したアプリケーションに合うように、これらのキーをアプリケーションの外部に設定したい。

**説明**: キーはコンテナーに格納され、必要に応じて URI によって呼び出されます。

### <a name="developer-for-software-as-a-service-saas"></a>サービスとしてのソフトウェア (SaaS) の開発者
**問題**: 顧客のキーやシークレットに対して義務や潜在的責任を負いたくない。

**説明**: 顧客は Azure Stack に自分のキーをインポートして管理できます。 顧客がキーを自分で所有して管理すれば、オペレーターは自分の専門分野である、中心的なソフトウェア機能を提供することに集中できます。

### <a name="chief-security-officer-cso"></a>最高セキュリティ責任者 (CSO)
**問題**: 組織が、キーのライフ サイクルを管理し、キーの使用状況を確実に監視できるようにしたい。

**説明**: Key Vault は、マイクロソフトがキーを確認または抽出しないように作られています。  アプリケーションが顧客のキーを使って暗号化操作を実行する必要がある場合は、Key Vault がアプリケーションに代わってこれを行います。 アプリケーションには、顧客のキーは表示されません。  複数の Azure Stack サービスとリソースを使っていますが、Azure Stack の 1 つの場所からキーを管理したいと望んでいます。 コンテナーは、Azure Stack にあるコンテナーの数、サポートするリージョン、使うアプリケーションに関係なく、1 つのインターフェイスを提供します。

## <a name="next-steps"></a>次のステップ

* [ポータルを使って Azure Stack の Key Vault を管理する](azure-stack-kv-manage-portal.md)  
* [PowerShell を使って Azure Stack の Key Vault を管理する](azure-stack-kv-manage-powershell.md)
