# ✅other_1

## **File Description**

- **debit card, deposit 에 관한 신용 거래 정보 file**
- **null X**
- **case_id 모두 unique → 각 고객에 대한 ID(다른 파일에서는?)**

## **Column Description**

| Variable                    | Description                                        |
| --------------------------- | -------------------------------------------------- |
| amtdebitincoming_4809443A   | Incoming debit card transactions amount.           |
| amtdebitoutgoing_4809440A   | Outgoing debit card transactions amount.           |
| amtdepositbalance_4809441A  | Deposit balance of client.                         |
| amtdepositincoming_4809444A | Amount of incoming deposits to client's account.   |
| amtdepositoutgoing_4809442A | Amount of outgoing deposits from client's account. |

## **Feature Engineering**

<aside>
💡 **`incoming_amount` : debit card로 들어오거나, 혹은 account에서 바로 입금되는 금액의 총합
`outgoing_amount` : debit card로 나가거나, 혹은 account에서 바로 출금되는 금액의 총합**

</aside>

- **incoming, outgoing 개념이 분리되어 있으나, 하나로 합쳐 계산해보기**
- **모든 컬럼의 null 값이 존재하지 않음 → 0과 positive 비율이 대략적으로 절반이 대부분(amtdepositincoming_4809444A 제외)**

| Columns                     | (null, 0, positive) | zero_rate | positive_rate |
| --------------------------- | ------------------- | --------- | ------------- |
| amtdebitincoming_4809443A   | (0, 27053, 24056)   | 0.529320  | 0.470680      |
| amtdebitoutgoing_4809440A   | (0, 27286, 23823)   | 0.533879  | 0.466121      |
| amtdepositbalance_4809441A  | (0, 32235, 18873)   | 0.630723  | 0.369277      |
| amtdepositincoming_4809444A | (0, 45985, 5124)    | 0.899744  | 0.100256      |
| amtdepositoutgoing_4809442A | (0, 22433, 28676)   | 0.438925  | 0.561075      |

**⇒ zero와 positive 의 이분법적으로 접근**

- **incoming_amount**
  ```
  ========== [incoming_amount] ==========
  데이터 타입: float64
  NaN값: False, 0.0
  (null, 0, positive) = (0, 23860, 27249)
  (min, max): (0.0, 4957852.0)
  평균값: 10502.297585610539
  중간값: 385.4
  =======================================
  ```
  ![Untitled](%E2%9C%85other_1%20bc91e53cb6274fe7aa3df07661f3a239/Untitled.png)
- **outgoing_amount**
  ```
  ========== [outgoing_amount] ==========
  데이터 타입: float64
  NaN값: False, 0.0
  (null, 0, positive) = (0, 3508, 47601)
  (min, max): (0.0, 5168004.5)
  평균값: 11049.259396074316
  중간값: 656.8
  =======================================
  ```
  ![Untitled](%E2%9C%85other_1%20bc91e53cb6274fe7aa3df07661f3a239/Untitled%201.png)

[실행코드](https://colab.research.google.com/drive/1fuTBgwvs2pbcpzeghNVnPVWrFcBahTb3#scrollTo=Kyyt0tnvbxWG)
