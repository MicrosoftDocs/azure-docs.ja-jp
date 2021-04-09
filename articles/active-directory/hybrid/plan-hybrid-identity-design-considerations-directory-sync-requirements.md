---
title: ハイブリッド ID 設計 - ディレクトリ同期要件 - Azure | Microsoft Docs
description: 企業のオンプレミスとクラウド間で、すべてのユーザーを同期するために必要な要件を特定します。
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278396"
---
# <a name="determine-directory-synchronization-requirements"></a>ディレクトリ同期要件の決定
同期とは、ユーザーのオンプレミス ID に基づいてクラウドに ID を提供することです。 認証またはフェデレーション認証のために同期されたアカウントを使用するかどうかにかかわらず、ユーザーはクラウドに ID を持つ必要があります。  この ID は保守し、定期的に更新する必要があります。  更新プログラムには、タイトルの変更からパスワードの変更まで、さまざまな形式があります。  

まず、組織のオンプレミス ID ソリューションとユーザーの要件を評価しましょう。 クラウドでユーザー ID を作成し、保守する方法の技術要件を定義するために、この評価は重要です。  多くの組織では、Active Directory はオンプレミスにあり、ユーザーを同期する元はオンプレミス ディレクトリになりますが、場合によってはオンプレミスではありません。  

次のチェック項目に回答してください。

* AD フォレストの数は 1 つですか、複数ですか、それともありませんか。
  
  * 同期する Azure AD ディレクトリ数はいくつですか。
    
    1. フィルター処理を使用していますか。
    2. 複数の Azure AD Connect サーバーを計画していますか。
* 現在、オンプレミスに同期ツールはありますか。
  
  * 「はい」の場合、ユーザーに仮想ディレクトリ/ID の統合はありますか。
* オンプレミスに同期したい他のディレクトリはありますか (LDAP ディレクトリ、HR データベースなど)。
  * GALSync を実行する予定はありますか。
  * 組織内の UPN は、現在どのような状態ですか。 
  * ユーザーを認証するディレクトリは他にもありますか。
  * 会社は Microsoft Exchange を使用していますか。
    * ハイブリッド Exchange デプロイを利用する予定はありますか。

同期要件について考えがまとまったら、これらの要件を満たすためにどのツールが適しているかを決定する必要があります。  Microsoft では、ディレクトリ統合と同期を実現するための複数のツールを提供しています。  詳細については、「 [ハイブリッド ID ディレクトリ統合ツールの比較表](plan-hybrid-identity-design-considerations-tools-comparison.md) 」を参照してください。 

会社の同期要件とこれを実現するツールの準備ができたら、これらのディレクトリ サービスを使用するアプリケーションを評価する必要があります。 この評価は、これらのアプリケーションをクラウドに統合する技術要件を定義するために重要です。 次のチェック項目に回答してください。

* これらのアプリケーションはクラウドに移行し、ディレクトリを使用する予定ですか。
* アプリケーションが属性を正常に使用するために、クラウドと同期する必要がある特殊な属性はありますか。
* クラウド認証を利用するために、これらのアプリケーションを作成し直す必要はありますか。
* これらのアプリケーションを今後もオンプレミスで実行し、ユーザーはクラウド ID を使用してアプリケーションにアクセスする必要はありますか。

また、ディレクトリ同期のセキュリティ要件と制限も決定する必要があります。 ユーザーの ID をクラウドに作成し、保守するために必要な要件の一覧を取得するには、この評価が重要です。 次のチェック項目に回答してください。

* 同期サーバーはどこに配置されますか。
* 同期サーバーはドメインに参加しますか。
* DMZ など、ファイアウォールの背後の制限付きネットワークにサーバーは配置されますか。
  * 同期をサポートする必要なファイアウォール ポートを開くことはできますか。
* 同期サーバーのディザスター リカバリー計画はありますか。
* 同期するフォレストについて、正しいアクセス許可を持つアカウントはありますか。
  * このチェック項目に対する答えがよくわからない場合は、記事「 [Azure Active Directory 同期サービスのインストール](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) 」のセクション「パスワード同期のためのアクセス許可」を参照し、それらのアクセス許可にアカウントが既にあるか、またはアカウントを作成する必要があるかを判断してください。
* 複数フォレストの同期がある場合、同期サーバーから各フォレストにアクセスできますか。

> [!NOTE]
> 回答をメモし、その背後にある論理的根拠を理解してください。 [インシデント対応要件の決定](plan-hybrid-identity-design-considerations-incident-response-requirements.md) 」では、使用できるオプションを確認します。 チェック項目に答えることで、ビジネス ニーズに最適な選択肢が見つかります。
> 
> 

## <a name="next-steps"></a>次のステップ
[多要素認証要件の決定](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>関連項目
[設計上の考慮事項の概要](plan-hybrid-identity-design-considerations-overview.md)