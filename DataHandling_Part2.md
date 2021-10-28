# Data Handling Part. 2 



## **Pivot Table**

```python
pd.pivot_table(data, values=None, index=None, columns=None, aggfunc='mean', 
fill_value=None, margins=False, dropna=True, margins_name='All', observed=False)
```

- pd.pivot_table('데이터프레임 변수',values=집계 대상 칼럼(수치 데이터), index=행 인덱스가 될 칼럼명, columns=열 인덱스가 될 칼럼명, aggfunc=집계함수-sum,mean,min,max,std,var)

- aggfunc 는 aggregate function의 약자

- fill_value : scalar, default는 None.. pivot table에서 Null 값을 대신할 값

- margins : bool, default는 False.. 모든 row/columns에 더한다.

  **margins=True** 옵션을 설정해주면 행과 열을 기준으로 합계(All, row sum, column sum)를 같이 제시

- dropna : bool, default는 True.. NaN 있는 column을 포함하지 않는다.. 자세히 봐라

- margins_name : str, default 는 'All'..

- observed : bool, default는 False

### 다양한 통계치로 pivot table 구현

```python
# result가 confirmed와 canceled 의 결제 금액의 평균 비교
pd.pivot_table(transaction, index="result", values="amount", aggfunc='mean') 
--------------------------------------------------------------------------------
# 평균 대신 누적값
pd.pivot_table(transaction, index="result", values="amount", aggfunc='sum') 
--------------------------------------------------------------------------------
# 여러 통계연산을 하기 위해서는 numpy 패키지가 필요
pd.pivot_table(transaction, index="result", values="amount", aggfunc=[np.mean, np.sum])
--------------------------------------------------------------------------------
# index에 여러개의 컬럼명을 list를 활용해 넣을 수 있다. 더 디테일한 컬럼 설정 가능
pd.pivot_table(transaction, index=["date", "result"],columns='컬럼명' values="amount") 
--------------------------------------------------------------------------------
# columns 옵션에 특정 컬럼을 넣어서 좌측은 index 위에는 columns 로 표시되게 더 세분화되게 표시 가능
```

### pivot table 결과를 변수에 저장해 여러 연산 가능 .loc 를 사용하여 인덱싱

- 새로운 행으로 추가도 가능

```python
xx = pd.pivot_table(transaction, index=["date", "result"], columns='컬럼명', values="amount")
xx.loc['dif'] = xx.loc['지정인덱스특정값1'] - xx.loc['지정인덱스특정값2']

# 행단위 또는 열단위로 정렬하기 이용해서 정렬
xx.sort_values( "기준컬럼", axis=1, ascending=True )

# 시각화까지 이어질 수 있다.
xx.loc['dif'].plot.bar()
```

# **Group By**

- 특정 컬럼들을 그룹화 하여 특정 통계치 집계 가능
- groupping 함수 : 그룹 데이터에 적용 가능한 통계 함수

1. count
2. sum
3. mean, std, var
4. min, max
5. idxmin, idxmax .. 최소 최댓값의 index 반환

- as_index=False 로 주면 그룹바이 기준 컬럼들이 인덱스가 되지 않는다!!!(컬럼으로 남음)

  그래프를 만들려면 이 옵션이 중요 defualt 가 True 이기 때문에 굉장히 중요한 개념이다

- 다음 두 코드의 차이점은 위에는 데이터 프레임형태가 유지된다는 것

- 밑에 코드는 Series 형태라는 것 따라서 그래프를 그리려면 위에 반드시 위에 형태로 써야한다!!!!

```python
temp3 = df.groupby(['호선','권종'], as_index=False)['승차인원'].sum()
temp4 = df.groupby(['호선','권종'], as_index=False).sum()['승차인원']
class_group = transaction.groupby("result") # groupby 객체가 생성됨
class_group.groups # 이 명령어로 딕셔너리 형태로 특정 컬럼의 각 값에 해당하는 index 값들이 나열됨
--------------------------------------------------------------------------------
# result 컬럼이 confirmed 와 canceled 의 amount의 mean을 구한다.. pivot table과 거의 유사하게 사용 가능
transaction.groupby("result")["amount"].mean() 
--------------------------------------------------------------------------------
# sum 으로 계산
transaction.groupby("result")["amount"].sum()
--------------------------------------------------------------------------------
# 복수의 컬럼으로 groupby .. 이 때는 인덱스가 두개가 되는 것이다.. 두개로 참조 가능
transaction.groupby(["date", "result"])["amount"].mean() 
--------------------------------------------------------------------------------
# 다음과 같이 참조 가능
transaction.groupby(["date", "result"])["amount"].mean().loc[(첫번째 인덱스 값, 두번째 인덱스 값)]
--------------------------------------------------------------------------------
# 다음과 같은 양식도 가능 
class_group = transaction.groupby("result") 
class_group.mean()['amount']
```

- 결과를 눈으로 확인하는 것이 더 중요할 경우 피벗테이블을 사용하면 더 예쁘게 나오고
- 결과를 활용하여 추가로 무언가를 분석하거나, 다른 기능에 활용할 경우 groupby를 사용하는것이 코드가 짧아서 편하다

### index를 이용한 group by

- index 가 있는 경우, groupby 함수에 level 사용 가능

  level 은 index의 depth 를 의미하며, 가장 왼쪽부터 0부터 증가

- set_index 함수

  column 데이터를 index 레벨로 변경.. replace 파라미터가 있다!

- reset_index 함수

  인덱스 초기화

```python
# 다른 강의에서 가져온 자료이기때문에 데이터 이름이 다르다
df.set_index(['Pclass','Sex']) # 인덱스로 설정해줌
df.set_index(['Pclass','Sex']).reset_index  #인덱스를 다시 처음으로 돌림

df.set_index('age').groupby(level=0).mean() 
# index가 한개 있으면 ievel 은 0  두개 있으면 level = 0 or 1 이다
# level 로 groupby 할 index 지정

# 심화.. 함수를 만들어서 컬럼의 값을 수정하고 groupby에 적용하기.. 나이대별로 생존율 구하기
import math
def age_categorize(age):
    if math.isnan(age):
        return -1
    return math.floor(age / 10) * 10

df.set_index('age').groupby(age_categorize).mean()
df.set_index('age').groupby(age_categorize).mean()['survived']
```

### multiindex 를 이용한 groupby

```python
df.set_index(['Pclass','Sex']).groupby(level=[0,1])
df.set_index(['Pclass','Sex']).groupby(level=[0,1]).mean()
df.set_index(['Pclass','Sex']).groupby(level=[0,1]).mean()['age']
```

### get_group() 을 통해 groupby 후 값에 따라 데이터 나누기

- 반드시 groupby 후에 나눈 기준의 값을 통해 나눈다!!!

```python
first_dataset = df.groupby("컬럼명").get_group('첫번째값')
second_dataset = df.groupby("컬럼명").get_group('두번째값')
```

### aggregate(집계) 함수 사용하기 ...

- 여러 개의 통계적 자료 확인 가능
- 각 통계치들은 새로운 컬럼이 된다

```python
df.set_index(['Pclass','Sex']).groupby(level=[0,1]).aggregate([np.mean, np.sum, np.max])
```

### groupby의 멤버 함수 transform

- groupby로 통계함수를 적용하면 원본 데이터에 변화가 생김
- transform은 원본 데이터프레임의 인덱스를 유지하면서 새로운 값을 추가함
- 새로운 속성을 추가하는데 용이한 함수

```python
df.groupby('Pclass').mean() # 로우의 갯수가 클래스의 갯수만큼 바뀜
--------------------------------------------------------------------------------

df.groupby('Pclass').transform(np.mean)  ## 각 컬럼들의 평균이 원본 데이터 형태를 유지하면서 구해짐
df['age2'] = df.groupby('Pclass').transform(np.mean)['age']   ## 새로운 속성 추가에 용이함
--------------------------------------------------------------------------------

df.groupby(['Pclass','Sex']).transform(np.mean) # 두개의 인덱스로 나눠서 구하기도 가능
```

## **데이터를 동일한 길이/개수 로  범주 만들기 pd.cut, pd.qcut**

- pd.cut()으로 동일 길이로 나누어서 범주를 만든 후 GroupBy()로 그룹별 통계량 구하기
- pd.qcut()으로 동일 개수로 나누어서 범주를 만든 후 GroupBy()로 그룹별 통계량 구하기

### pd.cut

```python
df = DataFrame({'col_1': np.random.randint(20, size=20), 'col_2': np.random.randn(20)})
--------------------------------------------------------------------------------

factor_col_1 = pd.cut(df.col_1, bins = 4, labels=['group1','group2','group3','group4']) # 'col_1' 칼럼에 대해 4개의 동일한 길이로 범주 만들기.. 
# 해당 컬럼에 자동으로 간격이 같게 구간 4개가 생겨서 해당 값이 어떤 구간에 속하는지 결과로 나옴
--------------------------------------------------------------------------------

# bins 값을 대괄호 안에 숫자들로 주면 해당 숫자들 간의 간격으로 범위가 설정된다.
pd.cut(boston_data['MEDV'], bins= [5,19,35,50],labels=['group1','group2', 'group3']).head(30)
# 또 labels 를 통해 해당 구간에 그룹 이름을 붙여줄 수 있다!!!
--------------------------------------------------------------------------------

# default 는 구간에서 오른쪽이 포함된다.. (초과, 이하]  하지만, right=False를 주면 [이상, 미만] 으로 설정된다.
pd.cut(boston_data['MEDV'], bins= [5,24,34.7,50], right=False)
--------------------------------------------------------------------------------

grouped_col_1.agg(['count', 'mean', 'std', 'min', 'max'])
```

### pd.qcut

```python
bucket_qcut_col_2 = pd.qcut(df.col_2, 4, labels=False)
--------------------------------------------------------------------------------

bucket_qcut_label_col_2 = pd.qcut(df.col_2, 4, labels=np.arange(4, 0, -1))
```

## **apply ... 모든 Series에 함수 적용**

- 판다스의 Series에 apply를 사용하여 함수를 지정하면,  해당 Series에 들어간 값을 하나하나 가져와 함수를 실행한다. 파이썬의 for문 과 유사한 방식..

```python
# is_vip라는 이름의 함수 정의
# 이 함수는 amount 컬럼 값에 조건을 걸어 True, False 반환
def is_vip(amount):    
    return amount >= 500 
# 이 함수를 apply를 이용하여 데이터에 적용시킴
transaction["VIP"] = transaction["amount"].apply(is_vip) # VIP라는 컴럼을 새로 만들어 결과를 추가시킴.
--------------------------------------------------------------------------------

# 간결한 코드는 lambda 를 이용하여 함수를 만들지 않고 apply 적용 가능
transaction["VIP"] = transaction["amount"].apply(lambda amount: amount >= 500) 
--------------------------------------------------------------------------------

# 컬럼이 아닌 row 에 apply 적용하는 것도 가능하다.    .loc 사용
transaction.loc["Kang"].apply(lambda x: x == 500)  # 자주 쓰이지 않음.  한 행의 모든 속성들의 특정 값에 해당 함수 적용한 결과가 나옴.
--------------------------------------------------------------------------------

# 하나가 아닌 여러 개의 컬럼에 동시에 적용 가능하다.. axis = 1 옵션 
def is_vip(row):    # row가 하나씩 들어가 반복문 처럼 적용됨 
    amount = row["amount"]    # 변수에 값을 저장하여 처리 이런 방식으로 여러 개의 컬럼에 동시 적용 가능
    result = row["result"]    
    return result == "confirmed" and amount >= 500  # 두개 컬럼의 조건을 & 으로 묶었다
# 복수의 컬럼을 동시에 쓰고 싶으면 Series가 아닌 DataFrame에다가 apply를 할 수 있다
# 이 때, axis = 1 이면 row가 하나씩 들어가고, axis = 0 이면 column이 하나씩 들어감
transaction["VIP"] = transaction.apply(is_vip, axis=1) 
--------------------------------------------------------------------------------

# 대문자로 만들기 
def uppercase(x):    
    return x.upper()
```

### 기존에 있는 컬럼을 apply 함수를 적용하여 새로운 함수 생성하기

- apply 함수로 변환하기

예제) age 변수를 20대, 30대 등의 범주형 변수로 변환하기

```python
import math

def age_categorize(age):
    if math.isnan(age):
        return -1    # 결측치는 나눗셈 연산이 안되기 때문에 미리 결측치 처리
    return math.floor(age / 10) * 10    # math.floor 로 소수점을 버린다

my_data['age'].apply(age_categorize)
```

예제) 컬럼에 있는 텍스트를 두개의 컬럼으로 분류하기

```python
## 컬럼 값들이 ios or Android 둘 중 하나 다음에 버전이 있는 형태
def find_ostype(osversion):   
    if 'iOS' in osversion:
        return 'iOS'
    else :
        return ' Android'
--------------------------------------------------------------------------------

def find_osversion(original):  # ios 와 Android 를 ''으로 replace, 원래 컬럼이 뒤에 버전만 남게 된다
        osversion = original.replace('iOS','').replace('Android','')
        return osversion
--------------------------------------------------------------------------------

raw_log['ostype(clean)'] = raw_log['osversion'].apply(find_ostype)
raw_log['osversion(clean)'] = raw_log['osversion'].apply(find_osversion)  # 
print(raw_log.shape)
raw_log[['osversion','ostype(clean)','osversion(clean)']].head()
```

예제) 컬럼 속 여래개의 값을 하나의 값으로 묶어 주기

```python
# Samsung, LG, Apple을 제외한 값들은 Others 로 묶어준다.
def device_col(devicemanufacturer):
    if 'samsung' in devicemanufacturer:
        return 'Samsung'
    elif 'LGE' in devicemanufacturer:
        return 'LG'
    elif 'LG Electronics' in devicemanufacturer:
        return 'LG'
    elif 'Apple' in devicemanufacturer:
        return 'Apple'
    else:
        return 'Others'
    
    return devicemanufacturer

raw_log['devicemanufacturer(clean)'] = raw_log['devicemanufacturer'].apply(device_col)
raw_log['devicemanufacturer(clean)'].value_counts()
```

예제) 구분자를 기준으로 컬럼을 나눠주기

- `viewcategory` - `inappeventcategory`에서 언더바(_)를 기준으로 왼쪽 텍스트만 가져옵니다.
- `viewid` - `inappeventcategory`에서 점(.)의 왼쪽 텍스트만 가져옵니다.
- `viewaction` - `inappeventcategory`에서 점(.)의 오른쪽 텍스트만 가져옵니다.

```python
def find_viewcategory(inappeventcategory):
    if pd.isnull(inappeventcategory):
        return inappeventcategory
    elif inappeventcategory.split('_'):
        return inappeventcategory.split('_')[0]

def find_viewid(inappeventcategory):
    if pd.isnull(inappeventcategory):
        return inappeventcategory
    elif inappeventcategory.split('.'):
        return inappeventcategory.split('.')[0]
    
def find_viewaction(inappeventcategory):
    if pd.isnull(inappeventcategory):
        return inappeventcategory
    elif inappeventcategory.split('.'):
        return inappeventcategory.split('.')[1]
    
raw_log['viewcategory']= raw_log['inappeventcategory'].apply(find_viewcategory)
raw_log['viewid']= raw_log['inappeventcategory'].apply(find_viewid)
raw_log['viewaction']= raw_log['inappeventcategory'].apply(find_viewaction)
raw_log[['inappeventcategory','viewcategory', 'viewid', 'viewaction']].head(5)
```

### apply 함수에 파라미터 전달하기..  함수 인자 2개 이상 사용 가능

키워드 파라미터를 사용하시면, apply가 적용된 함수에 파라미터를 전달하실 수 있습니다.

```python
def extract_year(year, current_year):
    return current_year - int(year)
    
df['age'] = df['year'].apply(extract_year, current_year=2018)
df
```

### apply 함수 데이트프레임 자체에 적용하여 두가지 이상 변수 활용하기

```python
def two_columns_apply(dataframe):
    if (dataframe['Marital_Status'] == 'Married') & (dataframe['Card_Category'] == 'Platinum'):
        return 1
    else:
        return 0

# 반드시 axis=1 값을 주어 열방향으로 계산되도록 해주어야 한다.
card['newState'] = card.apply(two_columns_apply,axis=1)
card['newState'].value_counts()
```

### .map 함수로 인자 다 바꾸기 .. apply 보다 간편하다

```python
df['job'] = df['job'].map({"student":1,"developer":2,"teacher":3})
df
```

### Applymap

컬럼 각각이 아니라 데이터프레임 전체의 각각의 값을 한번에 변경시키실 때 사용

```python
df = df.applymap(np.around)
df
```

# **데이터 재구조화**

## stack, unstack - column, index 재구조화

### stack : 컬럼 레벨에서 인덱스 레벨로 dataframe 변경

- 즉, 데이터를 쌓아올리는 개념으로 이해하면 쉬움

```python
pd.DataFrame.stack(level=-1, dropna=True)
```

### unstack : 인덱스 레벨에서 컬럼 레벨로 dataframe 변경

- stack 과 반대.. 둘은 역의 관계임

```python
pd.DataFrame.unstack(level=-1, fill_value=None)
```

- 결측값이 있는 데이터셋을 stack() 할 때 결측값을 제거할지(dropna=True), 아니면 결측값을 NaN으로 유지할지(dropna=False) 설정
- level 은 인덱스나 컬럼의 순서

```python
# multi index 먼저 만들어주기
new_df = df.set_index(['컬럼1','컬럼2'])
new_df

new_df.unstack(0) # 첫번째 인덱스인 컬럼1 을 컬럼으로 올리기
new_df.stack(1) # 두 번째 컬럼이 인덱스로 내려간다. 두번째 인덱스가됨
```

## melt, wide_to_long

pd.melt() 는 ID 변수를 기준으로 원래 데이터셋에 있던 여러개의 칼럼 이름을 'variable' 칼럼에 위에서 아래로 길게 쌓아놓고, 'value' 칼럼에 ID와 variable에 해당하는 값을 넣어주는 식으로 데이터를 재구조화

```python
pd.melt(data, id_vars, var_name, value_name)
data = pd.DataFrame({'cust_ID' : ['C_001', 'C_001', 'C_002', 'C_002'],
                  'prd_CD' : ['P_001', 'P_002', 'P_001', 'P_002'],
                  'pch_cnt' : [1, 2, 3, 4],
                  'pch_amt' : [100, 200, 300, 400]})
# pd.melt() 의 variable 이름, value 이름 부여하기 : var_name, value_name
pd.melt(data, id_vars=['cust_ID', 'prd_CD'], var_name='pch_CD', value_name='pch_value')
```

## 같은 형태의 테이블을 하나로 합치기 ...concat

- 열 방향 행 방향으로 단순이 붙이기
- ignore_index = True 를 주면 기존 테이블들의 index가 무시된채 새로운 index가 0-based index로 생긴다
- axis = 0 .. 행방향 .. 행들이 늘어남..
- axis = 1 .. 열방향 .. 열들이 늘어남..
- 없는 데이터는 NaN으로 채워짐.

```python
# 첫번째 테이블
import pandas as pd 
transaction01 = pd.read_csv("data/transaction/201701.csv") 
print(transaction01.shape) 
transaction01.head()

# 두번째 테이블
transaction02 = pd.read_csv("data/transaction/201702.csv") 
print(transaction02.shape) 
transaction02.head()

# pd.concat([연결할 데이터프레임 리스트]) 를 이용하여 데이터를 아래로 더해나갈 수 있다.
transaction = pd.concat([transaction01, transaction02]) 
transaction

# append 기능을 이용하여 데이터를 아래로 더해나갈 수 있다. append 는 판다스가 아닌 데이터프레임의 메소드
transaction = transaction01.append([transaction02]) 
transaction    ## pd.concat과 거의 동일하게 동작해서 편할 걸 사용해라!

# 옆으로 컬럼들을 늘리는 방식으로 붙이고 싶을 때도 concat을 이용한다. .. axis = 1
how2 = pd.DataFrame({    
    'payment_method' : ['카드 결제', '카드 결제', '무통장 입금', '카드 결제'],    
    'installment' : ['일시불', '3개월', None, '일시불'] }) 

pd.concat([transaction02, how2], axis=1)  # index 를 기준으로 붙이는 것이기 때문에 사이즈가 잘 맞는지 인덱스가 일치하는지 사전에 잘 확인해야한다.
# pd.concat 도 read_csv 나 read_sql 같은 다양한 옵션이 있다 
# 자세한 것은 판다스의 API 문서 (<https://pandas.pydata.org/pandas-docs/stable/merging.html#merging>) 참고
```

## 서로 다른 형태의 데이터를 하나로 합치기 ... merge

- how = 'inner', 'outer', 'left', 'right'

```python
# pd.merge 를 사용하여 겹치는 컬럼을 자동으로 찾아서 알맞게 합쳐준다
transaction = pd.read_csv("data/transaction/201701.csv") 
print(transaction.shape) 
transaction.head()

user = pd.read_csv("data/transaction/user.csv") 
print(user.shape) 
user.head()

pd.merge(transaction, user)  ## Name 컬럼의 값들이 겹쳐서 합쳐짐.
--------------------------------------------------------------------------------

# 여기서 주의할 점은 Name 컬럼이 겹치지 않은 transaction은 전부 제거된다..
# pd.merge() 가 INNER JOIN 이 Default 이기 때문이다. how 옵션에 LEFT, RIGHT, OUTER 조인을 설정해주면 해결
pd.merge(transaction, user, how='inner')   # 전부 제거됨
pd.merge(transaction, user, how='outer')   # 겹치지 않은 데이터가 남겨지고 NaN으로 채워진다
pd.merge(transaction, user, how='left') # 왼쪽에 넣은 데이터프레임을기준으로 갯수를 맞춘다. 빈칸엔 NaN
pd.merge(transaction, user, how='right') # 오른쪽 .....
--------------------------------------------------------------------------------

# 두 테이블의 컬럼명이 같지 않으면 다음과 같이 설정해줘야 한다.
user.rename(columns={'Name' : '이름'}, inplace = True)   ## 일부러 user 테이블의 컬럼명을 다르게 바꿈
# 원래대로 merge를 하면 에러가 뜬다
pd.merge(transaction, user, how='inner', left_on='Name', right_on = '이름') 
# 위와 같이 설정함으로 정상적으로 작동함.
--------------------------------------------------------------------------------

# 두 컬럼을 index 로 merge 할 경우 다음과 같은 코드로 가능
pd.merge(transaction, user, left_index=True, right_index=True)
all_df = pd.merge(left=tx_df, right=goods_df, how='left' on=['GoodsID','GoodsIDSeqNo'], sort=False)
--------------------------------------------------------------------------------

# set_index 를 이용해서 index로 지정해주고 해도됨.
```

## join .. merge 와 유사한 기능

- join 은 판다스의 함수가 아니고 데이터프레임의 메소드로만 사용할 수 있다.
- pd.join() 은 없고 table.join() 이 있다..
- merge와 기능이 거의 동일하기 때문에 햇갈리지 않게 merge를 사용하기로...

## 도수분포표 만들기 pd.crosstab()

https://rfriend.tistory.com/280

## 복합 응용 예시.. merge, groupby, loc 모두 사용

```python
pd.merge(customer, orders, on='customer_id').groupby(['name','item']).sum().loc['영희','quantity']
```