## Overview
This project is trying to get all the loans of a borrower

## Packages:
```
import pandas as pd
import numpy as np
from collections import Counter
```

## Infrastructure & Code
* **Overall** : DataFrame -> Array -> Value -> DataFrame
1. **Clean Data:**
* First, get the number of loans each borrower is holding;
```
loancount = Counter(loan['BwrPersonId'])
loanc = pd.DataFrame.from_dict(loancount, orient='index').reset_index()
loanc.rename(columns = {'index': 'BwrPersonId', 0: 'loann'}, inplace=True)
loan = loan.merge(loanc)
```
* Second, give each loan of the borrower a sequence number
```
loan['loanx'] = 0
for i in range(0,len(loan)):
    if i > 0:
        if loan['BwrPersonId'][i] == loan['BwrPersonId'][i-1]:
            loan['loanx'][i] = loan['loanx'][i-1] + 1
            print(i,loan['loanx'][i])
        else:
            loan['loanx'][i] = 1
    else:
        loan['loanx'][i] = 1
 ```
 2. **Build the table (DataFrame) of borrowers & their loans & loan status**
 BwrPersonId | Loan 1-n | Status 1-n
------------ | -------------
```
grouped = pd.DataFrame()
unique = loan['BwrPersonId'].unique()
grouped['BwrPersonId'] = ''*len(loan['BwrPersonId'].unique())
grouped['Loan1'] = ''
grouped['Loan2'] = ''
grouped['Loan3'] = ''
grouped['Loan4'] = ''
grouped['Loan5'] = ''
grouped['Status1'] = ''
grouped['Status2'] = ''
grouped['Status3'] = ''
grouped['Status4'] = ''
grouped['Status5'] = ''
```
3. **Get the loan IDs from Table `loan` (df -> array -> values) -> give the value to cell in Table `grouped` (values -> df)**
```
grouped.iloc[:,0] = unique
for i in grouped.index:
    length = loan.loc[loan['BwrPersonId'] == grouped['BwrPersonId'][i], 'loann'].unique()[0]
    for j in range(1,length+1):
        grouped.iloc[i,j] = loan.loc[(loan['BwrPersonId'] == grouped['BwrPersonId'][i]) & (loan['loanx'] == j), 'LoanId'].values[0]
        grouped.iloc[i,j+5] = loan.loc[(loan['BwrPersonId'] == grouped['BwrPersonId'][i]) & (loan['loanx'] == j), 'LoanStatusDescription1'].values[0]
```
4. **Merge Table `grouped` and `loan`**
```
result = pd.merge(grouped, loan, how='left', left_on='BwrPersonId',right_on='BwrPersonId').drop(['LoanId','SchoolStudentId','LoanStatusDescription1','loann','loanx'],axis=1)
result = result.drop_duplicates(subset=['BwrPersonId'])
```
5. **Read to csv**
```
outfile = 'Borrower List/loan2.csv'
result.to_csv(outfile)
```

## Notes
1. Only `value` can be given to cell in df; ** Be careful of the data type.**
eg.
```
        grouped.iloc[i,j+5] = loan.loc[(loan['BwrPersonId'] == grouped['BwrPersonId'][i]) & (loan['loanx'] == j), 'LoanStatusDescription1'].values[0]
```
2. **Be careful of the scalar**. Don't waste time repeating the mistake!!!
```
loan['BwrPersonId'][i] == loan['BwrPersonId'][i-1]
```

## Functions
1. Counter() -> from_dict (**pandas**) :switch from dict to df
2. **pandas.df:** 
rename(columns = {'column1':'XXX';'c2': 'yyy'}); <br />
reset_index(); <br />
sort_values('column'); <br />
unique() -> the unique values in column (return array); <br />
values() -> the values in column (return array); <br />
drop_duplicates() -> drop duplicates in column; <br />

## Things that can improve
1. Iterable list: 
```
loan['loanx'] = 0
for i in range(0,len(loan)):
    if i > 0:
        if loan['BwrPersonId'][i] == loan['BwrPersonId'][i-1]:
            loan['loanx'][i] = loan['loanx'][i-1] + 1
            print(i,loan['loanx'][i])
        else:
            loan['loanx'][i] = 1
    else:
        loan['loanx'][i] = 1
 ```
