---
title: Azure AD B2C を使用した回復性があるエンドユーザー エクスペリエンス | Microsoft Docs
description: Azure AD B2C を使用してエンドユーザー エクスペリエンスで回復性を強化する方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6896a812ec173994a1299a28ff2e99a0f351391
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724443"
---
# <a name="resilient-end-user-experience"></a>回復性があるエンドユーザー エクスペリエンス

サインアップとサインインのエンドユーザー エクスペリエンスは、次の要素で構成されています。

- ユーザーが対話するインターフェイス: CSS、HTML、JavaScript など

- ご自身が作成するユーザー フローとカスタム ポリシー: サインアップ、サインイン、プロファイルの編集など

- お使いのアプリケーション用の ID プロバイダー (IDP): ローカル アカウントのユーザー名/パスワード、Outlook、Facebook、Google など

![画像は、エンドユーザー エクスペリエンスのコンポーネントを示しています](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>ユーザー フローとカスタム ポリシーのいずれかを選択する  

最も一般的な ID タスクを設定しやすくするために、Azure AD B2C には組み込みの構成可能な[ユーザー フロー](../../active-directory-b2c/user-flow-overview.md)が用意されています。 また、独自の[カスタム ポリシー](../../active-directory-b2c/custom-policy-overview.md)を作成することもできます。これにより、高い柔軟性が得られます。 ただし、カスタム ポリシーを使用するのは、複雑なシナリオに対応する場合に限ることをお勧めします。

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>ユーザー フローとカスタム ポリシーのどちらにするか決定する方法

組み込みのユーザー フローでビジネス要件を満たすことができる場合は、そちらを選択します。 Microsoft によって広範囲でテストされているため、これらの ID ユーザー フローのポリシー レベルの機能、パフォーマンス、またはスケールの検証に必要なテストを最小限に抑えることができます。 その場合でも、アプリケーションの機能、パフォーマンス、スケールをテストする必要があります。

ビジネス要件を満たすために[カスタム ポリシーを選択する](../../active-directory-b2c/custom-policy-get-started.md)場合は、アプリケーション レベルのテストに加え、ポリシー レベルでも機能、パフォーマンス、スケールをテストする必要があります。

[ユーザー フローとカスタム ポリシーを比較する](../../active-directory-b2c/custom-policy-overview.md#comparing-user-flows-and-custom-policies)記事を参照して、決定に役立ててください。

## <a name="choose-multiple-idps"></a>複数の IDP を選択する

Facebook などの[外部 ID プロバイダー](../../active-directory-b2c/technical-overview.md#external-identity-providers)を使用する場合は、外部プロバイダーが使用できなくなった場合に備えて、代替の計画を準備しておく必要があります。

### <a name="how-to-set-up-multiple-idps"></a>複数の IDP を設定する方法

外部 ID プロバイダーの登録プロセスの一環として、ユーザーの携帯電話番号やメール アドレスなどの、検証済みの ID 要求を含めてください。 検証済みの要求を、基になっている Azure AD B2C ディレクトリ インスタンスにコミットします。 外部プロバイダーが使用できない場合は、検証済みの ID 要求に戻り、認証方法として電話番号を使用します。 もう 1 つのオプションとして、ユーザーがサインインできるようにするワンタイム パスコードをユーザーに送信します。

 [代替の認証パスを作成](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)するには、次の手順に従います。

 1. ローカル アカウントと外部 IDP によるサインアップを許可するように、サインアップ ポリシーを構成します。

 2. ユーザーがサインインした後に[他の ID を自分のアカウントにリンク](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking)することを許可するように、プロファイル ポリシーを構成します。

 3. 障害発生時にはユーザーに通知し、[代替の IDP に切り替える](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)ことができるようにします。

## <a name="availability-of-multi-factor-authentication"></a>多要素認証の可用性

[多要素認証 (MFA) に電話サービス](../../active-directory-b2c/phone-authentication.md)を使用する場合は、代替のサービス プロバイダーを検討する必要があります。 ローカルの通信会社や電話サービス プロバイダーでは、サービスの中断が発生する場合があります。

### <a name="how-to-choose-an-alternate-mfa"></a>代替 MFA の選択方法  

Azure AD B2C サービスでは、組み込みの電話ベースの MFA プロバイダーを使用して、時間ベースのワンタイム パスコード (OTP) が配信されます。 これは、ユーザーの事前登録された電話番号への音声通話とテキスト メッセージの形式になっています。 次の代替方法はさまざまなシナリオで使用できます。

**ユーザー フロー** を使用する場合、回復性を強化する方法は 2 通りあります。

- **ユーザー フローの構成を変更する**:電話ベースの OTP 配信の中断が検出されたら、OTP の配信方法を電話ベースからメール ベースに変更し、アプリケーションを変更しないまま、ユーザー フローを再デプロイします。

![スクリーンショットはサインインとサインアップを示しています](media/resilient-end-user-experiences/create-sign-in.png)

- **アプリケーションを変更する**:サインアップやサインインなどの ID タスクごとに、2 セットのユーザー フローを定義します。 最初のセットでは電話ベースの OTP、2 番目のセットではメールベースの OTP を使用するように構成します。 電話ベースの OTP 配信の中断が検出されたら、ユーザー フローを変更しないまま、ユーザー フローの最初のセットから 2 番目に切り替えるようにアプリケーションを変更し、再デプロイします。  

**カスタム ポリシー** を使用する場合、回復性を強化する方法は 4 通りあります。 以下の一覧は下に行くほど複雑になっています。更新したポリシーは再デプロイする必要があります。

- **電話ベースの OTP かメール ベースの OTP かをユーザーが選択できるようにする**: 両方のオプションをユーザーに示し、ユーザーがいずれかのオプションを自分で選択できるようにします。 ポリシーにもアプリケーションにも変更を加える必要はありません。

- **電話ベースの OTP とメールベースの OTP を動的に切り替える**:サインアップ時に電話とメールの両方の情報を収集します。 電話の中断時に電話ベースからメール ベースの OTP 配信に条件付きで切り替えるように、カスタム ポリシーを事前に定義します。 ポリシーにもアプリケーションにも変更を加える必要はありません。

- **認証アプリを使用する**:[認証アプリ](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)を使用するようにカスタム ポリシーを更新します。 通常の MFA が電話ベースまたはメール ベースのいずれかの OTP である場合、認証アプリの使用に切り替えるようにカスタム ポリシーを再デプロイしてください。

>[!Note]
>ユーザーはサインアップ中に認証アプリの統合を構成する必要があります。

- **セキュリティの質問を使用する**:上記のいずれの方法も適用できない場合は、バックアップとしてセキュリティの質問を実装します。 オンボードまたはプロファイルの編集中にユーザーのセキュリティの質問を設定し、ディレクトリ以外の別のデータベースに回答を格納します。 この方法は、MFA の要件である "ユーザーが持っているもの" (電話など) は満たしていませんが、2 番目の "ユーザーが知っているもの" を提供します。

## <a name="use-a-content-delivery-network"></a>コンテンツ配信ネットワークを使用する

コンテンツ配信ネットワーク (CDN) は、カスタム ユーザー フロー UI を格納するための BLOB ストアよりもパフォーマンスが高く、コストが低くなります。 Web ページのコンテンツは、地理的に分散した高可用性サーバーのネットワークから高速に配信されます。  

エンド ツー エンドのシナリオとロード テストを通じて、CDN の可用性とコンテンツ配信のパフォーマンスを定期的にテストします。 プロモーションや休日のトラフィックによって今後見込まれる急上昇に対応した計画を立てる場合は、ロード テストの見積もりを修正してください。
  
## <a name="next-steps"></a>次のステップ

- [Azure AD B2C 開発者向けの回復性に関するリソース](resilience-b2c.md)
  
  - [回復性がある外部プロセスとのインターフェイス](resilient-external-processes.md)
  - [開発者のベスト プラクティスによる回復性](resilience-b2c-developer-best-practices.md)
  - [監視と分析による回復性](resilience-with-monitoring-alerting.md)
- [認証インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)
- [アプリケーションで認証と認可の回復性を向上させる](resilience-app-development-overview.md)