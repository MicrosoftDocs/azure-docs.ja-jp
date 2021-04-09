---
title: Azure AD Identity Protection でのリスク検出のシミュレーション
description: Identity Protection でリスク検出をシミュレートする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835902"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Identity Protection でのリスク検出のシミュレーション

管理者は、次の項目を実現するために、環境内のリスクをシミュレートすることができます。

- リスク検出と脆弱性をシミュレートして、Identity Protection 環境にデータを入力する。
- リスクベースの条件付きアクセス ポリシーを設定し、それらのポリシーの影響をテストする。

このセクションでは、次の種類のリスク検出をシミュレートするための手順を説明します。

- 匿名 IP アドレス (容易)
- 見慣れないサインイン プロパティ (中程度)
- 特殊な移動 (難解)

他のリスク検出は、安全な方法でシミュレートできません。

各リスク検出の詳細については、「[リスクとは](concept-identity-protection-risks.md)」を参照してください。

## <a name="anonymous-ip-address"></a>匿名 IP アドレス

次の手順を完了するには、以下を使用する必要があります。

- 匿名 IP アドレスをシミュレートするための [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en)。 組織が Tor Browser の使用を制限している場合は、仮想マシンの使用が必要になることがあります。
- まだ Azure AD Multi-Factor Authentication に登録されていないテスト アカウント。

**匿名 IP からサインインをシミュレートするには、次の手順を実行します**。

1. [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) を使用して [https://myapps.microsoft.com](https://myapps.microsoft.com) に移動します。   
2. **匿名 IP アドレスからのサインイン** レポートに表示するアカウントの資格情報を入力します。

10 ～ 15 分以内に Identity Protection ダッシュボードにサインインが表示されます。 

## <a name="unfamiliar-sign-in-properties"></a>通常とは異なるサインイン プロパティ

未知の場所をシミュレートするには、テスト アカウントがサインイン元として以前に使用したことのない場所とデバイスからサインインする必要があります。

以下の手順では、次のものを新たに作成して使用します。

- 新しい場所をシミュレートするための VPN 接続。
- 新しいデバイスをシミュレートするための仮想マシン。

以下の手順を完了するには、次のようなユーザー アカウントを使用する必要があります。

- 30 日以上のサインイン履歴がある。
- Azure AD Multi-Factor Authentication が有効になっている。

**未知の場所からサインインをシミュレートするには、次の手順を実行します**。

1. テスト アカウントでサインインするとき、多要素認証 (MFA) チャレンジを完了しないことで、MFA チャレンジを失敗させます。
2. 新しい VPN を使用して、[https://myapps.microsoft.com](https://myapps.microsoft.com) に移動し、テスト アカウントの資格情報を入力します。

10 ～ 15 分以内に Identity Protection ダッシュボードにサインインが表示されます。

## <a name="atypical-travel"></a>特殊な移動

アルゴリズムでは、機械学習を使用して、既知のデバイスからの特殊な移動や、ディレクトリ内の他のユーザーによって使用される VPN からのサインインなどの誤検知が除去されるため、特殊な移動の状態をシミュレートすることは困難です。 さらに、このアルゴリズムでは、リスク検出の生成を開始する前に、ユーザーには 14 日間のサインイン履歴と 10 回のログインが必要です。 複雑な機械学習モデルと上記のルールのため、次の手順ではリスク検出が発生しないこともあります。 このリスク検出をシミュレートするには、複数の Azure AD アカウントでこの手順を繰り返してください。

**特殊な移動のリスク検出をシミュレートするには、次の手順を実行します**。

1. 標準的なブラウザーを使用して、[https://myapps.microsoft.com](https://myapps.microsoft.com) に移動します。  
2. 特殊な移動のリスク検出を生成するアカウントの資格情報を入力します。
3. ユーザー エージェントを変更します。 開発者ツール (F12) から Microsoft Edge のユーザー エージェントを変更できます。
4. IP アドレスを変更します。 VPN または Tor アドオンを使用するか、Azure 上に別のデータ センターの新しい仮想マシンを作成することで、IP アドレスを変更できます。
5. 前と同じ資格情報を使用し、前のサインインから数分以内に、[https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。

2 ～ 4 時間以内に Identity Protection ダッシュボードにサインインが表示されます。

## <a name="testing-risk-policies"></a>リスク ポリシーのテスト

このセクションでは、ユーザー リスク ポリシーとサインイン リスク ポリシーをテストする手順を示します。これらのポリシーは、記事「[方法: リスク ポリシーを構成して有効にする](howto-identity-protection-configure-risk-policies.md)」を参照してください。

### <a name="user-risk-policy"></a>ユーザー リスクのポリシー

ユーザー リスク セキュリティ ポリシーをテストするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[概要]** の順に移動します。
1. **[ユーザー リスク ポリシーの構成]** を選択します。
   1. **[割り当て]** で:
      1. **[ユーザー]** - **[すべてのユーザー]** を選択します。ロールアウトを制限する場合は **[個人と グループの選択]** を選択します。
         1. 必要に応じて、ポリシーからユーザーを除外できます。
      1. **[条件]**  -  **[ユーザー リスク]** 。Microsoft で推奨しているのは、このオプションを **[高]** に設定することです。
   1. **[コントロール]** で:
      1. **[アクセス]** - Microsoft で推奨しているのは、 **[アクセスを許可]** と **[パスワードの変更を必須とする]** です。
   1. **[ポリシーを適用する]**  -  **[オフ]**
   1. **[保存]** - この操作を行うと、 **[概要]** ページに戻ります。
1. テスト アカウントのユーザーのリスクを評価します。たとえば、リスク検出のいずれかを数回シミュレートします。
1. 数分待った後、そのユーザーのリスクが上昇したことを確認します。 そうでない場合は、そのユーザーに対するリスク検出をさらにシミュレートします。
1. リスク ポリシーに戻り、 **[ポリシーの適用]** を **[オン]** に設定し、ポリシーの変更を **[保存]** します。
1. これで、リスク レベルを上げたユーザーを使用してサインインすることで、ユーザーのリスクに基づく条件付きアクセスをテストできます。

### <a name="sign-in-risk-security-policy"></a>サインインのリスク セキュリティ ポリシー

サインイン リスク ポリシーをテストするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[概要]** の順に移動します。
1. **[サインイン リスク ポリシーの構成]** を選択します。
   1. **[割り当て]** で:
      1. **[ユーザー]** - **[すべてのユーザー]** を選択します。ロールアウトを制限する場合は **[個人と グループの選択]** を選択します。
         1. 必要に応じて、ポリシーからユーザーを除外できます。
      1. **[条件]**  -  **[サインイン リスク]** 。Microsoft で推奨しているのは、このオプションを **[中以上]** に設定することです。
   1. **[コントロール]** で:
      1. **[アクセス]** - Microsoft で推奨しているのは、 **[アクセスを許可]** と **[多要素認証が必要です]** を設定することです。
   1. **[ポリシーの適用]**  -  **[オン]**
   1. **[保存]** - この操作を行うと、 **[概要]** ページに戻ります。
1. これで、リスクの高いセッションを使用してサインインすることで (たとえば、Tor Browser を使用して)、サインインのリスクに基づく条件付きアクセスをテストできます。 

## <a name="next-steps"></a>次のステップ

- [リスクとは](concept-identity-protection-risks.md)

- [方法: リスク ポリシーを構成して有効にする](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
