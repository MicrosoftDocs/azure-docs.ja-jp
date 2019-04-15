---
title: Azure Stack SQL リソース プロバイダー 1.1.30.0 のリリース ノート | Microsoft Docs
description: 既知の問題やダウンロード場所など、最新の Azure Stack SQL リソース プロバイダー更新プログラムについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: ae5a824fbd96a9a76eb18811a46bfc17afa15073
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879354"
---
# <a name="sql-resource-provider-11330-release-notes"></a>SQL リソース プロバイダー 1.1.33.0 のリリース ノート

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

これらのリリース ノートでは、SQL リソース プロバイダー バージョン 1.1.33.0 の機能強化と既知の問題について説明します。

## <a name="build-reference"></a>ビルドのリファレンス
SQL リソース プロバイダー バイナリをダウンロードした後、自己展開ツールを実行してコンテンツを一時ディレクトリに抽出します。 リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 SQL リソース プロバイダーのこのバージョンをインストールするために必要な Azure Stack の最小リリース バージョンを次に示します。

> |最小の Azure Stack バージョン|SQL リソース プロバイダーのバージョン|
> |-----|-----|
> |バージョン 1808 (1.1808.0.97)|[SQL RP バージョン 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> SQL リソース プロバイダーの最新バージョンをデプロイする前に、サポートされている最低限の Azure Stack 更新プログラムを Azure Stack 統合システムに適用するか、最新の Azure Stack Development Kit (ASDK) をデプロイします。

## <a name="new-features-and-fixes"></a>新機能と修正
このバージョンの Azure Stack SQL リソース プロバイダーには、次の機能強化と修正プログラムが含まれています。

### <a name="fixes"></a>修正
- **SQL リソース プロバイダーのポータル拡張機能が誤ったサブスクリプションを選択する可能性があります**。 SQL リソース プロバイダーは Azure Resource Manager の呼び出しを使用して、使用する最初のサービス管理者のサブスクリプションを判断しますが、これが*既定のプロバイダー サブスクリプション*でない場合があります。 その場合、SQL リソース プロバイダーは正常に機能しません。 

- **SQL ホスティング サーバーがホストされたデータベースを一覧表示しません。** SQL ホスティング サーバーのテナント リソースを表示するとき、ユーザーが作成したデータベースが表示されない場合があります。

- **TLS 1.2 が有効になっていない場合に、以前の SQL リソース プロバイダー (1.1.30.0) のデプロイが失敗することがあります**。 リソース プロバイダーのデプロイ、リソース プロバイダーの更新、またはシークレットのローテーションを行うときに TLS 1.2 を有効にするために、SQL リソース プロバイダー 1.1.33.0 を更新しました。 

- **SQL リソース プロバイダーのシークレットのローテーションに失敗します**。 シークレットをローテーションするときに次のエラー コードが出る問題を修正しました。
`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>既知の問題 

- **SQL SKU はポータルに表示されるまで最大 1 時間かかることがあります**。 新しい SQL データベースを作成するときに、新規に作成される SKU が表示されて使用できるようになるまで、最大 1 時間かかることがあります。 

    **対処法**: なし。

- **再利用された SQL ログイン**。 同じサブスクリプションの既存のログインと同じユーザー名で新しい SQL ログインを作成しようとすると、同じログインと既存のパスワードが再利用されます。 

    **対処法**: 同じサブスクリプションに新しいログインを作成するときに別のユーザー名を使用するか、同じユーザー名のログインを異なるサブスクリプションに作成します。

- **データの不整合の原因となる共有 SQL ログイン**。 SQL ログインが同じサブスクリプション下で複数の SQL データベースに対して共有されている場合、ログイン パスワードを変更するとデータの不整合が発生します。

    **対処法**: 同じサブスクリプション下の異なるデータベースには常に異なるログインを使用します。

- **SQL リソース プロバイダーが SQL Server Always On リスナーの追加に失敗します**。 SQL リソース プロバイダー VM は、SQL Server Always On リスナーのリスナー IP アドレスを使用するとき、リスナーのホスト名を解決できません。

    **対処法**: リスナーのホスト名に対するリスナー IP を解決するには、DNS が正しく動作していることを確認します。

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack を運用するクラウド管理者に関する既知の問題
[Azure Stack リリース ノート](azure-stack-servicing-policy.md)内のドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順
[SQL リソース プロバイダーの詳細を確認します](azure-stack-sql-resource-provider.md)。

[SQL リソースプロバイダーのデプロイを準備します](azure-stack-sql-resource-provider-deploy.md#prerequisites)。

[SQL リソース プロバイダーを以前のバージョンからアップグレードします](azure-stack-sql-resource-provider-update.md)。 