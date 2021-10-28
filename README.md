# Pandas 코드 모음



### 기본 옵션 변경

```python
# 경고 메시지 제거
import warnings
warnings.filterwarnings(action='ignore')

# 출력 자릿수 설정
pd.options.display.float_format = '{:,.3f}'.format

# 데이터프레임(DataFrame)을 출력 시, 최대 출력할 수 있는 컬럼 수 변경
pd.options.display.max_columns = 100
```

### 파일 1. 파일 읽고 쓰기

- 다양한 확장자의 파일 읽어오기. pd.read_csv()

- 파일안에 csv 하나의 파일로 합치기. glob()

  [파일 읽고 쓰기](https://www.notion.so/aecf0e04e9094e22b79da48264a692c2)

### 파일 2. Pandas 기본 구조

- Series  -  1차원 list 활용
- DataFrame  -  2차원 list 활용

  [기본 구조 Series, DataFrame(Matrix)](https://www.notion.so/Series-DataFrame-Matrix-51dde18b735e4f6a9cd605dae141e90d)

### 파일 3, 4. Data Handling



[Data Handling Part. 1](https://www.notion.so/Data-Handling-Part-1-3d7a6c4f833643eda0736961c97dae12)

- 자주 사용하는 함수들.

[Data Handling Part. 2 ](https://www.notion.so/Data-Handling-Part-2-a61f630618fb4531b6091a546196410c)

- Pivot Table
- Group by
- DataFrame 재구조화 관련 함수

