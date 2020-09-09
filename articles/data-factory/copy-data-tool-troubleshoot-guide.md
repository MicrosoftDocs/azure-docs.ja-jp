---
title: Azure Data Factory のデータのコピー ツールに関するトラブルシューティング
description: Azure Data Factory でのデータのコピー ツールに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388312"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Azure Data Factory のデータのコピー ツールに関するトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のデータのコピー ツールの一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>エラー コード:Unable to validate in Copy Data tool (データのコピー ツールで検証できません)

- **現象**:データのコピー ツールの最初のステップで、"Unable to Validate (検証できません)" という警告メッセージが表示されます。
- **原因**:これは、すべてのサード パーティの cookie が無効にされている場合に発生する可能性があります。
- **解決方法**: 
    - Internet Explorer または Microsoft Edge ブラウザーを使用してください。
    - Chrome ブラウザーを使用している場合は、次の手順に従って *microsoftonline.com* と *windows.net* を Cookie の例外として追加します。
        1.  Chrome ブラウザーを開きます。
        2.  右側にあるレンチまたは 3 本の線をクリックします (Google Chrome をカスタマイズおよび制御します)。
        3.  **[設定]** をクリックします。
        4.  **Cookie** を検索するか、[Advanced Settings] の **[Privacy]** にアクセスします。
        5.  **[Content Settings]** を選択します。    
        6.  Cookie には、 **[allow local data to be set (recommended)]** を設定する必要があります。
        7.  **[Manage exceptions]** をクリックします。 **[hostname pattern]** に次の文字列を入力し、 **[Allow]** が動作セットであることを確認します。
            - login.microsoftonline.com
            - login.windows.net
        8.  ブラウザーを閉じて再起動します。
    - Firefox ブラウザーを使用している場合は、次の手順に従って Cookie の例外を追加してください。
        1. Firefox メニューから、 **[Tools]**  >  **[Options]** にアクセスします。
        2. **[Privacy]**  >  **[History]** で、現在の設定が **[Use Custom settings for history]** になっている可能性があります。
        3. **[Accept third-party cookie]** で、現在の設定が **[Never]** である場合があります。その場合は、右側にある **[Exceptions]** をクリックして、次のサイトを追加します。
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  ブラウザーを閉じて再起動します。 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>エラー コード:Unable to open login page and enter password (ログイン ページを開けず、パスワードを入力できません)

- **現象**:データのコピー ツールによってログイン ページにリダイレクトされますが、ログイン ページが正常に表示されません。
- **原因**:この問題は、ネットワーク環境をオフィス ネットワークからホーム ネットワークに変更した場合に発生する可能性があります。 ブラウザーにはキャッシュがあります。 
- **解決方法**: 
    1.  ブラウザーを閉じて、もう一度やり直してください。 問題がまだ発生する場合は、次の手順に進みます。   
    2.  Internet Explorer ブラウザーを使用している場合は、プライベート モードで開いてみてください (Ctrl + Shift + P キーを押します)。 Chrome ブラウザーを使用している場合は、シークレット モードで開いてみてください (Ctrl + Shift + N キーを押します)。 問題がまだ発生する場合は、次の手順に進みます。 
    3.  別のブラウザーを使用してみてください。 


## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。
*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [ADF マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
