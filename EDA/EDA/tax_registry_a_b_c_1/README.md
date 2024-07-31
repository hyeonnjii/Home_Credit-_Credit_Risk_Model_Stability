# ✅tax_registry_a_1

## File Description

- 세금 공제(tax_deduction)에 관한 file
- 세금 공제 금액, 세금 공제 날짜, 해당 업무를 처리한 employee
- **의의**:
  - 세금 공제 금액은 고객의 소득, 세금 납부 능력을 나타낼 수 있는 지표
  - 개인의 세금 납부 패턴, 정기성을 파악하는데에 유용

## Column Description

### a_1

| Variable            | Description                                               |
| ------------------- | --------------------------------------------------------- |
| amount_4527230A     | Tax deductions amount tracked by the government registry. |
| name_4527232M       | Name of employer.                                         |
| recorddate_4527225D | Date of tax deduction record.                             |

### b_1

| Variable               | Description                                               |
| ---------------------- | --------------------------------------------------------- |
| amount_4917619A        | Tax deductions amount tracked by the government registry. |
| deductiondate_4917603D | Tax deduction date.                                       |
| name_4917606M          | Name of employer.                                         |

### c_1

| Variable            | Description                                       |
| ------------------- | ------------------------------------------------- |
| employername_160M   | Employer's name.                                  |
| pmtamount_36A       | Tax deductions amount for credit bureau payments. |
| processingdate_168D | Date when the tax deduction is processed.         |

## Feature Engineering

3가지 항목에 대해 a, b, c file을 재구성

→ colab 목차 [a / b / c 공통 유형 컬럼]

- **`tax_deduction_amount`(세금 공제 금액)**
  - `amount_4527230A` / `amount_4917619A` / `pmtamount_36A`
  - (a, c의 범위 - b의 범위) 다름
- **`tax_data`(세금 공제 날짜)**
  - `recorddate_4527225D` / `deductiondate_4917603D` / `processingdate_168D`
  - 기록 날짜, 실제 공제된 날짜, 처리 날짜 → 약간의 의미는 다르지만, 같이 봐도 무방할 것
  - a(주로 2019), b (2019-2020) - c (2018-2019)
- **`employee_name`(employee 이름)**
  - `name_4527232M` / `name_4917606M`/ `employername_160M`

---

- 합치기 전후, 분포 차이 별로 X

![Untitled](%E2%9C%85tax_registry_a_1%20043150e49523408a868460e7545e6ea2/Untitled.png)

![Untitled](%E2%9C%85tax_registry_a_1%20043150e49523408a868460e7545e6ea2/Untitled%201.png)

- 날짜 별(month 단위) tax_deduction 금액

![Untitled](%E2%9C%85tax_registry_a_1%20043150e49523408a868460e7545e6ea2/Untitled%202.png)

[실행 코드](https://colab.research.google.com/drive/1iQfVuJ53l42I5Zd478LJhd9yJyu9UUcn#scrollTo=Xx2IZOi3bf57)
