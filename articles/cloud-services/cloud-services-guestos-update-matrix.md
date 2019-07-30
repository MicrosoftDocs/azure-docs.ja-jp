---
title: 最新の Azure ゲスト OS のリリースについて | Microsoft Docs
description: Azure Cloud Services のゲスト OS の最新のリリース情報と SDK との互換性について説明します。
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 7/8/2019
ms.author: raiye
ms.openlocfilehash: 88c3cd0e07e207a8b5ae1c07d39c8829a531c743
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721131"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure ゲスト OS リリースと SDK の互換性対応表
Cloud Services 向けの最新の Azure ゲスト OS リリースの最新情報を提供します。 この情報は、ゲスト OS が無効になる前にアップグレード パスを計画する際に役立ちます。 ロールを構成して、「 *Azure ゲスト OS の更新設定* 」に説明されているゲスト OS の [自動][Azure Guest OS Update Settings]更新を使用する場合、このページを読むことは必須ではありません。

> [!IMPORTANT]
> このページは、ゲスト OS 上で実行される Cloud Services Web ロールと worker ロールに適用されます。 IaaS Virtual Machines には **適用されません** 。
>
>


> [!TIP]
>  [ゲスト OS 更新 RSS フィード]をサブスクライブして、ゲスト OS のすべての変更に関する通知をタイムリーに受信してください。
>
>

> [!IMPORTANT]
> Azure portal ではゲスト OS の最新の 2 バージョンのみがサポートされて、使用できるようになります。
>
>

ゲスト OS の更新方法については、 [こちら][cloud updates]を確認してください。

## <a name="news-updates"></a>最新情報

###### <a name="july-8-2019"></a>**2019 年 7 月 8 日**
6 月のゲスト OS がリリースされました。

###### <a name="june-6-2019"></a>**2019 年 6 月 6 日**
5 月のゲスト OS がリリースされました。

###### <a name="may-7-2019"></a>**2019 年 5 月 7 日**
4 月のゲスト OS がリリースされました。

###### <a name="march-26-2019"></a>**2019 年 3 月 26 日**
3 月のゲスト OS がリリースされました。

###### <a name="march-12-2019"></a>**2019 年 3 月 12 日**
2 月のゲスト OS がリリースされました。

###### <a name="february-5-2019"></a>**2019 年 2 月 5 日**
1 月のゲスト OS がリリースされました。

###### <a name="january-24-2019"></a>**2019 年 1 月 24 日**
ファミリ 6 のゲスト OS (Windows Server 2019) がリリースされました。

###### <a name="january-7-2019"></a>**2019 年 1 月 7日**
12 月のゲスト OS がリリースされました。

###### <a name="december-14-2018"></a>**2018 年 12 月 14 日**
11 月のゲスト OS がリリースされました。

###### <a name="november-8-2018"></a>**2018 年 11 月 8 日**
10 月のゲスト OS がリリースされました。

###### <a name="october-12-2018"></a>**2018 年 10 月 12 日**
9 月のゲスト OS がリリースされました。

## <a name="releases"></a>リリース

## <a name="family-6-releases"></a>ファミリ 6 のリリース
**Windows Server 2019**

インストールされている .NET Framework:3.5、4.7.2、4.8

> [!NOTE]
> Windows Azure SDK for .NET - 3.0 は、[こちら][Windows Azure SDK]からダウンロードできます。
>
>インストール手順:
>1. MicrosoftAzureAuthoringTools*.msi の古いバージョンをすべてアンインストールします
>2. [Azure SDK for .NET - 3.0][Windows Azure SDK] をインストールします
>3. マシンを再起動します
>4. 新しいクラウド サービス プロジェクトを作成し、1 つの worker ロールを追加します
>5. OS ファミリを 6 に変更し、パッケージをビルドします
>6. Azure portal または Visual Studio を使用して Azure にパッケージをデプロイします
>


| 構成文字列 | リリース日 | 無効になる日 |
| --- | --- | --- |
| WA-GUEST-OS-6.8_201906-01 |2019 年 7 月 8 日 |Post 6.10 |
| WA-GUEST-OS-6.7_201905-01 |2019 年 6 月 6 日 |Post 6.9 |
|~~WA-GUEST-OS-6.6_201904-01~~ |2019 年 5 月 7 日 |2019 年 7 月 8 日 |
|~~WA-GUEST-OS-6.5_201903-01~~ |2019 年 3 月 26 日 |2019 年 6 月 6 日 |
|~~WA-GUEST-OS-6.4_201902-01~~ |2019 年 3 月 12 日 |2019 年 5 月 7 日 |
|~~WA-GUEST-OS-6.3_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-6.2_201812-01~~ |2019 年 1 月 24 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-6.1_201811-01~~ |2019 年 1 月 24 日 |2019 年 2 月 5 日 |

## <a name="family-5-releases"></a>ファミリ 5 のリリース
**Windows Server 2016**

インストールされている .NET Framework:3.5、4.6.2、4.7.2、4.8

> [!NOTE]
> OS ファミリ 5 用の RDP パスワードは、10 文字以上にする必要があります。
>


| 構成文字列 | リリース日 | 無効になる日 |
| --- | --- | --- |
| WA-GUEST-OS-5.32_201906-01 |2019 年 7 月 8 日 |Post 5.34 |
| WA-GUEST-OS-5.31_201905-01 |2019 年 6 月 6 日 |Post 5.33 |
|~~WA-GUEST-OS-5.30_201904-01~~ |2019 年 5 月 7 日 |2019 年 7 月 8 日 |
|~~WA-GUEST-OS-5.29_201903-01~~ |2019 年 3 月 26 日 |2019 年 6 月 6 日 |
|~~WA-GUEST-OS-5.28_201902-01~~ |2019 年 3 月 12 日 |2019 年 5 月 7 日 |
|~~WA-GUEST-OS-5.27_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-5.26_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-5.25_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-5.24_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-5.23_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-4-releases"></a>ファミリ 4 のリリース
**Windows Server 2012 R2**

インストールされている .NET Framework:3.5、4.5.1、4.5.2

| 構成文字列 | リリース日 | 無効になる日 |
| --- | --- | --- |
| WA-GUEST-OS-4.67_201906-01 |2019 年 7 月 8 日 |Post 4.69 |
| WA-GUEST-OS-4.66_201905-01 |2019 年 6 月 6 日 |Post 4.68 |
|~~WA-GUEST-OS-4.65_201904-01~~ |2019 年 5 月 7 日 |2019 年 7 月 8 日 |
|~~WA-GUEST-OS-4.64_201903-01~~ |2019 年 3 月 26 日 |2019 年 6 月 6 日 |
|~~WA-GUEST-OS-4.63_201902-01~~ |2019 年 3 月 12 日 |2019 年 5 月 7 日 |
|~~WA-GUEST-OS-4.62_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-4.61_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-4.60_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-4.59_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-4.58_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-3-releases"></a>ファミリ 3 のリリース
**Windows Server 2012**

インストールされている .NET Framework:3.5、4.5

| 構成文字列 | リリース日 | 無効になる日 |
| --- | --- | --- |
| WA-GUEST-OS-3.74_201906-01 |2019 年 7 月 8 日 |Post 3.76 |
| WA-GUEST-OS-3.73_201905-01 |2019 年 6 月 6 日 |Post 3.75 |
|~~WA-GUEST-OS-3.72_201904-01~~ |2019 年 5 月 7 日 |2019 年 7 月 8 日 |
|~~WA-GUEST-OS-3.71_201903-01~~ |2019 年 3 月 26 日 |2019 年 6 月 6 日 |
|~~WA-GUEST-OS-3.70_201902-01~~ |2019 年 3 月 12 日 |2019 年 5 月 7 日 |
|~~WA-GUEST-OS-3.69_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-3.68_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-3.67_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-3.66_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-3.65_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-2-releases"></a>ファミリ 2 のリリース
**Windows Server 2008 R2 SP1**

インストールされている .NET Framework:3.5 (2.0 と 3.0 を含む)、4.5

| 構成文字列 | リリース日 | 無効になる日 |
| --- | --- | --- |
| WA-GUEST-OS-2.87_201906-01 |2019 年 7 月 8 日 |Post 2.89 |
| WA-GUEST-OS-2.86_201905-01 |2019 年 6 月 6 日 |Post 2.88 |
|~~WA-GUEST-OS-2.85_201904-01~~ |2019 年 5 月 7 日 |2019 年 7 月 8 日 |
|~~WA-GUEST-OS-2.84_201903-01~~ |2019 年 3 月 26 日 |2019 年 6 月 6 日 |
|~~WA-GUEST-OS-2.83_201902-01~~ |2019 年 3 月 12 日 |2019 年 5 月 7 日 |
|~~WA-GUEST-OS-2.82_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-2.81_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-2.80_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-2.79_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-2.78_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="msrc-patch-updates"></a>MSRC 修正プログラム
毎月のゲスト OS リリースに含まれる修正プログラムの一覧は、[こちら][patches]で入手できます。

## <a name="sdk-support"></a>SDK のサポート
[Azure SDK の提供終了ポリシー][retire policy sdk]には 2.2 以降のバージョンのみがサポートされると示されていますが、特定のゲスト OS ファミリではそれより前のバージョンも使用できます。 常に、最新のサポートされている SDK を使用する必要があります。

| ゲスト OS ファミリ | 互換性のある SDK のバージョン |
| --- | --- |
| 6 |バージョン 2.9.6 以上 |
| 5 |バージョン 2.9.5.1 以上 |
| 4 |バージョン 2.1 以上 |
| 3 |バージョン 1.8 以上 |
| 2 |バージョン 1.3 以上 |
| 1 |バージョン 1.0 以上 |

## <a name="guest-os-release-information"></a>ゲスト OS のリリース情報
ゲスト OS リリースに重要な 3 つの日付として、**リリース日**、**無効になる日**、**有効期限**があります。 ゲスト OS は、それがポータルにあり、ターゲット ゲスト OS として選択できる場合に、使用可能と見なされます。 ゲスト OS は、**無効になる**日に達すると Azure から削除されます。 ただし、そのゲスト OS を対象とするクラウド サービスは引き続き通常どおりに動作します。

**無効になる**日から**有効期限**までの間に一定の期間を設けているため、新しいゲスト OS に簡単に切り替えることができます。 ゲスト OS として " *自動* " バージョンを使用していると、常に最新バージョンに維持されるため、期限切れを心配する必要はありません。

**有効期限**を過ぎると、そのゲスト OS を引き続き使用しているクラウド サービスが停止、削除、または強制的にアップグレードされます。 提供終了ポリシーの詳細については、[こちら][retirepolicy]を参照してください。

## <a name="guest-os-family-version-explanation"></a>ゲスト OS ファミリ、バージョンの説明
ゲスト OS ファミリは Microsoft Windows Server のリリース バージョンに基づいています。 ゲスト OS は、Azure Cloud Services が実行される、基になるオペレーティング システムです。 各ゲスト OS には、ファミリ、バージョン、リリース番号があります。

* **Guest OS family**  
  ゲスト OS の基盤となる Windows Server オペレーティング システムのリリース。 たとえば、" *ファミリ 3* " は Windows Server 2012 を基にしています。
* **ゲスト OS バージョン**  
  ゲスト OS ファミリ イメージと、関連する [Microsoft セキュリティ レスポンス センター (MSRC)][msrc] 修正プログラム (新しいゲスト OS バージョンが作成された時点で利用可能になる) の組み合わせに固有のバージョン。 すべての修正プログラムが含まれているわけではありません。

    番号は 0 から始まり、新しい更新プログラムのセットが追加されるたびに 1 ずつ増えます。 末尾のゼロが表示されるのは、重要な場合のみです。 つまり、バージョン 2.10 は、バージョン 2.1 よりかなり後の異なるバージョンです。
* **ゲスト OS リリース**  
  ゲスト OS バージョンの再リリース。 再リリースは、変更を必要とする問題がテスト中に見つかった場合に行われます。 最新のリリースは、公開されているかどうかに関係なく、常に以前のリリースよりも優先されます。 Azure Portal では、ユーザーは特定のバージョンの最新のリリースのみを取得できます。 前のリリースで実行中のデプロイは、通常、バグの重大度に応じてアップグレードが強制されることはありません。

次の例では、2 がファミリ、12 がバージョン、"rel2" がリリースです。

**ゲスト OS リリース** - 2.12 rel2

**このリリースの構成文字列** -WA-GUEST-OS-2.12_201208-02

ゲスト OS の構成文字列には、組み込まれているのと同じ情報に加えて、そのリリースで考慮された MSRC 修正プログラムを示す日付が含まれます。 この例では、2012 年 8 月までに Windows Server 2008 R2 用に作成された MSRC 修正プログラムが含まれることが想定されます。 そのバージョンの Windows Server に特に適用される修正プログラムのみが含まれます。 たとえば、Microsoft Office に適用される MSRC 修正プログラムは、製品が Windows Server 基本イメージに属さないため、ここに含まれません。

## <a name="guest-os-system-update-process"></a>ゲスト OS システムの更新プロセス
このページには、今後予定されているゲスト OS のリリースに関する情報が含まれます。 "自動" 更新に設定している場合はクラウド サービスのロールが再起動されるため、リリースのタイミングを把握する必要があるとお客様から指摘を受けています。 ゲスト OS のリリースは、通常、毎月第 2 火曜日に発生する MSRC 更新プログラムのリリースから 2 - 3 週間後に発生します。 新しいリリースには、ゲスト OS ファミリごとに関連するすべての MSRC 修正プログラムが含まれます。

Microsoft Azure では、常に更新プログラムがリリースされています。 ゲスト OS は、パイプラインにおいて唯一のこのような更新です。 リリースは、ここに記載できないほどの多く要因の影響を受ける可能性があります。 さらに、Azure は、実際には数十万台のコンピューターで実行されています。 つまり、ロールが再起動される正確な日時を指定することはできません。 Microsoft では、再起動を制限したり時間を決めたりする計画に取り組んでいます。

ゲスト OS の新しいリリースが公開されると、Azure 全体で完全に反映されるまでに時間がかかります。 サービスが新しいゲスト OS に更新されると、更新ドメインに従って再起動されます。 "自動" 更新プログラムを使用するように設定されているサービスで、まずリリースが取得されます。 更新後、Azure Portal には、ご使用のサービスの新しいゲスト OS バージョンの一覧が表示されます。 この期間に再リリースが発生する場合があります。 一部のバージョンは長期間にわたってデプロイされることがあります。また、正式なリリース日以降何週間も自動アップグレードの再起動が行われないこともあります。 ゲスト OS が使用可能になると、ポータルや構成ファイルから明示的にそのバージョンを選択できます。

再起動の関する有益な情報と、ゲスト OS とホスト OS の更新プログラムの技術的な詳細のヒントについては、MSDN の [OS のアップグレードによるロール インスタンスの再起動][restarts]に関するブログ記事を参照してください。

ゲスト OS を手動で更新する場合は、追加情報について[ゲスト OS の提供終了ポリシー][retirepolicy]のページを参照してください。

## <a name="guest-os-supportability-and-retirement-policy"></a>ゲスト OS のサポートと提供終了のポリシー
ゲスト OS のサポートと提供終了のポリシーについては、[こちら][retirepolicy]で説明されています。

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[ゲスト OS 更新 RSS フィード]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
