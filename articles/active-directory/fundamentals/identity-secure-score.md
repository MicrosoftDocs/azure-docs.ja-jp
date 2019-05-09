---
title: ID セキュリティ スコアとは  - Azure Active Directory (パブリック プレビュー) | Microsoft Docs
description: ID セキュリティ スコアを使用して Azure AD テナントのセキュリティ体制を強化する方法に関する手順です。
services: active-directory
keywords: ID セキュリティ スコア、Azure AD、会社のリソースへのアクセスのセキュリティ保護
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8617442c811dc2fe8f961038f35ebcf218a58302
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163957"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory-public-preview"></a>Azure Active Directory の ID セキュリティ スコアとは (パブリック プレビュー)

Azure AD テナントはどの程度セキュリティ保護されていますか。 この質問にどう答えていいかわからない場合は、この記事を読んで、ID セキュリティ体制を監視したり、強化したりするために ID セキュリティ スコアがどのように役立つかを学習してください。 

## <a name="what-is-an-identity-secure-score"></a>ID セキュリティ スコアとは

ID セキュリティ スコアは、セキュリティに関する Microsoft のベスト プラクティスの推奨事項にどれだけ適合しているかを示す指標として機能する 1 から 248 までの数値です。


![セキュリティ スコア](./media/identity-secure-score/01.png)



このスコアは、次のために役立ちます。

- ID セキュリティ体制を客観的に測定する

- ID セキュリティの強化を計画する

- 強化の成功を確認する 


このスコアや関連情報には、ID セキュリティ スコア ダッシュボードでアクセスできます。 このダッシュボードには、次の内容が表示されます。

- スコア

    ![セキュリティ スコア](./media/identity-secure-score/02.png)

- 比較グラフ

    ![セキュリティ スコア](./media/identity-secure-score/03.png)

- 傾向グラフ

    ![セキュリティ スコア](./media/identity-secure-score/04.png)

- ID セキュリティに関するベスト プラクティスの一覧。 

    ![セキュリティ スコア](./media/identity-secure-score/05.png)


強化アクションに従うことによって、次のことが可能になります。

- セキュリティ体制を強化し、スコアを改善する。
 
- Microsoft の ID 機能を利用する。 



## <a name="how-do-i-get-my-secure-score"></a>セキュリティ スコアを取得する方法

ID セキュリティ スコアは、Azure AD のすべてのエディションで使用できます。

このスコアにアクセスするには、[Azure AD の概要ダッシュボード](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore)に移動してください。



## <a name="how-does-it-work"></a>それはどのように機能しますか?

48 時間ごとに、Azure はセキュリティ構成を調べ、その設定を推奨されるベスト プラクティスと比較します。 この評価の結果に基づいて、テナントの新しいスコアが計算されます。 つまり、実施した構成変更がスコアに反映されるまでに最大 48 時間かかる場合があります。 

各推奨事項は、Azure AD の構成に基づいて測定されます。 ベスト プラクティスの推奨事項を有効にするためにサードパーティ製品を使用している場合は、強化アクションの設定でこれを示すことができます。

![セキュリティ スコア](./media/identity-secure-score/07.png)


さらに、推奨事項が実際の環境に適用されない場合は、それらの推奨事項が無視されるように設定するオプションも使用できます。 無視された推奨事項は、スコアの計算には使用されません。 
 
![セキュリティ スコア](./media/identity-secure-score/06.png)



## <a name="how-does-it-help-me"></a>それはどのように役立つか

セキュリティ スコアは、次のために役立ちます。

- ID セキュリティ体制を客観的に測定する

- ID セキュリティの強化を計画する

- 強化の成功を確認する



## <a name="what-you-should-know"></a>知っておくべきこと

### <a name="who-can-use-the-identity-secure-score"></a>ID セキュリティ スコアは誰が使用できますか?

ID セキュリティ スコアは、次のロールが使用できます。

- 全体管理者
- セキュリティ管理者 
- セキュリティ閲覧者 

### <a name="what-does-not-scored-mean"></a>[スコア付けなし] とはどういう意味ですか?

[スコア付けなし] というラベルが付いたアクションは、組織で実行できるが、ツールに (まだ) 組み込まれていないためにスコア付けされないアクションです。 そのため、引き続きセキュリティを強化できますが、現時点でこれらのアクションの実行には意味がありません。

### <a name="how-often-is-my-score-updated"></a>このスコアはどの程度の頻度で更新されますか?

このスコアは、1 日に 1 回 (太平洋標準時午前 1:00 頃) 計算されます。 測定されたアクションに変更を加えた場合、このスコアは翌日に自動的に更新されます。 変更がスコアに反映されるまでに、最大 48 時間かかります。


### <a name="my-score-changed-how-do-i-figure-out-why"></a>スコアが変更されました。 その理由を見つけるにはどうしたらよいですか?

[セキュリティ スコア ポータル](https://securescore.microsoft.com/#!/score)のスコア アナライザー ページで、特定の日のデータ ポイントをクリックします。次に、下へスクロールし、その日の完了したアクションと未完了のアクションを表示して何が変更されたかを見つけます。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>セキュリティ スコアは侵害されるリスクを測定しますか?

手短に言えば、しません。 セキュリティ スコアは、侵害される可能性の絶対的な尺度を表していません。 これは、侵害されるリスクを相殺できる機能をどの程度採用しているかを表しています。 侵害されないようにすることを保証できるサービスは存在せず、セキュリティ スコアをどのような保証としても解釈すべきではありません。

### <a name="how-should-i-interpret-my-score"></a>このスコアをどのように解釈したらよいですか?

ユーザーには、推奨されるセキュリティ機能を構成したり、セキュリティ関連のタスク (レポートの参照など) を実行したりするためのポイントが提供されます。 一部のアクションは、部分的な完了としてスコア付けされます (ユーザーのための多要素認証 (MFA) の有効化など)。 セキュリティ スコアは、使用している Microsoft セキュリティ サービスを直接表しています。 セキュリティは常に、使いやすさとのバランスを取る必要があることに注意してください。 すべてのセキュリティ コントロールには、ユーザーに影響するコンポーネントがあります。 ユーザーへの影響が少ないコントロールは、ユーザーの日常操作にほとんど、またはまったく影響しません。

スコアの履歴を表示するには、[セキュリティ スコア ポータル](https://securescore.microsoft.com/#!/score)のスコア アナライザー ページに移動してください。 特定の日付を選択すると、その日どのコントロールが有効になっていて、コントロールごとにどれだけのポイントを得たかを確認できます。


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>ID セキュリティ スコアは Office 365 のセキュリティ スコアにどのように関連していますか? 

[Office 365 のセキュリティ スコア](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score)は、次の 5 つの異なるスコアの集計に移行されるところです。

- ID

- データ

- デバイス

- インフラストラクチャ

- アプリケーション

ID セキュリティ スコアは、Office 365 のセキュリティ スコアの ID 部分を表しています。 つまり、ID セキュリティ スコアと Office 365 の ID スコアに関する推奨事項は同じです。 


## <a name="next-steps"></a>次の手順

Office 365 のセキュリティ スコアに関するビデオを見る場合は、[ここ](https://www.youtube.com/watch?v=jzfpDJ9Kg-A)をクリックしてください。
 
