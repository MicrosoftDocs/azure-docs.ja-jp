---
title: Azure Active Directory B2C のカスタム ポリシー | Microsoft Docs
description: Azure Active Directory B2C のカスタム ポリシーについて説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 043cf83d804803e1b5b47d3ac51bbccaa06e4e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87116418"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ポリシー

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

カスタム ポリシーは、Azure Active Directory B2C (Azure AD B2C) テナントの動作を定義する構成ファイルです。 Azure AD B2C ポータルでは、最も一般的な ID タスク用のユーザー フローが事前に定義されています。 カスタム ポリシーは、ID 開発者がさまざまなタスクを実現する目的で自由に編集することができます。

## <a name="comparing-user-flows-and-custom-policies"></a>ユーザー フローとカスタム ポリシーの比較

| Context | ユーザー フロー | カスタム ポリシー |
|-|-------------------|-----------------|
| 対象ユーザー | ID の専門知識を持つ、または持たないすべてのアプリケーション開発者。 | ID のプロフェッショナル、システム インテグレータ、コンサルタント、社内の ID チーム。 彼らは OpenID Connect のフローに慣れており、ID プロバイダーや要求ベースの認証を理解しています。 |
| 構成方法 | ユーザー フレンドリなユーザー インターフェイス (UI) を備えた Azure portal。 | XML ファイルを直接編集して Azure portal にアップロードします。 |
| UI のカスタマイズ | HTML、CSS、および JavaScript を含む完全な UI のカスタマイズ。<br><br>カスタム文字列による多言語サポート。 | 同じ |
| 属性のカスタマイズ | 標準属性とカスタム属性。 | 同じ |
| トークンおよびセッション管理 | カスタム トークンおよび複数のセッション オプション。 | 同じ |
| [ID プロバイダー] | 定義済みのローカル プロバイダーまたはソーシャル プロバイダー、およびほとんどの OIDC ID プロバイダー (Azure Active Directory テナントとのフェデレーションなど)。 | 標準ベースの OIDC、OAUTH、SAML。  REST API との統合を使用して認証を行うことも可能です。 |
| ID タスク | ローカル アカウントまたはさまざまなソーシャル アカウントによるサインアップまたはサインイン。<br><br>セルフサービス パスワード リセット。<br><br>プロファイルの編集。<br><br>Multi-Factor Authentication。<br><br>トークンとセッションのカスタマイズ。<br><br>アクセス トークンのフロー。 | カスタム ID プロバイダーを使用したユーザー フローと同じタスクの実行、またはカスタム スコープの使用。<br><br>登録時に別のシステムでユーザー アカウントをプロビジョニング。<br><br>独自の電子メール サービス プロバイダを使用して、ウェルカム メールを送信。<br><br>Azure AD B2C の外部のユーザー ストアを使用。<br><br>ユーザーが指定した情報を信頼できるシステムで検証 (API を使用)。 |

## <a name="policy-files"></a>ポリシー ファイル

次の 3 種類のポリシー ファイルが使用されます。

- **Base ファイル** - ほとんどの定義が含まれています。 ポリシーのトラブルシューティングおよび長期間の保守に役立つように、このファイルへの変更は最小限に抑えることをお勧めします。
- **Extensions ファイル** - テナントの固有の構成変更を保持しています。
- **証明書利用者 (RP) ファイル** - アプリケーションまたはサービス (証明書利用者) から直接呼び出される、単一タスクに焦点を置いています。 固有の各タスクには独自の RP が必要であり、ブランドの要件に応じて、この数は "アプリケーションの合計 x ユースケースの総数" になることがあります。

Azure AD B2C でのユーザー フローは上に示したファイルのパターンに従いますが、Azure portal が Extensions ファイルへの変更をバックグラウンドで行なっている間、開発者には RP ファイルしか表示されません。

ポリシー ファイルには 3 つの種類がありますが、3 つのファイルのみに制限されることはありません。 ファイルの種類ごとに複数のファイルを含めることができます。 たとえば、Extensions ファイルを変更したくない場合は、Extensions2 ファイルを作成して、Extensions ファイルをさらに拡張することができます。

## <a name="custom-policy-core-concepts"></a>カスタム ポリシーの中心概念

Azure の顧客 ID およびアクセス管理 (CIAM) サービスには、次のようなものがあります。

- Microsoft Graph を使ってアクセス可能なユーザー ディレクトリ。ここには、ローカル アカウントとフェデレーション アカウントの両方のユーザー データが保持されます。
- ID/アクセス管理タスクを完了するためにユーザーおよびエンティティ間の信頼を調整し、それらの間で要求を渡す **Identity Experience Framework** へのアクセス。
- ID トークン、更新トークン、アクセス トークン (および同等の SAML アサーション) を発行し、リソースを保護するためにそれらを検証するセキュリティ トークン サービス (STS)。

Azure AD B2C は、ID プロバイダー、ユーザー、その他のシステムとやり取りし、ローカル ユーザー ディレクトリを順次操作して ID タスクを完了します。 たとえば、ユーザーのサインイン、新規ユーザーの登録、パスワードのリセットなどです。 Identity Experience Framework とポリシー (ユーザー体験または信頼フレームワーク ポリシーとも呼ばれる) によって、マルチパーティの信頼が確立され、アクター、アクション、プロトコル、および完了するための一連の手順が明示的に定義されます。

Identity Experience Framework は、OpenID Connect、OAuth、SAML などの標準のプロトコル形式、およびいくつかの標準以外の形式 (REST API ベースのシステム間の要求の交換など) のエンティティ間の信頼を調整する、完全に構成可能で、ポリシー主導かつクラウドベースの Azure プラットフォームです。 このフレームワークは、HTML と CSS をサポートする、わかりやすい、ホワイトラベルが付けられたエクスペリエンスを作成します。

カスタム ポリシーは、階層型チェーンで互いを参照する 1 つまたは複数の XML 形式ファイルとして表されます。 XML 要素は、要求スキーマ、要求変換、コンテンツ定義、クレーム プロバイダー、技術プロファイル、ユーザー体験のオーケストレーション手順などの要素を定義します。 カスタム ポリシーは、証明書利用者から呼び出されたときに Identity Experience Framework によって実行される 1 つまたは複数の XML ファイルとしてアクセス可能です。 カスタム ポリシーを構成する開発者は、信頼できる関係の詳細をメタデータ エンドポイント、正確な要求交換定義を含むように慎重に定義し、各 ID プロバイダーの必要に応じてシークレット、キー、および証明書を構成する必要があります。

### <a name="inheritance-model"></a>継承モデル

アプリケーションが RP ポリシー ファイルを呼び出すと、Azure AD B2C の Identity Experience Framework はまず base ファイルから、次に extensions ファイルから、最後に RP ポリシー ファイルからすべての要素を追加して、有効な現在のポリシーを組み立てます。  要素の種類と名前が同じ場合は RP ファイルの要素によって extensions の内容がオーバーライドされ、extensions によって base がオーバーライドされます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ポリシーの概要](custom-policy-get-started.md)
