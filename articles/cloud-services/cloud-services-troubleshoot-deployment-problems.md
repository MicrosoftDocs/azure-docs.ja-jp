---
title: クラウド サービスのデプロイメントに関する問題のトラブルシューティング | Microsoft Docs
description: クラウド サービスを Azure にデプロイするときに発生しやすい問題がいくつか存在します。 この記事では、その解決方法について説明しています。
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122746"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>クラウド サービスのデプロイメントに関する問題のトラブルシューティング
クラウド サービス アプリケーション パッケージを Azure にデプロイするとき、デプロイメントに関する情報は、Azure Portal の **[プロパティ]** ウィンドウで確認できます。 クラウド サービスに関する問題は、このウィンドウの情報を頼りにトラブルシューティングすることができます。新規にサポート リクエストを行う際も、この情報を Azure サポートに提供してください。

**[プロパティ]** ウィンドウを表示する方法は次のとおりです。

* Azure Portal では、クラウド サービスのデプロイメントをクリックして、 **[すべての設定]** 、 **[プロパティ]** の順にクリックします。

> [!NOTE]
> **[プロパティ]** ウィンドウの内容は、ウィンドウの右上隅にあるアイコンをクリックしてクリップボードにコピーできます。
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>問題:デプロイメントは起動しており、すべてのロール インスタンスの準備が整っているにもかかわらず、Web サイトにアクセスできない
ポータルに表示される [Web サイトの URL] リンクにポートが含まれていません。 Web サイトの既定のポートは 80 です。 それ以外のポートでアプリケーションが構成されている場合は、Web サイトへのアクセス時に、正しいポート番号を URL に追加する必要があります。

1. Azure ポータルで、ご使用のクラウド サービスのデプロイメントをクリックします。
2. Azure Portal の **[プロパティ]** ウィンドウ ( **[入力エンドポイント]** ) で、ロール インスタンスのポートを確認します。
3. ポート番号が 80 以外であった場合、アプリケーションにアクセスするときに、正しいポート値を URL に追加してください。 既定以外のポートを指定するには、URL に続けてコロン (:) を入力し、スペースなしでポート番号を入力します。

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>問題:ロール インスタンスが勝手にリサイクルされる
問題のあるノードを Azure が検出し、ロール インスタンスを新しいノードに移すと、サービス復旧機能が自動的に作動します。 このとき、ロール インスタンスが自動的にリサイクルされることがあります。 サービス復旧機能が作動したかどうかは、次の手順で確認できます。

1. Azure ポータルで、ご使用のクラウド サービスのデプロイメントをクリックします。
2. ロールのリサイクルが観測された時間帯にサービス復旧機能が作動したかどうかを、Azure ポータルの **[プロパティ]** ウィンドウの情報を見て調べます。

ロールは、およそ月 1 回、ホスト OS とゲスト OS のアップグレード時にリサイクルされます。  
詳細については、 [OS のアップグレードに伴うロール インスタンスの再起動](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>問題:VIP スワップを実行できずエラーが発生する
デプロイメントの更新処理が行われている場合、VIP スワップが許可されません。 デプロイメントの更新処理は、次の状況で自動的に実行されます。

* 新しいゲスト オペレーティング システムが公開された場合で、かつ自動更新を構成している場合
* サービス復旧機能が作動した場合

VIP スワップを妨げている原因が自動更新であることを確認するには:

1. Azure ポータルで、ご使用のクラウド サービスのデプロイメントをクリックします。
2. Azure Portal の **[プロパティ]** ウィンドウで、 **[ステータス]** の値を確認します。 **[準備完了]** になっている場合は **[最終処理]** を見て、VIP スワップを妨げる可能性のある処理が最近実行されていないかどうかを確認します。
3. 運用環境のデプロイメントに対して手順 1. と手順 2. を繰り返します。
4. 自動更新が進行中である場合は、その完了を待って、VIP スワップを試みてください。

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>問題:ロール インスタンスの状態が [開始済み]、[初期化中]、[ビジー]、[停止] と絶えず変化する
この状態は、アプリケーションのコード、パッケージ、または構成ファイルに問題があることを示している可能性があります。 該当する場合、数分おきに [状態] が変化し、Azure Portal には、 **[リサイクル中]** 、 **[ビジー]** 、 **[初期化しています]** などと表示される場合があります。 これは、ロール インスタンスの実行を妨げる問題がアプリケーションに生じていることを示します。

この問題をトラブルシューティングする方法の詳細については、[Azure PaaS 計算診断データ](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)に関するブログ記事と「[ロールのリサイクルを引き起こす一般的な問題](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)」を参照してください。

## <a name="problem-my-application-stopped-working"></a>問題:アプリケーションが動作しなくなった
1. Azure ポータルで、ロール インスタンスをクリックします。
2. Azure ポータルの **[プロパティ]** ウィンドウで次の状況を考慮しながら、問題を解決します。
   * ロール インスタンスが最近停止した場合 ( **[中止の数]** の値を確認)、デプロイが更新中である可能性があります。 しばらく待って、ロール インスタンスの機能が自然に再開するかどうかを確認してください。
   * ロール インスタンスが **[ビジー]** 状態である場合、アプリケーション コードを見て、 [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) イベントを処理しているかどうかを確認します。 場合によっては、このイベントを処理するコードを追加するか、コードに修正を加える必要があります。
   * [Azure PaaS コンピューティング診断データ](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)に関するブログ記事で、診断データとトラブルシューティングのシナリオを参照してください。

> [!WARNING]
> クラウド サービスをリサイクルした場合、デプロイメントのプロパティはリセットされ、元の問題に関する情報は事実上失われます。
>
>

## <a name="next-steps"></a>次の手順
クラウド サービスの他の [トラブルシューティングに関する記事](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) を参照します。

Azure PaaS コンピューターの診断データを使用してクラウド サービス ロールの問題をトラブルシューティングする方法については、 [Kevin Williamson によるブログ シリーズ](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)をご覧ください。
