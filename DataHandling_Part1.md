# Data Handling Part. 1



### DataFrame 의 기초 통계

```python
df.shape # 데이터 형태
df.info()  # 데이터 정보
df.describe()  # 기초 통계량
```

### DataFrame Head/Tail

```python
transaction.head(n)
transaction.tail(n)
```

### Column 이름 재설정

```python
# 주어진 데이터의 cloumns 명을 바꾸고 싶다면, 
# .cloumns 에 원하는 컬럼명이 담긴 리스트를 넣어줘라... 개수는 일치해야한다.
df.columns = ["date", "amount", "result"]  
---------------------------------------------------------------------------------
# 두번째 방법
df.rename(columns={df.columns[0] : '새 컬럼 이름'}, inplace=True )
```

### Column 순서 정렬하기 & Column 이름 통일성있게 모두 재설정

```python
# 원하는 순서로 컬럼명을 다 써주면 된다. 대괄호 두개!
transaction_setup= transaction[['row_uuid','app_package_name','user_id','event_datetime','event_datetime_year','event_datetime_month','event_datetime_day','event_datetime_hour','event_datetime_minute','event_datetime_second','device_manufacturer','device_type','os_type','os_version','app_version','event_category','view_category','view_id','view_action','in_app_event_category','in_app_event_label','source_type','channel','params_campaign','params_medium','params_term','is_first_activity','is_first_goal_activity']]
transaction_setup.head()

# 컬럼명 재설정
raw_log.columns = ['event_category','is_first_activity','app_package_name','app_version','device_type','source_type','params_campaign','params_medium','params_term','in_app_event_category','in_app_event_label','event_datetime','row_uuid','is_first_goal_activity','user_id','data','event_datetime_year','event_datetime_month','event_datetime_day','event_datetime_hour','event_datetime_minute','event_datetime_second','os_type','os_version','device_manufacturer','channel','view_category','view_id','view_action']
```

### 특정 Column의 특정 값을 변경하기 replace('바꿀내용','바뀔내용')

- 컬럼에 전체로 적용할때는 반드시 replace 앞에 .str 이 있어야 한다.

- 이경우 "" 안에는 정규식이 적용된다. 이것을 빼려면 옵션 설정을 해주어야하는데 자세한것은 구글링

  -> 보통은 함수를 만들어 apply로 해준다. apply를 주로 사용해라

```python
df['Sex'] = df['Sex'].str.replace(['female','male'],['woman','man'])
---------------------------------------------------------------------------
# 공백 제거 ... 
# 여러개일 경우 replace가 아니라 isin을 사용한다!!!
other_data = data[~data['컬럼명'].isin([" ",""])]
```

### 특정 데이터 타입 컬럼만 추출 .select_dtypes(include='', exclude='')

```python
# 수치형 변수들만 추출
df2 = df.select_dtypes(exclude = object)

#수치형 변수들 컬럼만 출력
df.select_dtypes(exclude = object).columns
```

### 정렬하기 sort_values()

```python
-----------------------------------------------------------------------------
# index로 정렬...  
# 숫자는 0~9  알파벳은 A ~ Z 순서로 정렬, 대문자가 소문자 앞, 반대로 정렬하고 싶다면 ascending = False 로 하면 된다.
transaction.sort_index()
transaction.sort_index(ascending=False)
-----------------------------------------------------------------------------
# column 으로 정렬...  axis 옵션을 1로 주면 cloumn을 기준으로 정렬. axis의 Defualt는 0 이다
transaction.sort_index(axis=1, ascending=False)
-----------------------------------------------------------------------------
# values 로 정렬 ...  by 옵션에 기준 column명을 넣어라
transaction.sort_values(by="amount", ascending=False)
transaction.sort_values(by = ["date", "amount"], ascending=False) # date컬럼값이 높은 순으로 정렬 후, date 컬럼값이 동일하면 amount 높은 순으로 정렬
transaction.sort_values(by = ["date", "amount"], ascending = [True, False]) # date는 오름차순, amount는 내림차순
```

### DataFrame  기본 연산, 통계, 기초통계량 등

- skipna = True로 하면 missing values 를 skip

- axis=0 로 하면 행 방향으로 연산을 한다.

  axis=1 이면 열 방향으로 연산을 한다.

```python
# mean()
mean_amount = transaction["amount"].mean()
mean_amount = transaction["amount"].mean(axis=0) # 모든 컬럼에 대한 평균을 구한다
-----------------------------------------------------------------------------
# min()
min_amount = transaction["amount"].min()
-----------------------------------------------------------------------------
# max()
max_amount = transaction["amount"].max()
-----------------------------------------------------------------------------
# sum(skipna=True)   skipna = True로 하면 missing values 를 skip
-----------------------------------------------------------------------------
# 4분위수, 기초 통계...   평균, 표준편차, 사분위수, 최소, 최대 
transaction["amount"].describe()
transaction["amount"].describe(percentiles = .10, .30, .65, .80)
-----------------------------------------------------------------------------
# count 갯수 측정
transaction["amount"].count()
-----------------------------------------------------------------------------
# 중앙값 
median_amount = transaction["amount"].median()
-----------------------------------------------------------------------------
# mode
mode_amount = transaction["amount"].mode()
-----------------------------------------------------------------------------
# 그 외에도 표준편차standard deviation 등 왠만한 수학 연산을 모두 지원함
-----------------------------------------------------------------------------
# 최소 최댓 값의 인덱스
.idxmin()
.idxmax()
-----------------------------------------------------------------------------
# 누적합
.cumsum()
```

### 분위수 추출 .quantile()

```python
# 최소값 추출
naver_data['노출수'].quantile(0)    # .min() 와 같다
-----------------------------------------------------------------------------
# 최대값 추출
naver_data['노출수'].quantile(1)    # .max() 와 같다
-----------------------------------------------------------------------------
# 95 배분위수 이상의 데이터 추출
new_data = my_data[ my_data['컬럼명'] >= my_data['컬럼명'].quantile(0.95) ]
```

### 고유값들 확인 .unique()

```python
transaction["result"].unique()   ## 중복값 제거하여 결과적으로 컬럼 값의 종류가 나옴

len(transaction["result"].unique())   ## 고유값들의 숫자를 알려준다

# 고윳값의 개수를 알려주는 nunique()
df2['읍면동명'].nunique()
>>>
41
```

### 각 값의 갯수 구하기 .value_counts()

```python
transaction["result"].value_counts() 
# 해당 컬럼을 이루는 값들의 갯수를 구할 수 있다.
```

- value_counts() 이후에 각 값들의 비율 구하기

  value_counts() 이후 결과는 Series 형태이며, 각 값들의 빈도수가 구해진다.

  이를 Series 연산을 이용해 비율 구하기 가능

```python
result = transaction["result"].value_counts()
prop = result / result.sum()   #각 값들의 비율이 Series 형태로 제공됨
prop.name = 'Series 컬럼 이름 설정'
# 위 결과들을 모아서 concat 으로 붙이면 깔끔한 표 만들기 가능
```

### 결측치 확인 isnull()

```python
naver_data.isnull().sum()
dataframe[dataframe['Age'].isnull()].head(2)
# 다음과 같이 코드를 짜면 DataFrame 형태로 보기 좋게 제시됨.
```

### 결측치 채우기 fillna()

```python
# 한 값으로 채우기
my_data['컬럼명'].fillna(0)
-----------------------------------------------------------------------------
# 딕셔너리를 이용해서 채우기
dict = {'a' : 0, 'b': 1, 'c': -9999}  # 'a', 'b', 'c' 는 컬럼명
df.fillna(dict)
-----------------------------------------------------------------------------
# ffill, bfill 옵션 활용하여 앞에 있는값 뒤에 있는 값으로 대체하기
df.fillna(method='ffill')
df.fillna(method='bfill')
-----------------------------------------------------------------------------
# limit 옵션 활용하기 ..  몇번째 값까지 채워줄지 설정
df.fillna(0, limit=2)
```

### 결측치 제거

- dropna()

```python
### NaN 이 들어간 행 전체 제거
retail.dropna(axis=0, subset = ['컬럼명'] ,inplace=True)

# 모든 데이터 값이 NaN 인 행만 제거
df.dropna(how='all')
```

- pd.notnull()

```python
retail = retail[pd.notnull(retail['CustomerID'])]
len(retail)
retail = retail[retail['CustomerID'].notnull()]
```

### 반올림 하기 round()

```python
clk = round(naver_data['클릭수'], 0)

# 후에 astype로 정수형으로 바꿔주면 소숫점을 잘라낼 수 있다
```

### 

## **DataFrame 행렬 접근, 수정, 추가**

### 특정 Column 참조

- 데이터 프레임은 ndarray 와 다르게 대괄호 안에는 인덱스가 아니라 컬럼명이 와야한다.!!!

```python
# Column 접근하기.. 대괄호 안에 컬럼명 
transaction["date"] 
transaction["date"].head()
# 타입은 Series
type(transaction["date"]) 
------------------------------------------------------------------------------
# 여러 개 컬럼 접근, 대괄호 두개 안에 컬럼명.. 타입은 DataFrame
transaction[["date", "amount"]]
# 또는 변수에 리스트로 컬럼을 넣고 접근 가능..
columns = ["date", "amount"]
transaction[columns]
```

### 특정 column 만 다른 데이터프레임으로 추출

- 이 때, 대괄호가 두개여야 데이터프레임으로 유지된다. 한개면 Series !!!!!

```python
machin2 = df[['호선']]
machin2.info()
```

### 특정 Row 참조

- Seires 는 [] 안에 인덱스를 넣어 row 선택이 가능하지만 DataFrame은 column이 선택됨
- 하지만 slicing 으로는 row 선택이 가능

```python
# Slicing 문법을 이용해 row 를 여러 개 참조 가능.. 
transaction["Kang":"Choi"] 
# Slicing 에서 주의할점!!! DataFrame은 대괄호안에 인덱스 숫자를 넣을 경우 row 참조가 안되지만
# 슬라이싱으로 넣을 경우 참조가 된다. slicing은 예외!
transaction[:10] # column 참조가됨 주의하자!!
```

- 그 외에는 .loc .iloc 로 선택 가능 .loc 는 인덱스 자체를 사용 .iloc 는 0 based index 사용 이 두함수는 , 를 사용하여 column 선택도 가능

### 인덱스 재설정 .reset_index()

```python
df.reset_index(drop=True, inplace=True)
# drop 은 기존의 인덱스가 새로운 컬럼이 되는 것을 제거하는 것
```

### .loc (), .iloc()

```python
# 위에와 차이점은 .loc (locate의 약자)라는 문법을 사용,  대괄호 안에 컬럼명이 아닌 인덱스(index)
transaction.loc["Kang"]  # type은 Series

# 여러개 접근할 경우 .loc[] 안에 인덱스가 여러 개 담긴 리스트를 넣어준다.
transaction.loc[["Kang", "Kim", "Choi"]]   # type은 DataFrame

# 변수에 인덱스의 리스트를 저장한 후 참조 가능
names = ["Kang", "Kim", "Choi"] 
transaction.loc[names] 
-------------------------------------------------------------------------------

# 행렬을 순서대로 접근하기... index나 컬럼명이 아닌 순서대로 접근하는 것이 가능
# 이 때는 .iloc 를 사용

# 첫 번째 row 참조
transaction.iloc[0] 

# column도 순서대로 접근 가능
transaction.iloc[0, 0]   # 첫번째 row, 첫번째 column

# 하나가 아닌 여러 개 접근하기...  행을 고르고 열을 고른다... 총 6개 
transaction.iloc[[0, 1, 3], [1, 2]] 

# .at 과 동일하게 .iat 이 있고 순서를 기준으로 접근한다.. 속도가 빠르다..
transaction.iat[0, 0]

# loc, iloc 두함수는 , 를 사용하여 column 선택도 가능.. 밑에 자세한 설명
transaction.loc[ ['Kang','Choi'], ['date', 'count'] ]
transaction.iloc[ [2,3], [1,3,5] ]
-------------------------------------------------------------------------------

# 행(row) 열(column) 동시에 접근하는 방법 세가지

# 첫번째, .loc[] 문법으로 행(row)을 먼저 접근한 뒤, 그 결과에서 열(column)을 바로 접근
# .loc["index이름", "column이름"]
transaction.loc["Kang", "date"] 

# 두번째 .at 이라는 문법도 존재하는데, 이 문법은 .loc 보다 속도가 빠르다는 장점
transaction.at["Kang", "date"]
# 하지만 접근하려는 행(row)이 여러 개인 경우 .at 은 동작하지 않는다.
transaction.at[["Kang", "Kim", "Choi"], "date"]
# .loc 를 사용해야함
transaction.loc[["Kang", "Kim", "Choi"], "date"]

# 컬럼이 여러 개인 경우에도 .at은 동작하지 않음 .loc를 사용해야함
# transaction.at["Kang", ["date", "amount"]]  ## 에러 발생함
transaction.loc["Kang", ["date", "amount"]] 
# 행과 열을 동시에 여러 개 접근할 때에도 .at 이 아닌 .loc를 사용한다.
transaction.loc[["Kang", "Kim", "Choi"], ["date", "amount"]] 

# 변수에 리스트 할당해서 사용 가능
names = ["Kang", "Kim", "Choi"] 
columns = ["date", "amount"] 
transaction.loc[names, columns]
```

### 조건에 맞게 특정 column 특정 값을 변경하기

```python
# df2['지연여부'][df2['지연여부'] == 'N'] = 0  는 틀리다!!
df2.loc[df2['지연여부'] == 'N', '지연여부'] = 0  # 이 방법이 맞다
```

### 색인(Indexing) ... 데이터를 검색하거나 색인

- 파이썬의 비교 연산자   == , != , > , < , >= , <=

```python
# 비교 연산자 응용하여, 특정 속성 특정 값 존재 여부 확인
transaction[transaction["date"] == "2017-01-01"] 

# 아닌 게 존재하는지 여부 확인도 가능
transaction[transaction["date"] != "2017-01-01"] 

# 부등호의 조건 만족하는 데이터도 색인 가능
transaction[transaction["amount"] < 600] 

# 여러 개의 조건 동시 사용할 때는 & 기호 사용
transaction[(transaction["result"] == "confirmed") & (transaction["amount"] >= 500)]

# 여러 개의 조건을 or로 묶을 때는 | 기호를 사용한다
transaction[(transaction["result"] == "confirmed") | (transaction["amount"] >= 500)]

# 조건이 두 개 이상일 경우 각 조건을 변수로 뺸 뒤 색인하는 것을 판다스에서 권장함.
confirmed = transaction["result"] == "confirmed" 
high_paid = transaction["amount"] >= 500 
transaction[confirmed & high_paid]
```

### Boolean Selection으로 데이터 선택하기

```python
a = transaction['amount'] < 600  # 값이 boolean 형태로 저장됨
b = (transaction["amount"] >= 300) & (transaction["amount"] < 800)

transaction( a & b ) 다음과 같은 식으로 조건에 맞게 추리기 가능
```

### 컬럼 필터하기 .filter()

- 원하는 글자를 가진 로우를 보여준다.

```python
# select columns containing 'a'
df.filter(like='a',axis=1)
```

- 정규식으로 필터도 가능

```python
# select columns using regex, b로 끝나는 컬럼들을 걸러준다.
df.filter(regex='b$',axis=1)
```

### 특정 컬럼의 문자열 값 변경 or 제거하기... .str.replace()

- regex 옵션에 따라 정규식이 적용되냐 마냐가 결정된다. 햇갈리면 함수만들어서 apply로 해라

```python
my_data['컬럼명'].str.relpace(pat=r'[^A-Za]', repl=r'', regex=True)
```

### 특정 문자열을 포함하는 데이터 추출

.**str.contains()**

```python
df3.loc[df3['item_name'].str.contains('Chips'), :].head(5)
```

### 여러 개의 특정 값 참조하기  .isin()

- bool 형태로 반환됨

```python
# 여러 개의 특정 값 색인을 하고 싶다면 .isin 함수를 사용한다
date_candidate = ["2017-01-01", "2017-01-05", "2017-01-09"] 
transaction[transaction["date"].isin(date_candidate)]
-------------------------------------------------------------------------------
# 명시된 조건과 반대되는 조건을 가져오고 싶다면 물결(~) 표시 사용
transaction[~transaction["date"].isin(date_candidate)]
```

### 컬럼 추가하기 & 수정하기 & 연산하기

```python
# 컬럼에 들어가는 모든 값이 동일할 경우, 파이썬 변수 할당과 동일
transaction["card-holder"] = "KB Card" 
-------------------------------------------------------------------------------

# 모두 값을 할당할 경우 갯수에 맞게 할당한다.
transaction["order"] = [1, 2, 3, 4, 5, 6, 7, 8]  
-------------------------------------------------------------------------------

# 연산을 통해 파생 column 생성 가능
transaction['order_double'] = transaction['order'] * 2   ## 마지막 순서에 추가됨

transaction['sum'] = transaction['order'] + transaction['amount']
-------------------------------------------------------------------------------

# 판다스의 색인/검색을 통해 조건별로 값을 다르게 부여하여 새 변수 생성 가능.. 
# .loc 사용
transaction.loc[transaction["amount"] >= 500, "VIP"] = True 
transaction.loc[transaction["amount"] < 500, "VIP"] = False 
-------------------------------------------------------------------------------

# & 과 | 조건을 이용 가능
transaction.loc[(transaction["result"] == "confirmed") & transaction["amount"] > = 500, "VIP"] = True 
transaction.loc[(transaction["result"] != "confirmed") | (transaction["amount"] < 500), "VIP"] = False 
transaction
-------------------------------------------------------------------------------

# 컬럼의 이름을 변경하기 ## 컬럼을 하나 추가하는 형태
transaction['바꿀이름'] = transaction['바뀌기전']
-------------------------------------------------------------------------------

# 특정 값을 변경하기 replace 함수 사용 replace(old_val, new_val)
transaction['컬럼이름'] = transaction['컬럼이름'].str.replace(2, 20)
```

### insert 함수로 특정 위치에 컬럼 추가하기

```python
# 특정 순서에 컬럼 추가하기 insert 함수
transaction.insert(3, 'order_double', transaction['order']*2 )
```

### 데이터 삭제하기 ... .drop 행 또는 열 삭제

```python
# .drop 을 사용하고 삭제할 Index 를 써넣고 axis = 0  하면 해당 행 삭제
transaction.drop("Kang", axis=0)

# .drop 을 사용하고 삭제할 컬럼명을 써넣고 axis = 1  하면 해당 컬럼 삭제
transaction.drop("date", axis=1) 
# inplace 옵션을 True 로 설정하면 변수에 따로 저장하지 않아도 원래 데이터가 수정됨
-------------------------------------------------------------------------------

# drop 을 사용하지 않고 특정 열 특정 값 삭제하는 방법
my_data = mydata[mydata['특정컬럼명'] != '특정 값']
-------------------------------------------------------------------------------

# 여러 컬럼을 한번에 삭제하는 방법
my_data = my_data.drop(columns=['osversion','devicemanufacturer','canonicaldeviceuuid','channel','event_rank'])
```

### 중복값 확인 및 처리... duplicated

```python
# 중복값 확인
DataFrame.duplicated()

# 중복값 제거
DataFrame.drop_duplicates()
```

중복이 있으면 처음과 끝 중 무슨 값을 남길 것인가? : keep = 'first', 'last', 'False'

- keep='first'가 default 이며, 중복값이 있으면 첫번째 값을 duplicated 여부를 False로 반환하고, 나머지 중복값에 대해서는 True를 반환하게 된다.
- keep='last'는 그 반대
- keep=False는 처음이나 끝값인지 여부는 고려를 안하고 중복이면 무조건 True를 반환한다. (=> 나중에 drop_duplicates() 에서 keep 할 생각이 없다는 뜻).

## 컬럼간의 상관관계 계산하기

- corr 함수 이용 ...연속형 데이터만 연산.. 인과관계를 의미하진 않는다

```python
transaction.corr()

# 상관계수 그래프 ...  색깔이 밝을수록 상관관계가 높다
plt.matshow(transaction.corr())
```

## 시계열 다루기 .. pd.to_datetime

- 판다스는 날짜 데이터로 인식하지 않고 문자열(object, 내지는 string)으로 인식
- datetime 형태로 바꾼 뒤 연/월/일/시/분/초 데이터를 추출

```python
df['Datetime'] = pd.to_datetime(df['Datetime'], format="%m/%d/%Y, %H:%M:%S")

drunk_driving['측정일시'] = pd.to_datetime(drunk_driving['측정일시'])
--------------------------------------------------------------------------------

# 해당 datetime 속성을 index 로 설정하는 코드
drunk_driving.set_index('측정일시', inplace=False)
--------------------------------------------------------------------------------

# 연/월/일/시/분/초 데이터를 추출
drunk_driving['date']= pd.to_datetime(drunk_driving['측정일시'])
drunk_driving['eventdatetime_year']= drunk_driving['date'].dt.year
drunk_driving['eventdatetime_month']= drunk_driving['date'].dt.month
drunk_driving['eventdatetime_day']=  drunk_driving['date'].dt.day
drunk_driving['eventdatetime_hour']= drunk_driving['date'].dt.hour
drunk_driving['eventdatetime_minute']= drunk_driving['date'].dt.minute
drunk_driving['eventdatetime_second']= drunk_driving['date'].dt.second
# 요일
drunk_driving['eventdatetime_weekday']= drunk_driving['date'].dt.weekday

print(drunk_driving.shape)
drunk_driving[['date','eventdatetime_year','eventdatetime_month','eventdatetime_day','eventdatetime_hour','eventdatetime_minute','eventdatetime_second']].head()
```

## 더미 변수 만들기

```python
# 더미 변수
air_I = pd.get_dummies(air_I, columns=['공항',"항공사","편명"])
air_I.head()
```