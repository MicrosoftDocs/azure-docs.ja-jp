---
title: "アクセスおよび使用状況レポートを表示する | Microsoft Docs"
description: "アクセスおよび使用状況レポートを使用して組織のディレクトリの整合性とセキュリティを把握する方法を説明します。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: a074bc4e-cf3f-4ad1-8cc6-4199d2e09ce4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8b3835d354512645cd47ffa4541d6155c80e77ee


---
# <a name="view-your-access-and-usage-reports"></a>アクセスおよび使用状況レポートの表示
*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です。*

Azure Active Directory のアクセスおよび使用状況レポートを使用すると、組織のディレクトリの整合性とセキュリティを表示できます。 ディレクトリ管理者は、この情報を使用して、リスクを軽減するために適切に計画できるように、セキュリティ上のリスクがある箇所をより適切に確認できます。

レポートは、Microsoft Azure 管理ポータルで、次の方法で分類されます。

* 異常レポート: 異常と考えられるサインイン イベントが含まれます。 この目的は、このようなアクティビティを認識し、イベントが不審であるかどうかを判断できるようにすることです。
* 統合アプリケーション レポート – 組織内のクラウド アプリケーションの使用状況を明らかにします。 Azure Active Directory は、何千ものクラウド アプリケーションとの統合を提供します。
* エラー レポート: 外部アプリケーションにアカウントをプロビジョニングするときに発生するエラーを示します。
* ユーザー固有レポート: ディスプレイ デバイス/サインイン アクティビティのデータを特定のユーザーに表示します。
* アクティビティ ログ - 過去 24 時間、過去 7 日間、または過去 30 日間のすべての監査イベントの記録、グループのアクティビティの変更、およびパスワードのリセットと登録のアクティビティが含まれます。

> [!NOTE]
> * いくつかの高度な異常およびリソース使用状況レポートは、 [Azure Active Directory Premium](active-directory-get-started-premium.md)を有効にした場合にのみ使用可能です。 高度なレポートでは、アクセスのセキュリティ、潜在的な脅威への対処を向上させ、デバイスへのアクセスやアプリケーションの使用状況に関する分析機能を利用できます。
> * 中国のお客様は、Azure Active Directory の世界中のインスタンスを使用して、Azure Active Directory Premium および Basic エディションを利用できます。 中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure Active Directory Premium および Basic エディションは現在サポートされていません。 詳細については、 [Azure Active Directory フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からお問い合わせください。
> 
> 

## <a name="reports"></a>レポート
| レポート | Description |
| --- | --- |
| **異常アクティビティ レポート** | |
| [不明なソースからのサインイン](active-directory-reporting-sign-ins-from-unknown-sources.md) |追跡されないサインインの試行を示す場合があります。 |
| [複数のエラー後のサインイン](active-directory-reporting-sign-ins-after-multiple-failures.md) |ブルート フォース攻撃の成功を示す場合があります。 |
| [複数の地域からのサインイン](active-directory-reporting-sign-ins-from-multiple-geographies.md) |複数のユーザーが同じアカウントでサインインしていることを示す場合があります。 |
| [不審なアクティビティのある IP アドレスからのサインイン](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |持続的な侵入の試行後のサインインの成功を示す場合があります。 |
| [感染している可能性があるデバイスからのサインイン](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |感染の疑いのあるデバイスからサインインしようとした可能性があります。 |
| [不規則なサインイン アクティビティ](active-directory-reporting-irregular-sign-in-activity.md) |ユーザーのサインイン パターンに対して異常なイベントを示す場合があります。 |
| [異常なサインイン アクティビティがあるユーザー](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |アカウントが侵害された可能性があるユーザーを示します。 |
| 資格情報が漏洩したユーザー |資格情報が漏洩したユーザー |
| **アクティビティ ログ** | |
| 監査レポート |ディレクトリの監査されたイベント |
| パスワード リセット アクティビティ |組織内で行われたパスワード リセットに関する詳しい情報を示します。 |
| パスワード リセット登録アクティビティ |組織内で行われたパスワード リセット登録の詳しい情報を示します。 |
| セルフ サービス グループ アクティビティ |ディレクトリ内のすべてのグループ セルフ サービス アクティビティに関するアクティビティ ログを提供します。 |
| **統合アプリケーション** | |
| アプリケーションの使用状況 |ディレクトリと統合されたすべての SaaS アプリケーションの利用状況の概要を提供します。 |
| アカウント プロビジョニングのアクティビティ |外部アプリケーションにアカウントのプロビジョニングを試行した回数の履歴を示します。 |
| パスワード ロールオーバーの状態 |SaaS アプリケーションの自動パスワード ロールオーバーの状態の詳細な説明を入力します。 |
| アカウント プロビジョニング エラー |外部アプリケーションへのユーザーのアクセスに対する影響を示します。 |
| **Rights management** | |
| RMS の利用状況 |Rights Management の利用状況の概要を示します。 |
| 最もアクティブな RMS ユーザー |RMS で保護されたファイルにアクセスした上位 1,000 名のアクティブ ユーザーを示します。 |
| RMS デバイスの利用状況 |RMS で保護されたファイルにアクセスするために使用されたデバイスを示します。 |
| RMS 対応アプリケーションの利用状況 |RMS 対応アプリケーションの利用状況を示します。 |

## <a name="report-editions"></a>レポートのエディション
| レポート | 無料 | 基本 | プレミアム |
| --- | --- | --- | --- |
| **異常アクティビティ レポート** | | | |
| 不明なソースからのサインイン |✓ |✓  |✓ |
| 複数のエラー後のサインイン |✓ |✓  |✓ |
| 複数の地域からのサインイン |✓ |✓  |✓ |
| 不審なアクティビティのある IP アドレスからのサインイン | | |✓ |
| 感染している可能性があるデバイスからのサインイン | | |✓ |
| 不規則なサインイン アクティビティ | | |✓ |
| 異常なサインイン アクティビティがあるユーザー | | |✓ |
| 資格情報が漏洩したユーザー | | |✓ |
| **アクティビティ ログ** | | | |
| 監査レポート |✓ |✓  |✓ |
| パスワード リセット アクティビティ | | |✓ |
| パスワード リセット登録アクティビティ | | |✓ |
| セルフ サービス グループ アクティビティ | | |✓ |
| **統合アプリケーション** | | | |
| アプリケーションの使用状況 | | |✓ |
| アカウント プロビジョニングのアクティビティ |✓ |✓  |✓ |
| パスワード ロールオーバーの状態 | | |✓ |
| アカウント プロビジョニング エラー |✓ |✓  |✓ |
| **Rights Managment** | | | |
| RMS の利用状況 | | |RMS のみ |
| 最もアクティブな RMS ユーザー | | |RMS のみ |
| RMS デバイスの利用状況 | | |RMS のみ |
| RMS 対応アプリケーションの利用状況 | | |RMS のみ |

## <a name="anomalous-activity-reports"></a>異常アクティビティ レポート
<p>異常サインイン アクティビティ レポートは、Office365、Microsoft Azure 管理ポータル、Azure AD アクセスパネル、Sharepoint Online、Dynamics CRM Online、その他の Microsoft オンライン サービスに対する不審なサインイン アクティビティにフラグを設定します。</p>

<p>"複数のエラー後のサインイン" レポートを除くすべてのレポートでは、前述のサービスに対する不審な<i>フェデレーション</i> サインインにもフラグが設定されます。これはフェデレーション プロバイダーを問いません。 </p>

<p>次のレポートを使用できます。 </p><ul>

<li>[不明なソースからのサインイン](active-directory-reporting-sign-ins-from-unknown-sources.md)の一部です</li>

<li>[複数のエラー後のサインイン](active-directory-reporting-sign-ins-after-multiple-failures.md)の一部です</li>

<li>[複数の地域からのサインイン](active-directory-reporting-sign-ins-from-multiple-geographies.md)の一部です</li>

<li>[不審なアクティビティのある IP アドレスからのサインイン](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)の一部です</li>

<li>[不規則なサインイン アクティビティ](active-directory-reporting-irregular-sign-in-activity.md)の一部です</li>

<li>[感染している可能性があるデバイスからのサインイン](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)の一部です</li>

<li>[異常なサインイン アクティビティがあるユーザー](active-directory-reporting-users-with-anomalous-sign-in-activity.md)の一部です</li>

<li>資格情報が漏洩したユーザー</li></ul>

## <a name="activity-logs"></a>アクティビティ ログ
### <a name="audit-report"></a>監査レポート
| Description | レポートの場所 |
|:--- |:--- |
| 過去 24 時間、過去 7 日間、または過去 30 日間内のすべての監査イベントのレコードを表示します。 <br /> 詳細については、 [Azure Active Directory の監査レポート イベント](active-directory-reporting-audit-events.md) |[ディレクトリ] > [レポート] タブ |

![監査レポート](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### <a name="password-reset-activity"></a>パスワード リセット アクティビティ
| Description | レポートの場所 |
|:--- |:--- |
| 組織で実行されたすべてのパスワードのリセットの試行が表示されます。 |[ディレクトリ] > [レポート] タブ |

![パスワード リセット アクティビティ](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### <a name="password-reset-registration-activity"></a>パスワード リセット登録アクティビティ
| Description | レポートの場所 |
|:--- |:--- |
| 組織で実行されたすべてのパスワード リセットの登録が表示されます。 |[ディレクトリ] > [レポート] タブ |

![パスワード リセット登録アクティビティ](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### <a name="self-service-groups-activity"></a>セルフ サービス グループ アクティビティ
| Description | レポートの場所 |
|:--- |:--- |
| ディレクトリ内でセルフ サービスにより管理されるグループのすべてのアクティビティが表示されます。 |[ディレクトリ] > [ユーザー] > <i>[ユーザー]</i> > [デバイス] タブ |

![セルフ サービス グループ アクティビティ](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)

## <a name="integrated-applications-reports"></a>統合アプリケーション レポート
### <a name="application-usage-summary"></a>アプリケーションの使用状況: 概要
| Description | レポートの場所 |
|:--- |:--- |
| ディレクトリ内のすべての SaaS アプリケーションについて使用状況を表示するには、このレポートを使用します。 このレポートは、アクセス パネルでユーザーがアプリケーションをクリックした回数の合計に基づきます。 |[ディレクトリ] > [レポート] タブ |

このレポートには、事前に統合された Microsoft アプリケーションを含め、ディレクトリがアクセス権を持つ *すべて* のアプリケーションへのサインインが含まれます。

事前に統合された Microsoft アプリケーションには、Office 365、Sharepoint、Microsoft Azure 管理ポータルなどが含まれます。

![アプリケーションの利用状況の概要](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)

### <a name="application-usage-detailed"></a>アプリケーションの使用状況: 詳細
| Description | レポートの場所 |
|:--- |:--- |
| どの程度、特定の SaaS アプリケーションが使用されているかを表示するときに、このレポートを使用します。 このレポートは、アクセス パネルでユーザーがアプリケーションをクリックした回数の合計に基づきます。 |[ディレクトリ] > [レポート] タブ |

### <a name="application-dashboard"></a>アプリケーション ダッシュボード
| Description | レポートの場所 |
|:--- |:--- |
| このレポートは、選択した間隔で、組織内のユーザーによるアプリケーションへの累計サインインを示します。 ダッシュボード ページ上のグラフは、そのアプリケーションのすべての使用状況の傾向を把握するために役立ちます。 |[ディレクトリ] > [アプリケーション] > [ダッシュボード] タブ |

## <a name="error-reports"></a>エラー レポート
### <a name="account-provisioning-errors"></a>アカウント プロビジョニング エラー
| Description | レポートの場所 |
|:--- |:--- |
| SaaS アプリケーションから Azure Active Directory へのアカウントの同期中に発生するエラーを監視する場合に使用します。 |[ディレクトリ] > [レポート] タブ |

![アカウント プロビジョニング エラー](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)

## <a name="user-specific-reports"></a>ユーザー固有レポート
### <a name="devices"></a>デバイス
| Description | レポートの場所 |
|:--- |:--- |
| 特定のユーザーが Azure Active Directory へのアクセスに使用したデバイスの IP アドレスと地理的な場所を表示する場合は、このレポートを使用します。 |[ディレクトリ] > [ユーザー] > <i>[ユーザー]</i> > [デバイス] タブ |

### <a name="activity"></a>アクティビティ
| Description | レポートの場所 |
|:--- |:--- |
| ユーザーのサインイン アクティビティを表示します。 このレポートには、サインインされたアプリケーション、使用されたデバイス、IP アドレス、および場所などの情報が含まれています。 Microsoft アカウントでサインインしたユーザーの履歴は収集されません。 |[ディレクトリ] > [ユーザー] > <i>[ユーザー]</i> > [アクティビティ] タブ |

#### <a name="sign-in-events-included-in-the-user-activity-report"></a>ユーザーのアクティビティ レポートに含まれるサインイン イベント
特定の種類のイベントへのサインインだけが、ユーザーのアクティビティ レポートに表示されます。

| イベントの種類 | 表示の有無 |
| --- | --- |
|  [アクセス パネル](http://myapps.microsoft.com/) |はい |
|  [Microsoft Azure 管理ポータル](https://manage.windowsazure.com/) |はい |
|  [ポータル](https://portal.azure.com/) |はい |
|  [Office 365 ポータル](http://portal.office.com/) |はい |
| Outlook などのネイティブのアプリケーションへのサインイン (次の例外を参照してください) |はい |
| アクセス パネルを通じた Salesforce などのフェデレーション/プロビジョニング アプリケーションへのサインイン |はい |
| アクセス パネルを通じた Twitter などのパスワードに基づくアプリケーションへのサインイン |はい |
| ディレクトリに追加されているカスタム ビジネス アプリケーションへのサインイン |なし (近日対応予定) |
| ディレクトリに追加されている Azure AD アプリケーション プロキシ アプリへのサインイン |なし (近日対応予定) |

> 注: このレポートでは、ノイズの量を減らすために、 [Microsoft Online Services サインイン アシスタント](http://community.office365.com/en-us/w/sso/534.aspx) によるサインインは表示されません。
> 
> 

## <a name="things-to-consider-if-you-suspect-security-breach"></a>セキュリティ違反が疑われる場合の考慮事項
ユーザー アカウントが漏洩した可能性があると疑う場合、またはクラウド内のディレクトリ データのセキュリティ違反につながる可能性があるなんらかの種類の不審なユーザー アクティビティがある場合は、次の 1 つ以上のアクションを検討することができます。

* ユーザーに連絡を取って、アクティビティについて確認する
* ユーザーのパスワードをリセットする
* [多要素認証](../multi-factor-authentication/multi-factor-authentication-get-started.md) を有効にする

## <a name="view-or-download-a-report"></a>レポートの閲覧またはダウンロード:
1. Azure クラシック ポータルで、**[Active Directory]** をクリックし、組織のディレクトリの名前をクリックし、**[レポート]** をクリックします。
2. [レポート] ページで、閲覧またはダウンロードするレポート をクリックします。
   
   > [!NOTE]
   > Azure Active Directory のレポート機能を初めて使用した場合は、内容を選択するためのメッセージが表示されます。 同意する場合は、チェック マーク アイコンをクリックして続行します。
   > 
   > 
3. [間隔] の横にあるドロップダウン メニューをクリックしてから、このレポートを生成するときに使用する、次のいずれかの期間をクリックします。
   
   * 過去 24 時間
   * 過去 7 日間
   * 過去 30 日間
4. レポートを実行するには、チェック マーク アイコンをクリックします。
   * Azure クラシック ポータルに最大 1,000 件のイベントが表示されます。
5. 必要に応じて **[ダウンロード]** をクリックして、オフラインでの閲覧またはアーカイブ目的のために、コンマ区切り値 (CSV) 形式で圧縮されたファイルのレポートをダウンロードします。
   * ダウンロードしたファイルには、最大 75,000 件のイベントが含まれます。
   * 詳細については、「 [Azure AD Reporting API の概要](active-directory-reporting-api-getting-started.md)」を参照してください。

## <a name="ignore-an-event"></a>イベントを無視する
異常レポートを表示している場合は、関連するレポートに表示されるさまざまなイベントを無視できることがあります。 イベントを無視するには、レポート内のイベントを強調表示して、 **[無視]**をクリックします。 **[無視]** ボタンをクリックすると、強調表示されているイベントが永続的にレポートから削除され、ライセンスを付与されたグローバル管理者のみがこのイベントを使用できます。

## <a name="automatic-email-notifications"></a>電子メールの自動通知
Azure AD のレポート通知の詳細については、「 [Azure Active Directory のレポート通知](active-directory-reporting-notifications.md)」をご確認ください。

## <a name="whats-next"></a>参照トピック
* [Azure Active Directory Premium の概要](active-directory-get-started-premium.md)
* [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)




<!--HONumber=Nov16_HO3-->


