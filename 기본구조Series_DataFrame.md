# 기본 구조 Series, DataFrame(Matrix)



## **Pandas 의 기본 구조 Series**

- Series는 리스트와 거의 유사하지만 Pandas에서 제공하는 다양한 기능을 사용하기 위해 변환한다.
- numpy의 ndarray 를 기반으로 인덱싱 기능을 추가하여 1차원 배열을 나타냄
- index를 지정하지 않을 시, 기본적으로 ndarray와 같이 0-based 인덱스(0부터 숫자순 서)를 생성, 지정할 경우 명시적으로 지정된 index를 사용
- 같은 타입의 0개 이상의 데이터를 가질 수 있음
- dtype 파라미터로 데이터 타입 명시 가능

```python
----------------------------------------------------------------------------------
##### 리스트를 pandas의 기능을 이용하기 위해 Series로 변환
odd = [1,2,3,4]
odd2 = pd.Series(odd)
odd2
>>>
0    1
1    2
2    3
3    4
dtype: int64
----------------------------------------------------------------------------------
##### index 지정
odd3 = pd.Series(odd, index=('a','b','c','d'))
odd3
>>>
a    1
b    2
c    3
d    4
dtype: int64
----------------------------------------------------------------------------------
##### 인덱스 확인하기 
odd3.index
##### 값 확인하기
odd3.values
>>>
array([1, 2, 3, 4], dtype=int64)
----------------------------------------------------------------------------------
##### index를 통해 값 참조하거나 변경하기
odd3['b']
odd3[ ['a','c'] ] # 리스트를 사용해서 여러개의 인덱스 참조 가능
##### index를 통해 값 변경하기
odd3['b']= 100
odd3
##### 없는 인덱스일시 데이터가 추가된다
odd3['d']= 4
>>>
a      1
b    100
c      3
d      4
dtype: int64
----------------------------------------------------------------------------------
# 평균 구하기  
odd3.mean()   # NaN 을 제외하고 값들의 평균을 구한다.

# value_counts : 각각 값들의 빈도수를 계산해준다
odd3.value_counts()

# shape : 튜플 형태로 shape 알려줌 .. 항상 1차원 형태임
odd3.shape()

# unique : 유일한 값들만 ndarray 로 반환
odd3.unique()

# 데이터 갯수 구하기, 길이 구하기
len(odd3)
odd3.size  # 둘 중 하나 사용

# count : nan 을 제외하고 갯수를 구해준다
odd3.count()
----------------------------------------------------------------------------------
# Series 데이터 연산하기
# 이 때, 같은 인덱스 끼리 값이 연산된다.
# 인덱스가 안맞는것들은 NaN 으로 결과값이 나오고 맞는것끼리 계산된다.

s1 = pd.Series([1,2,3,4,], index=['a','b','c','d'])
s2 = pd.Series([4,3,2,1], index=['d','c','b','a'])
s1 + s2
s1 ** 2  # broadcasting 이 적용됨
s1 ** s2
>>>
a      1
b      4
c     27
d    256
dtype: int64
----------------------------------------------------------------------------------
# Series 데이터 Boolean Selection 으로 데이터 선택하기.. 데이터에 조건절 적용
# numpy와 같다...
s = pd.Series(np.arange(10), index = np.arange(10)+1)

s > 5
s[ s>5 ]     ## True 인것만 값을 가져온다...

s.index > 5
s[s.index > 5]  ## 다음과 같이 index에 조건을 걸어서도 가져올 수 있다..

# 다중 조건에는 & , | 조건으로 사용 가능
s[(s>5) & (s<8)]

# 조건에 해당하는 것에 연산도 가능
s[s>=7].sum()
>>> 24
----------------------------------------------------------------------------------
# Series 데이터 삭제
s = pd.Series(np.arange(100,105), index=['a','b','c','d','e'])

# 인덱스로 삭제
s.drop('b')  
# drop 함수는 원래 함수에 적용되지 않는다, 결과값을 따로 저장해야한다..
s.drop('b', inplace=True)  # inplace 옵션을 True 로 설정해서 s에 적용시키기 가능
>>>
a    100
c    102
d    103
e    104
dtype: int32
----------------------------------------------------------------------------------
# Series 데이터 Slicing 슬라이싱 하기.... list, ndarray 와 동일
s[0:3]

# 인덱스를 다른 것으로 정했어도 0-based index 기준으로 슬라이싱 가능
# 내가 지정해준 인덱스로 슬라이싱할경우 마지막것도 포함한다
s['a':'d']
----------------------------------------------------------------------------------
```

## **DataFrame (Matrix)**

```python
----------------------------------------------------------------------------------
# 엑셀과 같이 2차원 리스트를 DataFrame으로 변환한다.
numbers = [[1,2,3],[4,5,6],[7,8,9]]
numbers2 = pd.DataFrame(numbers)
----------------------------------------------------------------------------------
## DataFrame 의 각 행과 열은 Data Series이다.
type(numbers2)         # type으로 확인 가능
type(numbers2[0])      # 1 번째 열
type(numbers2.loc[0])  # 1 번째 행
----------------------------------------------------------------------------------
data = pd.Series([1,3,5,7,9])  
data.dtypes
### 데이터 타입 출력 가능.. 중간에 하나라도 다른 타입이 있으면 결과 바뀜
----------------------------------------------------------------------------------
```

### 시리즈를 리스트로 만들기

```python
series_.to_list()
```

### 시리즈를 데이터 프레임으로 만들기

```python
series_.to_frame()
```

### Pandas Null 값 생성

- numpy 패키지를 이용하여 null 생성한다. np.nan  -> 이 경우 float 으로 간주함.

```python
import numpy as np
data = pd.Series([1, np.nan, 5, 7 ,9])
data.dtypes
```

### 데이터 타입 변경. astype()

- integer(int) : 정수형 int32, int64
- float : 실수형 float32, float64
- object: 문자열.. 파이썬에서는 string(str) 이라고 표현
- bool : True False

```python
data = data.astype('float')
print(data.dtypes)
data = data.astype('int32')
print(data.dtypes)
```

### DataFrame 생성하는 여러가지 유형

```python
--------------------------------------------------------------------------------
# 데이터만 있는 2차원 리스트에 cloumn 명 추가
transaction = [['2017-01-01', 500, 'confirmed'],
               ['2017-01-03', 700, 'confirmed'],
               ['2017-01-10', 200, 'canceled'],] 
df = pd.DataFrame(transaction, columns=["data","price","state"])
--------------------------------------------------------------------------------
# 파이썬의 dictionary와 list를 활용하여 생성.. 
transaction = {'date': ['2017-01-01', '2017-01-03', '2017-01-10'],
               'price': [500, 700, 200],
               'state': ['confirmed', 'confirmed', 'canceled'], }
df = pd.DataFrame(transaction, index=[1,2,3])
--------------------------------------------------------------------------------
# 위와 유사하지만 list먼저, 그 안에 dictionary 있는 유형
transaction = [{'date': '2017-01-01', 'price': 500, 'state': 'confirmed'},
               {'date': '2017-01-03', 'price': 700, 'state': 'confirmed'},
               {'date': '2017-01-10', 'price': 200, 'state': 'canceled'}, ]
df = pd.DataFrame(transaction)
--------------------------------------------------------------------------------
```

## DataFrame의 기본 기능

### index 지정

```python
trans = [{'date': '2017-01-01', 'price': 500, 'state': 'confirmed'},
               {'date': '2017-01-03', 'price': 700, 'state': 'confirmed'},
               {'date': '2017-01-03', 'price': 900, 'state': 'confirmed'}]
names = ["Kang", "Kim", "Choi"]
df = pd.DataFrame(trans, index = names)  ### index 지정!
df
```

### index, .cloumns, .values

- DataFrame은 row, cloumn, values, index로 구성되며 각각 .index .cloumns .values를 통해 구성 값들을 확인할 수 있다.

```python
df.index
df.cloumns
df.values
# index 도 데이터 타입이고 몇 가지 재미있는 특징을 가지고 있지만, 
# 일단은 Series와 동일한 데이터 타입이라 생각해도 무방하다
type(df.index)

# .values의 반환 결과는 데이터 타입이 numpy의 array 라는 것을 기억해라
```