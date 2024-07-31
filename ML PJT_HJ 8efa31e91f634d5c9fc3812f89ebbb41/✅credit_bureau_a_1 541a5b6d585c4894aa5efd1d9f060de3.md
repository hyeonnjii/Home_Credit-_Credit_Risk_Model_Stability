# credit_bureau_a_1

## File Description

- 신용 금융 기관에 기록된 데이터
- null 값이 80% +
    - isnull vs. notnull:   target 영향력 확인
        
        ⇒ eda로 결정
        
    
- masking category > null이 없음!
    - ~~null 을 encoding한 값이 들어가있는건 아닌지 확인 필요~~
    - (강사님 피드백)
    - 그냥 masking에 의미를 부여하지 않는게 중요!
    - 필요하면, 의미가 있다면 찾아보는 게 중요!!! ⇒ 모델링 자체에서 tree feature importance가 높은 컬럼, 분포가 특이하게 나오는 컬럼
- 대부분 left skewed 형태
    - log 변환 : np.log1p()
    - 음수 data 존재 컬럼 `dpdmax_757P`: (+)min 처리로 -inf 제거

## Column Description

| Variable | Description |
| --- | --- |
| annualeffectiverate_199L | Interest rate of the closed contracts. |
| annualeffectiverate_63L | Interest rate for the active contracts. |
| classificationofcontr_13M | Classificiation of the active contract. |
| classificationofcontr_400M | Classificiation of the closed contract. |
| contractst_545M | Contract status. |
| contractst_964M | Contract status of terminated credit contract. |
| contractsum_5085717L | Sum of other contract values. |
| credlmt_230A | Credit limit of the closed credit contracts from credit bureau. |
| credlmt_935A | Credit limit for active loan. |
| dateofcredend_289D | End date of an active credit contract. |
| dateofcredend_353D | End date of a closed credit contract. |
| dateofcredstart_181D | Date when the credit contract was closed. |
| dateofcredstart_739D | Start date of a closed credit contract. |
| dateofrealrepmt_138D | Date of credit's closure (contract termination date). |
| debtoutstand_525A | Outstanding amount of existing contract. |
| debtoverdue_47A | Amount that is currently past due on a client's existing credit contract. |
| description_351M | Categorization of clients by credit bureau. |
| dpdmax_139P | Maximal days past due for active contract. |
| dpdmax_757P | Maximum days past due for a closed contract. |
| dpdmaxdatemonth_442T | Max DPD occurrence month for terminated contracts from credit bureau data. |
| dpdmaxdatemonth_89T | Month when maximum days past due occurred on the active contract with the credit bureau. |
| dpdmaxdateyear_596T | Year when maximum Days Past Due (DPD) occurred for the active contract. |
| dpdmaxdateyear_896T | Year of maximum Days Past Due of closed contract obtained from Credit Bureau. |
| financialinstitution_382M | Name of financial institution that is linked to a closed contract. |
| financialinstitution_591M | Financial institution name of the active contract. |
| instlamount_768A | Instalment amount for the active contract in credit bureau. |
| instlamount_852A | Instalment amount for closed contract. |
| interestrate_508L | Interest rate for a closed contract in the credit bureau. |
| lastupdate_1112D | Date of last update for an active contract from credit bureau. |
| lastupdate_388D | Date of last update for a closed contract in the credit bureau. |
| monthlyinstlamount_332A | Monthly instalment amount for active contract. |
| monthlyinstlamount_674A | Monthly amount of instalment payment on a closed contract. |
| nominalrate_281L | Interest rate of the active contract. |
| nominalrate_498L | Interest rate for closed contract. |
| numberofcontrsvalue_258L | Number of active contracts in credit bureau. |
| numberofcontrsvalue_358L | Number of closed credit contracts. |
| numberofinstls_229L | Number of instalments on closed contract. |
| numberofinstls_320L | Number of instalments of the active contract. |
| numberofoutstandinstls_520L | Number of outstanding instalment for closed contract. |
| numberofoutstandinstls_59L | Number of outstanding instalments for the active contracts. |
| numberofoverdueinstlmax_1039L | Number of outstanding instalments for active contracts. |
| numberofoverdueinstlmax_1151L | Maximum number of past due installments for a closed contract. |
| numberofoverdueinstlmaxdat_148D | Date of maximum number of past due instalments for the closed contract. |
| numberofoverdueinstlmaxdat_641D | Date of maximum number of past due instalments for the active contract. |
| numberofoverdueinstls_725L | Maximum number of past due instalments for an active contract. |
| numberofoverdueinstls_834L | Number of past due instalments for a closed contract. |
| outstandingamount_354A | Outstanding amount for closed credit contract in credit bureau. |
| outstandingamount_362A | Active contract's outstanding amount. |
| overdueamount_31A | Past due amount for a closed contract. |
| overdueamount_659A | Past due amount for active contract. |
| overdueamountmax_155A | Maximal past due amount for active contract. |
| overdueamountmax_35A | Maximal past due amount for a closed contract. |
| overdueamountmax2_14A | Maximal past due amount for an active contract. |
| overdueamountmax2_398A | Maximal overdue amount for a closed contract. |
| overdueamountmax2date_1002D | Date of maximal past due amount for a closed contract |
| overdueamountmax2date_1142D | Date of maximal past due amount for an active contract. |
| overdueamountmaxdatemonth_284T | Month when the maximum past due amount occurred for a closed contract. |
| overdueamountmaxdatemonth_365T | Month when maximum past due amount occurred for an active contract. |
| overdueamountmaxdateyear_2T | Year when the maximum past due amount occurred for active contracts. |
| overdueamountmaxdateyear_994T | Year when maximum past due amount occurred for closed contract. |
| periodicityofpmts_1102L | Frequency of instalments for a closed contract. |
| periodicityofpmts_837L | Frequency of instalments for an active contract. |
| prolongationcount_1120L | Count of prolongations on terminated contract according to credit bureau. |
| prolongationcount_599L | Count of active contract prolongations. |
| purposeofcred_426M | Purpose of credit for active contract. |
| purposeofcred_874M | Purpose of credit on a closed contract. |
| refreshdate_3813885D | Date when the credit bureau's public sources have been last updated. |
| residualamount_488A | Residual amount of a closed contract. |
| residualamount_856A | Residual amount for the active contract. |
| subjectrole_182M | Subject role in active credit contract. |
| subjectrole_93M | Subject role in closed credit contract. |
| totalamount_6A | Total amount of closed contracts. |
| totalamount_996A | Total amount of active contracts in the credit bureau. |
| totaldebtoverduevalue_178A | Total amount of past due debt on active contracts. |
| totaldebtoverduevalue_718A | Total overdue debt amount for closed credit contracts. |
| totaloutstanddebtvalue_39A | Total outstanding debt for active contracts in the credit bureau. |
| totaloutstanddebtvalue_668A | Total outstanding debt for the closed contracts in the credit bureau. |

## Feature Engineering

- 유효이자율 vs. 명목금리
    - **`annualeffectiverate` / `nominalrate`**
    - 유효 이자율: 채권 또는 채무의 이자를 인식할 때, 그 이자율이 매년 일정하게 되도록 하는 이자수익/비용 계산방법
        
        ([https://www.investopedia.com/terms/e/effectiveinterest.asp](https://colab.research.google.com/corgiredirector?site=https%3A%2F%2Fwww.investopedia.com%2Fterms%2Fe%2Feffectiveinterest.asp))
        
    - 90% 이상이 null 값 → null vs. 기존 값
        - 실험
            
            1) null -1로 치환
            
            2) fillna →median (mean에 비해 변동성이 거의 없음)
            
            - 1.0 파일만
            
            ![Untitled](%E2%9C%85credit_bureau_a_1%20541a5b6d585c4894aa5efd1d9f060de3/Untitled.png)
            
            - 전체
            
            ![Untitled](%E2%9C%85credit_bureau_a_1%20541a5b6d585c4894aa5efd1d9f060de3/Untitled%201.png)
            
- **연체, 미지급, 잔여 구분**
    - outstanding(미납) vs. overdue(연체)
        - outstanding: 원래 예정된 지불 기한을 넘어서 미지급된 금액
        - overdue: 대출의 만기일을 이미 지난 채 지불되지 않은 금액. 즉, 기한 내에 지불되지 않은 연체된 금액 --> outstanding 금액이 지불 기한을 초과하여 미지불 상태로 남아 있으면 이후에는 overdue amount로 간주
            
            > cf) 대출 약정에서 overdue 금액이 적용되는 기준은 일반적으로 대출 계약서에 명시, 대부분의 경우, 지불 기한을 넘긴 경우 발생하는 overdue 금액에 대한 세부 내용은 대출 약정서에 기재되어 있으며, 그 안에는 연체 이자율, 유예 기간, overdue 금액이 발생하는 상황 등이 포함
            > 
        - 지불 기한을 넘긴 후부터 overdue interest(연체이자) 역시 발생하게 됨
    - residual
    
    > 궁금증
    > 
    > - outstanding의 범위:
    >     - 1) the total amount that is currently due and has not been paid. (past due + current due)
    >     - 2) Just past due

- **active vs. closed contract**
    - 실험 예정(오늘 안)

[실행 코드](https://colab.research.google.com/drive/1HXSFdodrV3v1_tGe6-z0L5AVZ8ZvmDBE#scrollTo=sWOz6s3-iS9s&uniqifier=2)
