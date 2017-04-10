この条件は、それぞれの新しい Salesforce の潜在顧客の電子メール アドレス フィールドを評価します。 電子メール アドレスに "*amazon.com*" が含まれている場合、条件の結果は *True* になります。

1. **[+ New step (+ 新しいステップ)]**を選択します。  
   ![Salesforce 条件イメージ 1](./media/connectors-create-api-salesforce/condition-1.png)   
2. **[条件の追加]**を選択します。    
   ![Salesforce 条件イメージ 2](./media/connectors-create-api-salesforce/condition-2.png)  
3. **[値の選択]**を選択します。    
   ![Salesforce 条件イメージ 3](./media/connectors-create-api-salesforce/condition-3.png)  
4. トリガーの潜在顧客から *[電子メール]* トークンを選択します。    
   ![Salesforce 条件イメージ 4](./media/connectors-create-api-salesforce/condition-4.png)  
5. *[を含む]*を選択します。      
   ![Salesforce 条件イメージ 5](./media/connectors-create-api-salesforce/condition-5.png)  
6. コントロールの下部にある **[値の選択]** を選択します。     
   ![Salesforce 条件イメージ 6](./media/connectors-create-api-salesforce/condition-6.png)  
7. 潜在顧客の新しい電子メール アドレスを評価する値として、「 *amazon.com* 」を入力します。 電子メール アドレスに "*amazon.com*" が含まれている場合、条件は *True* と評価され、ロジック アプリの他の手順に進むことができます。    
   ![Salesforce 条件イメージ 7](./media/connectors-create-api-salesforce/condition-7.png)  
8. ロジック アプリを保存する。  



<!--HONumber=Nov16_HO3-->


