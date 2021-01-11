---
title: Azure App Service のセキュリティ コントロール
description: 組織で Azure App Service を評価するためのセキュリティ コントロールのチェックリストを示します。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 93e5123e5b61c9013177f7f3c908578b68da52d6
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962929"
---
# <a name="security-controls-for-azure-app-service"></a>Azure App Service のセキュリティ コントロール

この記事では、Azure App Service に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント
|---|---|--|
| サービス エンドポイントのサポート| はい | App Service に使用できます。| [Azure App Service のアクセス制限](app-service-ip-restrictions.md)
| VNet インジェクションのサポート| はい | App Service Environment は、お客様の仮想ネットワークに挿入される 1 人のお客様専用の App Service のプライベート実装です。 | [App Service Environment の概要](environment/intro.md)
| ネットワークの分離とファイアウォールのサポート| はい | App Service のパブリック マルチテナント バリエーションでは、お客様は、許可されたインバウンド トラフィックをロック ダウンするようにネットワーク ACL (IP 制限) を構成することができます。  App Service Environment は仮想ネットワークに直接デプロイされるため、NSG で保護することができます。 | [Azure App Service のアクセス制限](app-service-ip-restrictions.md)
| 強制トンネリングのサポート| はい | App Service Environment は、強制トンネルが構成された、お客様の仮想ネットワークにデプロイすることができます。 | [強制トンネリングを使用した App Service Environment の構成](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | App Service は、Application Insights をサポートする言語用の Application Insights と統合されます (完全な .NET Framework、.NET Core、Java および Node.JS)。  「[Azure App Service のパフォーマンスの監視](../azure-monitor/app/azure-web-apps.md)」を参照してください。 App Service では、アプリケーション メトリックを Azure Monitor に送信することもできます。 | [Azure App Service のアプリの監視](web-sites-monitor.md)
| コントロールと管理プレーンのログ記録と監査| はい | App Service オブジェクトで実行されるすべての管理操作は、[Azure Resource Manager](../azure-resource-manager/index.yml) 経由で実行されます。 これらの操作の履歴ログは、ポータルと CLI の両方で利用できます。 | [Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)、[az monitor activity-log](/cli/azure/monitor/activity-log)
| データ プレーンのログ記録と監査 | いいえ | App Service のデータ プレーンは、ユーザーのデプロイされたWeb サイトのコンテンツを含む、リモート ファイル共有です。  リモート ファイル共有の監査はありません。 |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes |  ドキュメント
|---|---|--|
| 認証| はい | お客様は、[Azure Active Directory (Azure AD)](../active-directory/index.yml) および OAuth と互換性のある他の ID プロバイダーと自動的に統合される App Service でアプリケーションを構築できます。App Service 資産への管理アクセスでは、Azure AD で認証されたプリンシパルと Azure RBAC の組み合わせによって、すべてのアクセスが制御されます。 | [Azure App Service での認証および承認](overview-authentication-authorization.md)
| 承認| はい | App Service 資産への管理アクセスでは、Azure AD で認証されたプリンシパルと Azure RBAC の組み合わせによって、すべてのアクセスが制御されます。  | [Azure App Service での認証および承認](overview-authentication-authorization.md)

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | Web サイト ファイルのコンテンツは、Azure Storage に格納されます。これにより、コンテンツは保存時に自動的に暗号化されます。 <br><br>お客様が提供するシークレットは保存時に暗号化されます。 シークレットは、App Service 構成データベースに格納されている間、保存時に暗号化されます。<br><br>ローカルに接続されたディスクは、必要に応じて、Web サイトによって一時ストレージとして使用できます (D:\local および %TMP%)。 ローカルに接続されたディスクでは、保存時の暗号化は行われません。 | [保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい | お客様はアプリケーション シークレットを Key Vault に格納し、それらを実行時に取得することを選択できます。 | [App Service と Azure Functions の Key Vault 参照を使用する (プレビュー)](app-service-key-vault-references.md)
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | お客様は、インバウンド トラフィックで HTTPS を必要とし、使用するように Web サイトを構成することができます。  | [Azure App Service を HTTPS のみにする方法](/archive/blogs/benjaminperkins/how-to-make-an-azure-app-service-https-only) (ブログ記事)
| API 呼び出しの暗号化| はい | App Service を構成するための管理呼び出しは、HTTPS 経由の [Azure Resource Manager](../azure-resource-manager/index.yml) 呼び出しで行われます。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、App Service 構成の状態を Azure Resource Manager テンプレートとしてエクスポートし、経時的にバージョン管理できます。 ランタイム操作では、お客様は App Service デプロイ スロット機能を使用して、複数の異なるライブ バージョンのアプリケーションを維持できます。 | 

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。