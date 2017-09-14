---
title: "Azure AD のセルフサービスによるパスワードのリセットの概要 | Microsoft Docs"
description: "Azure AD のセルフサービスによるパスワードのリセットが組織に提供するものは何か。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: 9f72a4900f20282827ff939aa41bc4f306e547a3
ms.contentlocale: ja-jp
ms.lasthandoff: 05/20/2017

---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>IT プロフェッショナルにとっての Azure AD のセルフサービスによるパスワードのリセット

"セルフ サービス" とは、世界中の多くの IT 部門で、さまざまな意味でよく使われている業界用語です。 市場には、クラウドまたはオンプレミスから、オンプレミスのグループ、パスワード、またはユーザー プロファイルを管理できる製品が多数出回っています。

Azure Active Directory (Azure AD) のセルフサービスによるパスワードのリセット (SSPR) は、その使いやすさとデプロイの容易さによって、他の製品を大きく引き離しています。 Azure AD のセルフサービスによるパスワードのリセットでは、次の能力が結合されています。

* ユーザーが自分のパスワードを管理できます。
  * どのデバイスでも
  * どこからでも
  * いつでも
* 管理者が定義したポリシーに従ってコンプライアンスを維持します。

準備ができたら、[クイック スタート ガイダンス](active-directory-passwords-getting-started.md)から Azure AD SSPR の使用を開始して、すぐにユーザーが自分のパスワードをリセットできるように設定できます。

## <a name="what-is-possible"></a>何ができるか

* **セルフ サービスによるパスワードの変更**: エンド ユーザーまたは管理者は、管理者の支援なしで自分のパスワードを変更できます。
* **セルフ サービスによるアカウントのロック解除**: エンドユーザーは、管理者の支援なしで自分のアカウントのロックを解除できます。
* **セルフ サービスによるパスワードのリセット**: エンド ユーザーまたは管理者は、管理者の支援なしで自分のパスワードを自動的にリセットできます。 セルフ サービスによるパスワードのリセットを行うには、Azure AD Premium または Basic が必要です ([Azure Active Directory のエディション](active-directory-editions.md))。
* **管理者によるパスワードのリセット**: 管理者は、[Azure ポータル](https://docs.microsoft.com/azure/azure-portal-overview)からエンド ユーザーまたは別の管理者のパスワードをリセットできます。
* **パスワード管理アクティビティ レポート**: 組織内で発生したパスワードのリセットおよび登録アクティビティの詳細が管理者に提供されます ([管理レポート](active-directory-passwords-reporting.md))。
* **パスワード ライトバック**: クラウドからオンプレミスのパスワードを管理できるので、上記のすべてのシナリオを、フェデレーション ユーザーまたはパスワード同期済みユーザーが (またはこれらのユーザーに代わって) 実行できます。 パスワード ライトバックを行うには [Azure AD Premium](active-directory-get-started-premium.md) が必要です。

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Azure AD のセルフ サービスによるパスワードのリセットを選択する理由

* **コストの削減**: ヘルプデスクとサポートの支援によるパスワードのリセットは、通常、組織の IT 支出の 20% を占めています。
* **エンド ユーザーのエクスペリエンスの向上**と**ヘルプデスクの仕事量の削減**: 自分のパスワードに関する問題をすぐに解決できる能力をエンド ユーザーに与えることで、ヘルプデスクへの連絡やサポートの要求数が減少します。
* **モビリティの推進**: ユーザーはどこからでも自分のパスワードをリセットできます。

## <a name="azure-ad-self-service-password-reset-availability"></a>Azure AD のセルフ サービスによるパスワードのリセットの使用可能性

Azure AD のセルフサービスによるパスワードのリセットは、サブスクリプションに応じて次の 3 つのレベルで使用できます。

* **Azure AD Free** - クラウドのみの管理者が、自分のパスワードをリセットできます。
* **Azure AD Basic** または **Office 365 のすべての有料サブスクリプション** - クラウドのみのユーザーおよびクラウドのみの管理者が、自分のパスワードをリセットできます。
* **Azure AD Premium** - クラウドのみ、フェデレーション、パスワード同期ユーザーを含むすべてのユーザーまたは管理者が、自分のパスワードをリセットできます。 オンプレミスのパスワードでは、パスワードの書き戻しが有効になっている必要があります。

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>Azure AD のセルフサービスのパスワードのリセット、パーツの合計

Azure AD のセルフサービスのパスワードのリセットは、次のコンポーネントで構成されています。

* **パスワード管理構成ポータル**: Azure ポータルを使用してテナントのパスワードを管理する方法のオプションを制御できます。
* **パスワード リセット登録ポータル**: ユーザーはここでパスワードのリセットを自己登録できます。
* **パスワード リセット ポータル**: 管理者が定義したチャレンジとユーザーが入力した回答を使用して、ユーザーが自分のパスワードをリセットできます。
* **ユーザー パスワード変更ポータル** – ユーザーは、古いパスワードを入力し、新しいパスワードを指定することで、自分のパスワードを変更できます。
* **パスワード管理レポート**: 管理者は、テナントのパスワード アクティビティを Azure ポータルに表示して分析できます。
* **Azure AD Connect を使用したオンプレミスへのパスワードの書き戻し**: オンプレミス、フェデレーション、またはパスワード同期ユーザーをクラウドから管理できます。

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD の価格、SLA、更新、およびロードマップ

これらのトピックの詳細については、次のページを参照してください。

* [**Azure Active Directory の価格**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Office 365 の価格**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure サービス レベル アグリーメント**](https://azure.microsoft.com/support/legal/sla/)
* [**Microsoft Online Services サービス レベル アグリーメント**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure の更新**](https://azure.microsoft.com/updates/)
* [**Azure のロードマップ**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>次のステップ

次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [**クイック スタート**](active-directory-passwords-getting-started.md) - Azure AD のセルフサービスによるパスワードのリセットの管理を始めることができます。 
* [**ライセンス**](active-directory-passwords-licensing.md) - Azure AD のライセンスを構成します。
* [**データ**](active-directory-passwords-data.md) - パスワード管理に必要なデータとその使用方法がわかります
* [**展開**](active-directory-passwords-best-practices.md) - ここで見つかるガイダンスを使用してユーザーに対する SSPR を計画してデプロイできます
* [**カスタマイズ**](active-directory-passwords-customize.md) - 会社の SSPR エクスペリエンスの外観をカスタマイズします。
* [**レポート**](active-directory-passwords-reporting.md) - ユーザーが SSPR 機能にアクセスしたかどうかや、アクセスしたタイミングと場所を検出します
* [**技術的詳細**](active-directory-passwords-how-it-works.md) - しくみを詳しく説明しています
* [**よく寄せられる質問**](active-directory-passwords-faq.md) - どのようにですか? なぜですか? 何ですか? どこですか? 誰がですか? いつですか? - ずっと確認したかった質問に対する回答
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - SSPR の一般的な問題を解決する方法について説明しています


