---
title: Azure Active Directory Identity Protection プレイブック | Microsoft Docs
description: Azure AD Identity Protection を使用して、侵害された ID またはデバイスを攻撃者が悪用する能力を制限する方法、および以前に疑われた、または侵害を確認された ID またはデバイスを保護する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a6aca2050676650b955ec078b47b2ffcfe319
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333932"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection プレイブック

このプレイブックは次の操作で役立ちます。

* リスク イベントと脆弱性をシミュレートし、ID 保護環境にデータを入力する
* リスクに基づく条件付きアクセス ポリシーを設定し、そのポリシーの影響をテストする

## <a name="simulating-risk-events"></a>リスク イベントのシミュレーション

このセクションでは、次の種類のリスク イベントをシミュレートするための手順を提供します。

* 匿名の IP アドレスからのサインイン (簡単)
* 未知の場所からのサインイン (普通)
* 特殊な場所へのあり得ない移動 (困難)

他のリスク イベントは、安全な方法でシミュレートすることはできません。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>匿名の IP アドレスからのサインイン

このリスク イベントの詳細については、「[匿名 IP アドレスからのサインイン](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses)」を参照してください。 

次の手順を完了するには、以下を使用する必要があります。

- 匿名 IP アドレスをシミュレートするための [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en)。 組織が Tor Browser の使用を制限している場合は、仮想マシンの使用が必要になることがあります。
- まだ多要素認証に登録されていないテスト アカウント。

**匿名 IP からサインインをシミュレートするには、次の手順を実行します**。

1. [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) を使用して [https://myapps.microsoft.com](https://myapps.microsoft.com) に移動します。   
2. **匿名 IP アドレスからのサインイン** レポートに表示するアカウントの資格情報を入力します。

10 ～ 15 分以内に Identity Protection ダッシュボードにサインインが表示されます。 

### <a name="sign-ins-from-unfamiliar-locations"></a>未知の場所からのサインイン

このリスク イベントの詳細については、「[未知の場所からのサインイン](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations)」を参照してください。 

未知の場所をシミュレートするには、テスト アカウントがサインイン元として以前に使用したことのない場所とデバイスからサインインする必要があります。

以下の手順では、次のものを新たに作成して使用します。

- 新しい場所をシミュレートするための VPN 接続。
- 新しいデバイスをシミュレートするための仮想マシン。

以下の手順を完了するには、次のようなユーザー アカウントを使用する必要があります。

- 30 日以上のサインイン履歴がある。
- 多要素認証が有効になっている。

**未知の場所からサインインをシミュレートするには、次の手順を実行します**。

1. テスト アカウントでサインインするとき、MFA チャレンジをパスしないことで、MFA チャレンジを失敗させます。
2. 新しい VPN を使用して、[https://myapps.microsoft.com](https://myapps.microsoft.com) に移動し、テスト アカウントの資格情報を入力します。

10 ～ 15 分以内に Identity Protection ダッシュボードにサインインが表示されます。

### <a name="impossible-travel-to-atypical-location"></a>特殊な場所へのあり得ない移動

このリスク イベントの詳細については、「[特殊な場所へのあり得ない移動](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations)」を参照してください。 

アルゴリズムでは、機械学習を使用して、既知のデバイスからのあり得ない移動や、ディレクトリ内の他のユーザーによって使用される VPN からのサインインなどの誤検知が除去されるため、あり得ない移動の状態をシミュレートすることは困難です。 さらに、リスク イベントの生成を開始する前に、ユーザーには 14 日間のサインイン履歴と 10 回のログインが必要です。 複雑な機械学習モデルと上記のルールのため、次の手順ではリスク イベントが発生しないこともあります。 このリスク イベントを発行するには、複数の Azure AD アカウントでこの手順を繰り返してください。

**特殊な場所へのあり得ない移動をシミュレートするには、次の手順を実行します**。

1. 標準的なブラウザーを使用して、[https://myapps.microsoft.com](https://myapps.microsoft.com) に移動します。  
2. あり得ない移動のリスク イベントを生成するアカウントの資格情報を入力します。
3. ユーザー エージェントを変更します。 ユーザー エージェントの変更は、Internet Explorer の開発者ツールから、あるいは Firefox または Chrome のユーザー エージェント切り替えアドオンを使用して、行うことができます。
4. IP アドレスを変更します。 IP アドレスの変更は、VPN または Tor アドオンを使用して、または別のデータセンターの Azure で新しいマシンを起動して、行うことができます。
5. 前と同じ資格情報を使用し、前のサインインから数分以内に、[https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。

2 ～ 4 時間以内に Identity Protection ダッシュボードにサインインが表示されます。

## <a name="simulating-vulnerabilities"></a>脆弱性のシミュレーション
脆弱性は、悪意のあるユーザーによって悪用される可能性のある Azure AD 環境内の弱点です。 現在、Azure AD Identity Protection では、Azure AD の他の機能を利用する 3 種類の脆弱性が明らかになっています。 これらの脆弱性は、以下の機能がセットアップされると Identity Protection ダッシュボードに自動的に表示されます。

* Azure AD [Multi-Factor Authentication](../authentication/multi-factor-authentication.md)
* Azure AD [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/)。
* Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

## <a name="testing-security-policies"></a>セキュリティ ポリシーのテスト

このセクションでは、ユーザー リスクとサインイン リスクのセキュリティ ポリシーをテストする手順を示します。

### <a name="user-risk-security-policy"></a>ユーザーのリスク セキュリティ ポリシー

詳細については、「[方法: ユーザー リスク ポリシーを構成する](howto-user-risk-policy.md)」をご覧ください。

![ユーザーのリスク](./media/playbook/02.png "プレイブック")

**ユーザーのリスク セキュリティ ポリシーをテストするには、次の手順に従います**。

1. テナントのグローバル管理者の資格情報を使用して [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. **Identity Protection**に移動します。 
3. **[Azure AD Identity Protection]** ページで、 **[ユーザーのリスク ポリシー]** をクリックします。
4. **[割り当て]** セクションで、目的のユーザー (およびグループ) とユーザーのリスク レベルを選択します。

    ![ユーザーのリスク](./media/playbook/03.png "プレイブック")

5. [コントロール] セクションで、必要なアクセスの制御 ([パスワードの変更を必須とする] など) を選択します。
5. **[ポリシーを適用します]** で **[オフ]** を選択します。
6. テスト アカウントのユーザーのリスクを評価します。たとえば、リスク イベントのいずれかを数回シミュレートします。
7. 数分待った後、そのユーザーのユーザー レベルが "中" であることを確認します。 そうでない場合は、そのユーザーに対してリスク イベントをさらにシミュレートします。
8. **[ポリシーを適用します]** で **[オン]** を選択します。
9. これで、リスク レベルを上げたユーザーを使用してサインインすることで、ユーザーのリスクに基づく条件付きアクセスをテストできます。

### <a name="sign-in-risk-security-policy"></a>サインインのリスク セキュリティ ポリシー

詳細については、「[方法: サインイン リスク ポリシーを構成する](howto-sign-in-risk-policy.md)」をご覧ください。

![サインインのリスク](./media/playbook/01.png "プレイブック")

**サインインのリスク ポリシーをテストするには、次の手順に従います。**

1. テナントのグローバル管理者の資格情報を使用して [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. **Azure AD Identity Protection** に移動します。
3. **[Azure AD Identity Protection]** メイン ページで、 **[サインインのリスク ポリシー]** をクリックします。 
4. **[割り当て]** セクションで、目的のユーザー (およびグループ) とサインインのリスク レベルを選択します。

    ![サインインのリスク](./media/playbook/04.png "プレイブック")

5. **[コントロール]** セクションで、必要なアクセスの制御 ( **[多要素認証を要求する]** など) を選択します。 
6. **[ポリシーを適用します]** で **[オン]** を選択します。
7. **[Save]** をクリックします。
8. これで、リスクの高いセッションを使用してサインインすることで (たとえば、Tor Browser を使用して)、サインインのリスクに基づく条件付きアクセスをテストできます。 

## <a name="see-also"></a>関連項目

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
