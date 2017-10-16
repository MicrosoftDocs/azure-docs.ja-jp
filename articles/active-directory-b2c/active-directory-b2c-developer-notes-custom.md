---
title: "Azure Active Directory B2C: カスタム ポリシーの使用に関する開発者向けのメモ | Microsoft Docs"
description: "カスタム ポリシーで Azure AD B2C を構成および管理する開発者向けのメモ"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C カスタム ポリシー パブリック プレビューのリリース ノート
カスタム ポリシー機能セットは、すべての Azure Active Directory B2C (Azure AD B2C) のユーザーがパブリック プレビューで評価できるようになりました。 この機能セットは、最も複雑な ID ソリューションを構築する上級 ID 開発者を対象としています。  

現在、この機能セットを使用するために、開発者は XML ファイルを編集して直接 Identity Experience Framework を構成する必要があります。 この構成の方法は、強力で複雑です。 Identity Experience Framework を使用する上級 ID 開発者は、チュートリアルを実行したり参考ドキュメントを確認したりするための時間を取ることを計画する必要があります。 

## <a name="features-included-in-this-public-preview"></a>このパブリック プレビューに含まれている機能
パブリック プレビューで導入された新機能を使用すると、開発者は次のタスクを実行できます。<br>

* カスタム ポリシーを使用して、カスタム認証のユーザー体験を作成およびアップロードする。 
   * 要求プロバイダー間のやり取りとしてユーザー体験を順を追って記述する。 
   * ユーザー体験の条件分岐を定義する。 
* カスタム認証のユーザー体験に REST API 対応サービスを統合する。  
* OpenIDConnect 標準に準拠している ID プロバイダーとのフェデレーションを追加する。 <br>
* SAML 2.0 プロトコルに準拠している ID プロバイダーとのフェデレーションを追加する。 

## <a name="terms-of-the-public-preview"></a>パブリック プレビューの使用条件

* 新機能の使用は、評価目的のみで使用することが推奨されています。<br>
* 新機能は、運用環境での使用を目的としていません。<br>
* サービス レベル アグリーメント (SLA) は新機能には適用されません。 <br>
* サポート要求は、通常のサポート チャネルを通じて提出できます。 <br>
* 一般公開の期日は確定されていません。<br>
* Microsoft の裁量や何らかの理由により、Microsoft は、Azure AD B2C 製品が顧客 ID およびアクセス管理 (CIAM) プラットフォームとしての役割を果たすための取り決めの範囲を超えるシナリオおよびユーザー体験を停止、拒否、および制限することができます。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>カスタム ポリシー機能セット開発者の責任
手動のポリシー構成で付与される Azure AD B2C の基になるプラットフォームへのアクセス レベルは低くなるため、独自の完全にカスタマイズ可能な信頼フレームワークが作成されます。 カスタム ID プロバイダー、信頼関係、外部サービスとの統合、ステップバイステップのワークフローの起こり得る配列により、それらを使用する上級開発者に対する要求はより厳しくなります。

パブリック プレビューを十分に活用するために、カスタム ポリシー機能セットを利用する開発者には、次の点に従うことをお勧めします。
* Identity Experience Engine とキー/シークレット管理の構成言語について理解を深める。
* シナリオおよびカスタム統合の主導権を得る。
* シナリオの体系的なテストを実行する。
* ソフトウェアの開発とステージングのベスト プラクティスに従い、少なくとも 1 つの開発/テスト環境と 1 つの運用環境を用意する。
* ID プロバイダーによる新しい開発と、統合するサービスに関する情報を常に入手する。 たとえば、シークレットの変更や、サービスの予定された変更と予定外の変更を把握します。
* アクティブな監視を設定し、運用環境の応答性を監視する。
* 連絡先のメール アドレスを最新に保ち、Microsoft のライブ サイト チームのメールに対応できるようにしておく。
* Microsoft ライブ サイト チームからのアドバイスがあった場合に、すぐに対処する。 


>[!NOTE]
>これらの機能は、最終的には Azure AD の組み込みポリシーに含まれ、すべての開発者にとって利用しやすくなる予定です。

## <a name="next-steps"></a>次のステップ
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)
