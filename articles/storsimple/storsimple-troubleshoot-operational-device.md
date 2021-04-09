---
title: デプロイ済みの StorSimple デバイスのトラブルシューティング | Microsoft Docs
description: デプロイし、現在運用している StorSimple デバイスに発生するエラーを診断して修正する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6cd1d981737db1e7c852931ecc2449e0afc03530
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956791"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>StorSimple 運用デバイスのトラブルシューティング
> [!NOTE]
> StorSimple のクラシック ポータルは非推奨となる予定です。 ご使用の StorSimple デバイス マネージャーは、廃止スケジュールに従い、自動的に新しい Azure Portal に移行されます。 この移行に関しては、メールとポータル通知でお知らせします。 このドキュメントも間もなく廃止されます。 この移行についてご質問があれば、[Azure Portal への移行に関する FAQ](./index.yml) のページを参照してください。

## <a name="overview"></a>概要
この記事は、デプロイした StorSimple デバイスの運用開始後に発生する可能性のある構成の問題を解決するための有益なトラブルシューティング ガイダンスです。 Microsoft Azure StorSimple の利用時に起こることがある問題の解決に役立つ、一般的な問題、考えられる原因、推奨手順について説明します。 この情報は、StorSimple のオンプレミスの物理デバイスと StorSimple 仮想デバイスの両方に当てはまります。

この記事の最後には、Microsoft Azure StorSimple の操作中に発生する可能性のあるエラー コードの一覧と、エラーを解決するための手順が示されています。 

## <a name="setup-wizard-process-for-operational-devices"></a>運用デバイスのセットアップ ウィザードのプロセス
セットアップ ウィザード ([Invoke-HcsSetupWizard][1]) を使用して、デバイスの構成を確認し、必要に応じて是正措置を実施します。

以前に構成し、運用しているデバイスでセットアップ ウィザードを実行する場合、プロセス フローが異なります。 次のエントリのみを変更することができます。

* IP アドレス、サブネット マスク、ゲートウェイ
* プライマリ DNS サーバー
* プライマリ NTP サーバー
* Web プロキシ構成 (省略可能)

セットアップ ウィザードでは、パスワードの収集やデバイスの登録に関連する操作は実行されません。

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>セットアップ ウィザードを 2 回以上実行したときに発生するエラー
次の表では、運用しているデバイスでセットアップ ウィザードを実行した場合に発生する可能性のあるエラー、エラーの考えられる原因、エラーを解決するための推奨される操作について説明します。 

| いいえ。 | エラー メッセージまたは条件 | 考えられる原因 | 推奨される操作 |
|:--- |:--- |:--- |:--- |
| 1 |エラー 350032: このデバイスは既に非アクティブ化されています。 |非アクティブ化されたデバイスでセットアップ ウィザードを実行すると、このエラーが表示されます。 |[Microsoft サポート](./storsimple-8000-contact-microsoft-support.md) に対処法をお問い合わせください。 非アクティブ化されているデバイスを利用することはできません。 デバイスを再アクティブ化する前に、出荷時の設定に戻す必要があります。 |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION (HRESULT からの例外: 0x80070001) |DNS サーバーの更新に失敗しました。 DNS の設定はグローバル設定であるため、有効なすべてのネットワーク インターフェイスに適用されます。 |インターフェイスを有効にし、再度、DNS の設定を適用します。 これらの設定はグローバルであるため、有効になっている他のインターフェイスのネットワークが中断される可能性があります。 |
| 3 |StorSimple Manager サービスのポータルではデバイスがオンラインであると表示されますが、最小のセットアップを完了して構成を保存しようとすると、操作に失敗します。 |実際のプロキシ サーバーがあるにもかかわらず、初期セットアップ時に、Web プロキシが構成されませんでした。 |[Test-HcsmConnection コマンドレット][2]を使用して、エラーを特定します。 [Microsoft サポート](./storsimple-8000-contact-microsoft-support.md) ください。 |
| 4 |Invoke-HcsSetupWizard: 値が期待される範囲内にありません。 |不適切なサブネット マスクがこのエラーの原因です。 考えられる原因: <ul><li> サブネット マスクが存在しないか空である。</li><li>Ipv6 プレフィックスの形式が正しくない。</li><li>インターフェイスはクラウドに対応しているが、ゲートウェイが見つからないか、正しくない。</li></ul>DATA 0 は、セットアップ ウィザードを使用して構成した場合は自動的にクラウド対応になります。 |問題を特定するには、サブネット 0.0.0.0 または 256.256.256.256 を使用して出力を確認します。 必要に応じて、サブネット マスク、ゲートウェイ、および IPv6 プレフィックスの正しい値を入力します。 |

## <a name="error-codes"></a>エラー コード
エラーは番号順に示されています。

| エラー番号 | エラー テキストまたは説明 | 推奨されるユーザー アクション |
|:--- |:--- |:--- |
| 10502 |ストレージ アカウントへのアクセス中にエラーが発生しました。 |数分待ってから操作をやり直してください。 引き続きエラーが発生する場合は、Microsoft サポートに対処法をお問い合わせください。 |
| 40017 |バックアップ ポリシーで指定されたボリュームがデバイスで見つからないため、バックアップ操作に失敗しました。 |この操作をやり直しても問題が引き続き発生する場合は、Microsoft サポートに 次のステップについて問い合わせてください。 |
| 40018 |バックアップ ポリシーで指定されたどのボリュームもデバイスで見つからないため、バックアップ操作に失敗しました。 |この操作をやり直しても問題が引き続き発生する場合は、Microsoft サポートに 次のステップについて問い合わせてください。 |
| 390061 |システムは、ビジー状態か利用できない状態です。 |数分待ってから操作をやり直してください。 引き続きエラーが発生する場合は、Microsoft サポートに対処法をお問い合わせください。 |
| 390143 |エラー コード 390143 でエラーが発生しました  (不明なエラー)。 |引き続きエラーが発生する場合は、Microsoft サポートに対処法をお問い合わせください。 |

## <a name="next-steps"></a>次のステップ
問題を解決できない場合は、 [Microsoft サポートにお問い合わせ](./storsimple-8000-contact-microsoft-support.md) ください。 

[1]: /previous-versions/windows/powershell-scripting/dn688135(v=wps.630)
[2]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)