---
title: Azure Active Directory B2C でのカスタム ポリシーのトラブルシューティング | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーを使って作業するときに発生するエラーを解決する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b33b76175558c71720c15a2a4e206e26a60f1f95
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880646"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Azure AD B2C のカスタム ポリシーと Identity Experience Framework のトラブルシューティング

Azure Active Directory B2C (Azure AD B2C) のカスタム ポリシーを使用する場合、そのポリシー言語の XML 形式で Identity Experience Framework を設定する課題が発生する可能性があります。  カスタム ポリシーの記述の学習は、新しい言語の学習に似ています。 この記事では、問題を迅速に発見して解決するうえで役立つツールとヒントについて説明します。 

> [!NOTE]
> この記事では、Azure AD B2C のカスタム ポリシーの構成のトラブルシューティングについて主に説明します。 証明書利用者アプリケーションまたはその ID ライブラリのトラブルシューティングは取り扱いません。

## <a name="xml-editing"></a>XML の編集

カスタム ポリシーの設定における最も一般的なエラーが、不適切な形式の XML です。 そのため、優れた XML エディターがほぼ不可欠です。 優れた XML エディターでは、XML のネイティブな表示、コード内容の色付け、一般的な用語の事前入力、XML 要素の持続的なインデックス作成、スキーマの検証が可能です。 次の 2 つの XML エディターをお勧めします。

* [Visual Studio Code](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

XML スキーマ検証では、XML ファイルをアップロードする前にエラーを識別します。 スターター パックのルート フォルダーで、XML スキーマ定義 TrustFrameworkPolicy_0.3.0.0.xsd を取得します。 詳細については、XML エディターのドキュメントで "*XML ツール*" と "*XML 検証*" を検索してください。

XML ルールのレビューが役に立つ可能性があります。 Azure AD B2C では、検出された XML 形式エラーはすべて拒否されます。 ときどき不適切な形式の XML が原因で、誤解を招くエラー メッセージが表示されることがあります。

## <a name="upload-policies-and-policy-validation"></a>アップロード ポリシーとポリシー検証

 XML ファイルのアップロードの検証は自動的に行われます。 ほとんどのエラーによって、アップロードが失敗します。 検証には、アップロードするポリシー ファイルが含まれます。 アップロード ファイルが参照する一連のファイル (証明書利用者のポリシー ファイル、拡張ファイル、およびベース ファイル) も含まれます。 
 
 一般的な検証エラーには、次のものがあります。

エラー スニペット: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* ClaimType 値のスペルが間違っているか、スキーマに存在しない可能性があります。
* ClaimType 値は、ポリシー内の少なくとも 1 つのファイルで定義されている必要があります。 
    次に例を示します。`<ClaimType Id="socialIdpUserId">`
* ClaimType が拡張ファイルで定義されているが、ベース ファイルの TechnicalProfile でも使用される場合、ベース ファイルのアップロードでエラーが発生します。

エラー スニペット: `...makes a reference to a ClaimsTransformation with id...`
* このエラーの原因は、ClaimType エラーと同じである可能性があります。

エラー スニペット: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* **\<TrustFrameworkPolicy\>** と **\<BasePolicy\>** の各要素の TenantId の値が、ターゲット Azure AD B2C テナントに一致していることを確認します。  

## <a name="troubleshoot-the-runtime"></a>ランタイムのトラブルシューティング

* `Run Now` と `https://jwt.io` を使用して、Web アプリケーションまたはモバイル アプリケーションとは別にポリシーをテストします。 この Web サイトは、証明書利用者アプリケーションと同様に動作します。 このサイトには、Azure AD B2C ポリシーによって生成される JSON Web トークン (JWT) の内容が表示されます。 Identity Experience Framework でテスト アプリケーションを作成するには、次の値を使用します。
    * 名前:TestApp
    * Web アプリ/Web API:いいえ 
    * ネイティブ クライアント:いいえ 

* クライアント ブラウザーと Azure AD B2C の間でのメッセージ交換をトレースするには、[Fiddler](https://www.telerik.com/fiddler) を使用します。 これにより、オーケストレーション手順のどこでユーザー体験が失敗するか、ヒントを得ることができます。

* **開発モード**で **Application Insights** を使用して、Identity Experience Framework のユーザー体験のアクティビティをトレースします。 **開発モード**では、技術プロファイルで定義された各種要求プロバイダーと Identity Experience Framework の間で行われる要求の交換を観察できます。この要求プロバイダーには、ID プロバイダー、API ベースのサービス、Azure AD B2C ユーザー ディレクトリのほか、Azure Multi-Factor Authentication などのサービスがあります。  

## <a name="recommended-practices"></a>推奨プラクティス

**複数のバージョンのシナリオを保持する。アプリケーションを使用してプロジェクトでそれらをグループ化する。** ベース ファイル、拡張ファイル、証明書利用者ファイルは互いに直接依存しています。 これらのファイルを 1 つのグループとして保存します。 新機能がポリシーに追加されたら、作業バージョンを別々に保持します。 これらのファイルがやり取りするアプリケーション コードを使用して、独自のファイル システムで作業バージョンをステージングします。  アプリケーションによって、テナント内で多様な証明書利用者ポリシーが呼び出される可能性があります。 これらが、Azure AD B2C ポリシーからの想定される要求に依存するようになることがあります。

**既知のユーザー体験を使用して技術プロファイルの開発とテストを行う。** テストが済んでいるスターター パックのポリシーを使用して技術プロファイルを設定します。 独自のユーザー体験に組み込む前に個別にテストします。

**テストが済んでいる技術プロファイルを使用してユーザー体験の開発とテストを行う。** ユーザー体験のオーケストレーション手順を少しずつ変更します。 目的のシナリオを段階的に構築します。

## <a name="next-steps"></a>次の手順

* GitHub から、[active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip ファイルをダウンロードします。
