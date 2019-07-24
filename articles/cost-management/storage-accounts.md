---
title: Azure で Cloudy 用にストレージ アカウントを構成する | Microsoft Docs
description: この記事では、Cloudyn 用に Azure ストレージ アカウントと AWS ストレージ バケットを構成する方法について説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: f03193253bd8d8a7530d65a552a07d3901887cf5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104303"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Cloudyn 用にストレージ アカウントを構成する

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Cloudyn のレポートは、Cloudyn ポータル、Azure ストレージ、または AWS ストレージ バケットに保存できます。 Cloudyn ポータルへのレポートの保存は無料です。 一方、レポートをクラウド サービス プロバイダーのストレージに保存することもできますが、追加コストが発生します。 この記事では、Azure ストレージ アカウントとアマゾン ウェブ サービス (AWS) ストレージ バケットを構成してレポートを保存する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure ストレージ アカウントまたは Amazon ストレージ バケットが必要です。

Azure ストレージ アカウントを持っていない場合は、作成する必要があります。 Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。

AWS Simple Storage Service (S3) バケットを持っていない場合は、作成する必要があります。 S3 バケットの作成の詳細については、「[Create a Bucket (バケットの作成)](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)」を参照してください。

## <a name="configure-your-azure-storage-account"></a>Azure ストレージ アカウントの構成

Cloudyn で使用するための Azure ストレージの構成は簡単です。 ストレージ アカウントに関する詳細情報を収集し、Cloudyn ポータルにコピーします。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
2. **[すべてのサービス]** をクリックし、**[ストレージ アカウント]** を選択して、使用するストレージ アカウントまでスクロールしたら、そのアカウントを選択します。
3. [ストレージ アカウント] ページの **[設定]** で **[アクセス キー]** をクリックします。
4. [key1] の**ストレージ アカウント名**と**接続文字列**をコピーします。  
   ![ストレージ アカウント名と接続文字列をコピーする](./media/storage-accounts/azure-storage-access-keys.png)  
5. Azure portal から Cloudyn ポータルを開くか、 https://azure.cloudyn.com に移動してサインインします。
6. 歯車記号をクリックし、**[Reports Storage Management]\(レポートのストレージ管理\)** を選択します。
7. **[新規追加 +]** をクリックし、Microsoft Azure が選択されていることを確認します。 使用する Azure ストレージ アカウント名を **[名前]** ボックスに貼り付けます。 使用する**接続文字列**を対応するボックスに貼り付けます。 コンテナー名を入力し、**[保存]** をクリックします。  
   ![[Add a new report storage]\(新しいレポート ストレージの追加\) ボックスに Azure Storage アカウントの名前と接続文字列を貼り付ける](./media/storage-accounts/azure-cloudyn-storage.png)

   新しい Azure レポート ストレージのエントリがストレージ アカウントの一覧に表示されます。  
    ![新しい Azure レポート ストレージの一覧のエントリ](./media/storage-accounts/azure-storage-entry.png)


これで、レポートを Azure ストレージに保存できるようになりました。 任意のレポートで **[アクション]** をクリックし、**[レポートのスケジュール設定]** を選択します。 レポートに名前を付け、独自の URL を追加するか、自動的に作成された URL を使用します。 **[Save to storage]\(ストレージに保存\)** を選択し、ストレージ アカウントを選択します。 レポート ファイル名に付加されるプレフィックスを入力します。 ファイル形式として CSV または JSON を選択し、レポートを保存します。

## <a name="configure-an-aws-storage-bucket"></a>AWS ストレージ バケットの構成

Cloudyn では、既存の AWS 資格情報 (ユーザーまたはロール) を使用してレポートをバケットに保存します。 アクセスをテストするために、Cloudyn は、小さいテキスト ファイルを _check-bucket-permission.txt_ というファイル名でバケットに保存することを試みます。

Cloudyn のロールまたはユーザーには、バケットに対する PutObject アクセス許可を付与します。 その後、既存のバケットを使用するか新しいバケットを作成して、レポートを保存します。 最後に、ストレージ クラスの管理、ライフサイクル規則の設定、不要なファイルの削除を実行する方法を決定します。

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>AWS のユーザーまたはロールにアクセス許可を割り当てる

新しいポリシーを作成する際は、S3 バケットにレポートを保存するために必要なアクセス許可を付与します。

1. AWS コンソールにサインインし、**[Services]\(サービス\)** を選択します。
2. サービスの一覧から、**[IAM]** を選択します。
3. コンソールの左側にある **[Policies]\(ポリシー\)** を選択し、**[Create Policy]\(ポリシーの作成\)** をクリックします。
4. **[JSON]** タブをクリックします。
5. 次のポリシーを使用することで、S3 バケットにレポートを保存できます。 次のポリシーの例をコピーして、**[JSON]** タブに貼り付けます。&lt;bucketname&gt; は、実際のバケット名に置き換えます。

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. **[Review policy]\(ポリシーの確認\)** をクリックします。  
    ![情報の例を表示する AWS JSON ポリシー](./media/storage-accounts/aws-policy.png)  
7. [Review policy]\(ポリシーの確認\) ページで、ポリシーの名前を入力します。 たとえば、_CloudynSaveReport2S3_ とします。
8. **[Create policy]\(ポリシーの作成\)** をクリックします。

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>自身のアカウント内の Cloudyn のロールまたはユーザーにポリシーをアタッチする

新しいポリシーをアタッチするには、AWS コンソールを開き、Cloudyn のロールまたはユーザーを編集します。

1. AWS コンソールにサインインし、**[Services]\(サービス\)** を選択して、サービスの一覧から **[IAM]** を選択します。
2. コンソールの左側から **[Roles]\(ロール\)** または **[Users]\(ユーザー\)** を選択します。

**ロールの場合:**

  1. Cloudyn のロール名をクリックします。
  2. **[Permissions]\(アクセス許可\)** タブで **[Attach Policy]\(ポリシーのアタッチ\)** をクリックします。
  3. 作成したポリシーを検索し、それを選択して、**[Attach Policy]\(ポリシーのアタッチ\)** をクリックします。
    ![Cloudyn のロールにアタッチされたポリシーの例](./media/storage-accounts/aws-attach-policy-role.png)

**ユーザーの場合:**

1. Cloudyn ユーザーを選択します。
2. **[Permissions]\(アクセス許可\)** タブで **[Add permissions]\(アクセス許可の追加\)** をクリックします。
3. **[Grant Permission]\(アクセス許可の付与\)** セクションで、**[Attach existing policies directly]\(既存のポリシーを直接アタッチする\)** を選択します。
4. 作成したポリシーを検索し、それを選択して、**[Next: Review]\(次へ: 確認\)** をクリックします。
5. [Add permissions to role name]\(ロール名にアクセス許可を追加する\) ページで、**[Add permissions]\(アクセス許可の追加\)** をクリックします。  
    ![Cloudyn ユーザーにアタッチされたポリシーの例](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>省略可能:バケット ポリシーを使用してアクセス許可を設定する

バケット ポリシーを使用して、S3 バケットにレポートを作成するためのアクセス許可を設定することもできます。 クラシック S3 ビューで、次を実行します。

1. バケットを作成するか、既存のバケットを選択します。
2. **[Permissions]\(アクセス許可\)** タブを選択し、**[Bucket policy]\(バケット ポリシー\)** をクリックします。
3. 次のポリシーのサンプルをコピーして貼り付けます。 &lt;bucket\_name&gt; と &lt;Cloudyn\_principle&gt; を、使用するバケットの ARN に置き換えます。 Cloudyn で使用されるロールまたはユーザーの ARN を置き換えます。

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. バケット ポリシー エディターで、**[Save]\(保存\)** をクリックします。

### <a name="add-aws-report-storage-to-cloudyn"></a>AWS レポート ストレージを Cloudyn に追加する

1. Azure portal から Cloudyn ポータルを開くか、 https://azure.cloudyn.com に移動してサインインします。
2. 歯車記号をクリックし、**[Reports Storage Management]\(レポートのストレージ管理\)** を選択します。
3. **[新規追加 +]** をクリックし、AWS が選択されていることを確認します。
4. アカウントとストレージ バケットを選択します。 AWS ストレージ バケットの名前は自動的に設定されます。  
    ![[Add a new report storage]\(新しいレポート ストレージの追加\) ボックスの情報の例](./media/storage-accounts/aws-cloudyn-storage.png)  
5. **[保存]** をクリックし、**[OK]** をクリックします。

    新しい AWS レポート ストレージのエントリがストレージ アカウントの一覧に表示されます。  
    ![ストレージ アカウントの一覧に表示された新しい AWS レポート ストレージのエントリ](./media/storage-accounts/aws-storage-entry.png)


これで、レポートを Azure ストレージに保存できるようになりました。 任意のレポートで **[アクション]** をクリックし、**[レポートのスケジュール設定]** を選択します。 レポートに名前を付け、独自の URL を追加するか、自動的に作成された URL を使用します。 **[Save to storage]\(ストレージに保存\)** を選択し、ストレージ アカウントを選択します。 レポート ファイル名に付加されるプレフィックスを入力します。 ファイル形式として CSV または JSON を選択し、レポートを保存します。

## <a name="next-steps"></a>次の手順

- Cloudyn のレポートの基本的な構造と機能については、[Cloudyn のレポート](understanding-cost-reports.md)に関するページをご確認ください。
