## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus トピックとサブスクリプションとは
Service Bus のトピックとサブスクリプションは、メッセージ通信の *発行/サブスクライブ* モデルをサポートします。 トピックとサブスクリプションを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすトピックを介してメッセージをやり取りすることになります。

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

各メッセージが 1 つのコンシューマーによって処理される Service Bus キューとは異なり、トピックとサブスクリプションでは、発行/サブスクライブ パターンを使用した "1 対多" 形式の通信を行います。 複数のサブスクリプションを 1 つのトピックに登録できます。 トピックに送信されたメッセージはサブスクリプションに渡され、各サブスクリプションで独立して処理できます。

トピックにとってのサブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 トピックに対するフィルター ルールをサブスクリプション単位で登録することもできます。これを使用すると、トピックに送信されるどのメッセージをどのトピック サブスクリプションで受信するかをフィルター処理または制限することができます。

Service Bus のトピックとサブスクリプションを使用することで、多くのユーザーやアプリケーションの間でやり取りされる膨大な数のメッセージを処理することもできます。

## <a name="create-a-namespace"></a>名前空間の作成
Azure で Service Bus のトピックとサブスクリプションの使用を開始するには、最初に *サービス名前空間*を作成する必要があります。 名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

名前空間を作成するには:

1. [Azure Portal][Azure portal] にログオンします。
2. ポータルの左側のナビゲーション ウィンドウで、**[新規]** をクリックし、**[Enterprise Integration]**、**[Service Bus]** の順にクリックします。
3. **[名前空間の作成]** ダイアログで、名前空間の名前を入力します。 その名前が使用できるかどうかがすぐに自動で確認されます。
4. 入力した名前空間の名前が使用できることを確認したら、価格レベル (Basic、Standard、Premium) を選択します。
5. **[サブスクリプション]** フィールドで、名前空間を作成する Azure サブスクリプションを選択します。
6. **[リソース グループ]** フィールドで、名前空間を追加する既存のリソース グループを選択するか、新しいリソース グループを作成します。      
7. **[場所]**で、名前空間をホストする国またはリージョンを選択します。
   
    ![[名前空間の作成]][create-namespace]
8. **[Create]** ボタンをクリックします。 これで、システムによってサービス名前空間が作成され、有効になります。 システムがアカウントのリソースを準備し 終わるまでに、数分間かかる場合があります。

### <a name="obtain-the-credentials"></a>資格情報を取得する
1. 名前空間の一覧で、新しく作成した名前空間の名前をクリックします。
2. **[Service Bus 名前空間]** ブレードで、**[共有アクセス ポリシー]** をクリックします。
3. **[共有アクセス ポリシー]** ブレードで、**[RootManageSharedAccessKey]** をクリックします。
   
    ![connection-info][connection-info]
4. **[ポリシー: RootManageSharedAccessKey]** ブレードで、**[接続文字列 - プライマリ キー]** の横にあるコピー ボタンをクリックし、後で使用するために接続文字列をクリップボードにコピーします。
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png




<!--HONumber=Jan17_HO1-->


