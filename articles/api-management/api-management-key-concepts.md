---
title: Azure API Management の概要と主な概念 | Microsoft Docs
description: API、成果物、ロール、グループ、その他 API Management の重要概念について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 8d7fa8b8119ddf1769b36bcb55831047d6242470
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073437"
---
# <a name="about-api-management"></a>API Management について

API Management (APIM) は、既存のバックエンドのサービスに対して一貫性のある最新の API ゲートウェイを迅速に作成する手段です。

API Management が組織にもたらす利点は、外部のパートナーや社内の開発者に API を公開することによって、社内に眠っているデータやサービスの可能性を発掘できることです。 どの企業も、その業務をデジタル プラットフォームで拡大し、新しい販路と顧客を開拓すると共に、既存の顧客との絆を深めようと模索しています。 API Management は、開発者の取り組み、ビジネス インサイト、分析、セキュリティ、保護を通じて API プログラムの価値を高め、企業にコア コンピテンシーをもたらします。 Azure API Management を任意のバックエンドで実行し、それに基づいて本格的な API プログラムを起動できます。

この記事では、APIM に関連する一般的なシナリオの概要について説明します。  また、APIM システムの主要コンポーネントの概要についても説明します。 さらに、各コンポーネントの詳細な概要を示します。

## <a name="overview"></a>概要

API Management を使用するには、管理者が API を作成します。 API はそれぞれ、少なくとも 1 つの操作で構成され、1 つまたは複数の成果物に追加することができます。 API を使用する開発者は、その API を含んだ成果物をサブスクライブしたうえで、使用ポリシーが適用されていればその範囲の中で、API の操作を呼び出すことができます。 一般的なシナリオは、次のとおりです。

* **モバイル インフラストラクチャのセキュリティを強化する**。API キーによるアクセス制御、スロットル処理による DOS 攻撃の防止、JWT トークン検証などの高度なセキュリティ ポリシーの使用によって、これを行います。
* **ISV パートナー エコシステムを可能にする**。開発者ポータルを通したパートナーとの迅速な協力の提供と、パートナーによる使用の準備が整っていない内部実装から切り離された API ファサードの構築によって、これを行います。
* **内部 API プログラムを実行する**。組織が可用性と API に対する最新の変更を伝達するための一元的な場所の提供と、組織アカウントに基づいたアクセスの制限によって、これを行います。そのすべてが、API ゲートウェイとバックエンドの間のセキュリティ保護されたチャネルに基づいて実行されます。

システムは、次のコンポーネントで構成されます。

* **API ゲートウェイ**。これは次の機能を持つエンドポイントです。
  
  * API 呼び出しを受け入れ、バックエンドにルーティングします。
  * API キー、JWT トークン、証明書、その他の資格情報を検証します。
  * 使用量クォータとレート制限を適用します
  * コードを変更せずにその場で API を変換します。
  * セットアップ時にバックエンドの応答をキャッシュします。
  * 分析目的で呼び出しメタデータを記録します。
* **Azure Portal** は、API プログラムをセットアップする管理インターフェイスです。 これは次の目的で使用されます。
  
  * API スキーマを定義またはインポートします。
  * API を製品にパッケージします。
  * API のクォータや変換などのポリシーを設定します。
  * 分析から洞察を得ます。
  * ユーザーを管理します。
* **開発者ポータル**は、開発者用のメイン Web として機能します。ここで開発者は以下の作業を行うことができます。
  
  * API のドキュメントを閲覧します。
  * 対話型コンソールを使用して API を試します。
  * API キーを取得するために、アカウントを作成してサブスクライブします。
  * 自分自身の使用に関する分析にアクセスします。

詳しくは、「[Cloud-based API Management: Harnessing the Power of APIs](https://j.mp/ms-apim-whitepaper)」(クラウドベースの API 管理: API のパワーを利用する) ホワイトペーパー (PDF) をご覧ください。 CITO Research 社が作成した API Management に関するこの概要ホワイト ペーパーでは、次のトピックが取り上げられています。 
 
 * API の一般的な要件と課題
 * API の分離とファサードの提供
 * 開発者の準備時間の短縮
 * アクセスのセキュリティ保護
 * 分析とメトリック
 * API Management プラットフォームを使用した制御と把握
 * クラウド ソリューションとオンプレミス ソリューションの利用の違い
 * Azure API Management
 
## <a name="apis"> </a>API と操作
API Management サービス インスタンスの基礎となるのは API です。 それぞれの API は、開発者が利用できる一連の操作を表します。 各 API は、それを実装するバックエンド サービスへの参照を含んでおり、その操作は、バックエンド サービスに実装されている操作と対応します。 操作は、API Management で細かく設定することができます。URL のマッピング、クエリとパスのパラメーター、要求と応答の内容、操作から返される応答のキャッシュを制御することが可能です。 また、レート制限、クォータ、IP 制限のポリシーを API レベルや個々の操作レベルで導入することもできます。

詳細については、[API の作成方法][How to create APIs]に関するページと [API に操作を追加する方法][How to add operations to an API]に関するページを参照してください。

## <a name="products"> </a> 成果物
開発者から見える API の全体像が成果物です。 API Management の成果物には、少なくとも 1 つの API が含まれており、タイトルや説明、使用条件などが設定されます。 成果物の種類には、**オープンな**成果物と**保護された**成果物があります。 保護された成果物を使用するには、事前にサブスクライブする必要があります。一方、オープンな成果物は、サブスクライブせずに使用できます。 開発者に使用してもらう準備が整ったら、成果物を発行することができます。 発行された成果物は、開発者が見ることができます (保護された成果物の場合は、開発者がサブスクライブできるようになります)。 サブスクリプションの承認は成果物レベルで構成します。管理者による承認を必須とするか自動的に承認するかを選択できます。

開発者に成果物の表示を許可するかどうかは、グループを使用して管理します。 成果物の表示の可否はグループに対して付与されます。開発者は、自分が所属するグループから見える成果物を表示してサブスクライブすることができます。 

## <a name="groups"> </a> グループ
開発者に成果物の表示を許可するかどうかは、グループを使用して管理します。 API Management には、次に示すシステム グループが用意されています。これらのシステム グループを変更することはできません。

* **管理者** - Azure サブスクリプション管理者は、このグループのメンバーです。 管理者は、API Management サービス インスタンスを管理します。開発者が使用する API とその操作、成果物は、管理者が作成します。
* **開発者** - 認証された開発者ポータル ユーザーは、このグループに分類されます。 開発者は、API の利用者です。管理者によって作成された API を使用してアプリケーションを構築します。 開発者は、開発者ポータルへのアクセスが認められており、API の操作を呼び出すアプリケーションを構築します。
* **ゲスト** - API Management インスタンスの開発者ポータルに訪れる開発者ポータル ユーザーのうち、認証を受けていないもの (利用予定者など) がこのグループに該当します。 特定の読み取り専用アクセスを許可することができます (API の閲覧はできるが、呼び出すことはできないなど)。

管理者は、これらのシステム グループに加えてカスタム グループを作成できるほか、 [関連付けられている Azure Active Directory テナントの外部グループを活用する](api-management-howto-aad.md)こともできます。 カスタム グループと外部グループをシステム グループと共に使用することにより、開発者には API 成果物の可視性とアクセスが提供されます。 たとえば、特定のパートナー企業に所属する開発者向けにカスタム グループを 1 つ作成し、関連する API のみが含まれている成果物の API に対してアクセスを許可することができます。 ユーザーは 複数のグループのメンバーになることができます。

詳細については、[グループを作成して使用する方法][How to create and use groups]に関するページをご覧ください。

## <a name="developers"> </a> 開発者
開発者は、API Management サービス インスタンス内のユーザー アカウントです。 開発者は、管理者が作成したり参加を呼びかけたりすることができるほか、 [開発者ポータル][Developer portal]からサインアップすることもできます。 それぞれの開発者はグループ (複数可) に所属し、そのグループに閲覧が認められている成果物をサブスクライブすることができます。

成果物のサブスクリプションを持つ開発者には、その成果物へのプライマリ キーとセカンダリ キーが付与されます。 成果物の API を呼び出す際は、このキーを使用することになります。

詳細については、[開発者を作成または招待する方法][How to create or invite developers]に関するページと[グループを開発者に関連付ける方法][How to associate groups with developers]に関する記事を参照してください。

## <a name="policies"> </a> ポリシー
ポリシーは、Azure Portal がその構成を通じて API の動作を変更できる、API Management の強力な機能の 1 つです。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 代表的なステートメントとしては、XML 形式から JSON 形式への変換や、(開発者からの呼び出しの回数を制限する) 呼び出しレート制限が挙げられ、他にも数多くのポリシーが利用できます。

ポリシーの式は、ポリシーで特に指定されていない限り、任意の API Management ポリシーで属性値またはテキスト値として使用できます。 [制御フロー](/azure/api-management/api-management-advanced-policies#choose) ポリシーや[変数の設定](/azure/api-management/api-management-advanced-policies#set-variable)ポリシーなど、一部のポリシーはポリシーの式に基づいています。 詳細については、「[詳細なポリシー](/azure/api-management/api-management-advanced-policies#AdvancedPolicies)」と「[ポリシーの式](/azure/api-management/api-management-policy-expressions)」をご覧ください。


API Management の全ポリシー一覧については、「 [Policy reference (ポリシー リファレンス)][Policy reference]」をご覧ください。 ポリシーの使用と構成の詳細については、「 [API Management policies (API Management のポリシー)][API Management policies]」を参照してください。 レート制限ポリシーとクォータ ポリシーを持った成果物の作成に関するチュートリアルについては、「 [Azure API Management で成果物を作成して詳細設定を行う方法][How create and configure advanced product settings]」をご覧ください。


## <a name="developer-portal"> </a> 開発者ポータル
開発者ポータルは、開発者が API の使用法を習得したり、操作を確認して呼び出したり、成果物をサブスクライブしたりすることができる場です。 利用予定者は、開発者ポータルにアクセスして、API と操作を閲覧したうえで、サインアップすることができます。 開発者ポータルの URL は、API Management サービス インスタンスの Azure Portal のダッシュボードに示されます。

カスタム コンテンツを追加したり、スタイルをカスタマイズしたり、貴社のブランドを追加したりすることによって、開発者ポータルのルック アンド フィールをカスタマイズすることができます。

## <a name="api-management-and-the-api-economy"></a>API Management と API エコノミー

API Management の詳細については、Microsoft Ignite 2017 カンファレンスの次のプレゼンテーションを参照してください。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>次の手順

次のクイックスタートを完了して、Azure API Management の使用を開始します。

> [!div class="nextstepaction"]
> [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md




