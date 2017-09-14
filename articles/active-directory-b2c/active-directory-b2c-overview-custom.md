---
title: "Azure Active Directory B2C: カスタム ポリシー | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーに関するトピック"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 33f62a33ea7a3fadb6e7b045de10df25f5edbe83
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシー

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>カスタム ポリシーとは

カスタム ポリシーは、Azure AD B2C テナントの動作を定義する構成ファイルです。 **組み込みのポリシー**が、最も一般的な ID タスク用に Azure AD B2C ポータルで事前に定義されているのに対して、カスタム ポリシーは、ほぼ無制限の数のタスクを完了するために ID 開発者が完全に編集できます。 カスタム ポリシーがユーザーおよびユーザーの ID シナリオに適しているかどうかを判定するには、この先をお読みください。

**カスタム ポリシーの編集は、すべてのユーザーのためのものではありません。** 学習曲線は厳しく、立上がり時間も長く、さらに将来カスタム ポリシーが変更された場合は保守するために同様の専門知識が必要になります。 カスタム ポリシーを使用する前に、まずユーザーのシナリオ用に組み込みのポリシーを慎重に検討する必要があります。

## <a name="comparing-built-in-policies-and-custom-policies"></a>組み込みのポリシーとカスタム ポリシーの比較

| | 組み込みのポリシー | カスタム ポリシー |
|-|-------------------|-----------------|
|対象ユーザー | ID の専門知識を持つ、または持たないすべてのアプリ開発者 | ID の利点: システム インテグレータ、コンサルタント、および社内の ID チーム。 OpenIDConnect フローに慣れており、ID プロバイダーや要求ベースの認証を理解しています |
|構成方法 | わかりやすい UI を備えた Azure ポータル | XML ファイルを直接編集した後、Azure ポータルにアップロード |
|UI のカスタマイズ | HTML、CSS、および JScript のサポートを含む、完全な UI のカスタマイズ (カスタム ドメインが必要)<br><br>カスタム文字列による多言語サポート | 同じ |
| 属性のカスタマイズ | 標準属性とカスタム属性 | 同じ |
|トークンおよびセッション管理 | カスタム トークンおよび複数のセッション オプション | 同じ |
|[ID プロバイダー]| **現在**: 定義済みのローカルのソーシャル プロバイダ<br><br>**将来**: 標準ベースの OIDC、SAML、OAuth | **現在**: 標準ベースの OIDC、OAUTH、SAML<br><br>**将来**: WsFed |
|ID タスク (例) | ローカルおよび多くのソーシャル アカウントによるサインアップまたはサインイン<br><br>セルフサービスのパスワード リセット<br><br>プロファイルの編集<br><br>多要素認証のシナリオ<br><br>トークンとセッションのカスタマイズ<br><br>アクセス トークンのフロー | カスタム ID プロバイダーまたはカスタム スコープを使用して、組み込みのポリシーと同じタスクを完了<br><br>登録時に別のシステムでのユーザーのプロビジョニング<br><br>独自の電子メール サービス プロバイダを使用して、ウェルカム メールを送信<br><br>B2C の外部のユーザー ストアを使用<br><br>ユーザーが指定した情報を API 経由の信頼できるシステムで検証 |

## <a name="policy-files"></a>ポリシー ファイル

カスタム ポリシーは、階層型チェーンで互いを参照する 1 つまたは複数の XML 形式ファイルとして表されます。 XML 要素は、要求スキーマ、要求変換、コンテンツ定義、要求プロバイダー/技術プロファイル、Userjourney オーケストレーションの手順などの要素を定義します。

次の 3 種類のポリシー ファイルを使用することをお勧めします。

- **BASE ファイル**。ほとんどの定義が含まれており、Azure はこの完全なサンプルを提供しています。  ポリシーのトラブルシューティングおよび長期間の保守に役立つように、このファイルへの変更は最小限に抑えることをお勧めします
- **EXTensions ファイル**。テナントの固有の構成変更を保持しています
- **証明書利用者 (RP) ファイル**。これは、アプリケーションまたはサービス (証明書利用者) から直接呼び出される、単一タスクに焦点を置いたファイルです。  詳細については、ポリシー ファイルの定義に関する記事を参照してください。  固有の各タスクには独自の RP が必要であり、ブランドの要件に応じて、この数は "アプリケーションの合計 x ユースケースの総数" になることがあります。


Azure AD B2C での組み込みのポリシーは上に示した 3 つのファイルのパターンに従いますが、ポータルが EXTenstions ファイルへの変更をバックグラウンドで行なっている間、開発者には証明書利用者 (RP) ファイルしか表示されません。

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>カスタム ポリシーを使用するときに知っておくべき主要な概念

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure の顧客 ID およびアクセス管理 (CIAM) サービス。 このサービスには、次のものが含まれます。

1. Microsoft Graph 経由でアクセス可能な専用の Azure Active Directory の形式のユーザー ディレクトリ。ここには、ローカル アカウントとフェデレーション アカウントの両方のユーザー データが保持されます 
2. ID/アクセス管理タスクを完了するためにユーザーおよびエンティティ間の信頼を調整し、それらの間で要求を渡す **Identity Experience Framework** へのアクセス 
3. ID トークン、更新トークン、アクセス トークン (および同等の SAML アサーション) を発行し、リソースを保護するためにそれらを検証するセキュリティ トークン サービス (STS)。

Azure AD B2C は ID プロバイダー、ユーザー、他のシステム、およびローカルのユーザー ディレクトリと順番に対話して、ID タスク (ユーザーのログイン、新しいユーザーの登録、パスワードのリセットなど) を実行します。 マルチパーティの信頼を確立し、これらの手順を実行する基礎となるプラットフォームは Identity Experience Framework と呼ばれ、ポリシー (ユーザー体験または信頼フレームワーク ポリシーとも呼ばれる) によってアクター、アクション、プロトコル、および完了するための一連の手順が明示的に定義されます。

### <a name="identity-experience-framework"></a>Identity Experience Framework

OpenIDConnect、OAuth、SAML、WSFed などの標準のプロトコル形式、およびいくつかの標準以外の形式 (REST API ベースのシステムからシステムへの要求の交換など) のエンティティ (広く要求プロバイダー) 間の信頼を調整する、完全に構成可能で、ポリシー主導かつクラウド ベースの Azure プラットフォーム。 I2E は、HTML、CSS、および JScript をサポートする、わかりやすい、ホワイトラベルが付けられたエクスペリエンスを作成します。  今日、Identity Experience Framework は Azure AD B2C サービスのコンテキストでのみ使用でき、CIAM 関連のタスクに対して優先的に使用されます。

### <a name="built-in-policies"></a>組み込みのポリシー

最も一般的に使用される ID タスク (ユーザーの登録、サインイン、パスワードのリセットなど) を実行するために Azure AD B2C の動作を指示し、 その関係も Azure AD B2C で事前に定義されている信頼できるパーティー (Facebook ID プロバイダー、LinkedIn、Microsoft アカウント、Google アカウントなど) と対話する定義済みの構成ファイル。  将来、組み込みのポリシーは、Azure Active Directory Premium、Active Directory/ADFS、Salesforce ID プロバイダなどの、通常はエンタープライズ領域にある ID プロバイダーのカスタマイズも提供する可能性があります。


### <a name="custom-policies"></a>カスタム ポリシー

Azure AD B2C テナント内の Identity Experience Framework の動作を定義する構成ファイル。 カスタム ポリシーは、証明書利用者 (アプリケーションなど) から呼び出されたときに Identity Experience Framework によって実行される 1 つまたは複数の XML ファイル (ポリシー ファイルの定義を参照) としてアクセス可能です。 カスタム ポリシーは、ほぼ無制限の数のタスクを完了するために ID 開発者が直接編集できます。 カスタム ポリシーを構成する開発者は、信頼できる関係の詳細をメタデータ エンドポイント、正確な要求交換定義を含むように慎重に定義し、各 ID プロバイダーの必要に応じてシークレット、キー、および証明書を構成する必要があります。

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Identity Experience Framework 信頼フレームワークのためのポリシー ファイルの定義

### <a name="policy-files"></a>ポリシー ファイル

カスタム ポリシーは、階層型チェーンで互いを参照する 1 つまたは複数の XML 形式ファイルとして表されます。 XML 要素は、要求スキーマ、要求変換、コンテンツ定義、要求プロバイダー/技術プロファイル、Userjourney オーケストレーションの手順などの要素を定義します。  次の 3 種類のポリシー ファイルを使用することをお勧めします。

- **BASE ファイル**。ほとんどの定義が含まれており、Azure はこの完全なサンプルを提供しています。  ポリシーのトラブルシューティングおよび長期間の保守に役立つように、このファイルへの変更は最小限に抑えることをお勧めします
- **EXTensions ファイル**。テナントの固有の構成変更を保持しています
- **証明書利用者 (RP) ファイル**。これは、アプリケーションまたはサービス (証明書利用者) から直接呼び出される、単一タスクに焦点を置いたファイルです。  詳細については、ポリシー ファイルの定義に関する記事を参照してください。  固有の各タスクには独自の RP が必要であり、ブランドの要件に応じて、この数は "アプリケーションの合計 x ユースケースの総数" になることがあります。

![ポリシー ファイルの種類](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| ポリシー ファイルの種類 | ファイル名の例 | 推奨される使用法 | 継承元 |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | マイクロソフトによって構成された主要な要求スキーマ、要求変換、要求プロバイダー、およびユーザー ジャーニーが含まれています<br><br>このファイルへの変更は最小限にしてください | なし |
| Extension (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | BASE ファイルへの変更をここに統合します<br><br>変更された要求プロバイダー<br><br>変更されたユーザー ジャーニー<br><br>独自のカスタム スキーマ定義 | BASE ファイル |
| 証明書利用者 (RP) | B2C_1A_sign_up_sign_in.xml| トークンの形状およびセッション設定はここで変更します| Extensions(EXT) ファイル |

### <a name="inheritance-model"></a>継承モデル

アプリケーションが RP ポリシー ファイルを呼び出すと、B2C の Identity Experience Framework はまず BASE から、次に EXTENSIONS から、最後に RP ポリシー ファイルからすべての要素を追加して、有効な現在のポリシーを組み立てます。  要素の種類と名前が同じ場合は RP ファイルの要素によって EXTENSIONS の内容が上書きされ、EXTENSIONS によって BASE が上書きされます。

Azure AD B2C での**組み込みのポリシー**は上に示した 3 つのファイルのパターンに従いますが、ポータルが EXTenstions ファイルへの変更をバックグラウンドで行なっている間、開発者には証明書利用者 (RP) ファイルしか表示されません。  Azure B2C チームの制御下にあり、頻繁に更新される BASE ポリシー ファイルをすべての Azure AD B2C が共有します。

