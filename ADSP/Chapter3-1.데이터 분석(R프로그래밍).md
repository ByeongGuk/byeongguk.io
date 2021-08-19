# R프로그래밍

## R프로그래밍

### R의 Type : 기본형,구조형,복잡형으로 나눌 수 있으며,Special Values가 존재
- 기본형

|데이터 형|특징|
|---|---|
|Numeric|정수,실수,복소수,수학적 연산 및 통계적 계산|
|Character|문자,단어로 구성, "" 또는 ''내에 표현|
|Logical|True,False,산술 연산시 1,0으로 사용|

- 구조형
- 복합형

|데이터 형|차원|원소|원소의 타입|
|---|---|---|---|
|scalar|단일|수치/문자/논리|단일|
|factor|1D|수치/문자|단일,범주형|
|Vector|1D|수치/문자/논리|단일|
|matrix|1D|수치/문자/논리|단일|
|data.frame|2D|수치/문자/논리|복합가능|
|array|2D이상|수치/문자/논리|단일|
|list|2D이상|수치/문자/논리|복합가능|

- Speical Values : NULL(변수 값이 초기화되지 않음), NA(Not Available,데이터 값 없음),NaN(계산 불가능),INF(Infinite,무한대)

### R의 데이터 구조-Vector
- 하나 이상의 스칼라 원소들을 갖는 단순한 형태의 집합
- 숫자,문자,논리형 데이터를 원소로 사용가능
- 동일한 자료형을 갖는 값

```c
10 + 20
5 - 2
4 * 5 
10 / 3
10%/% 3 # 몫
10 %% 3 # 나머지
2 ** 10
2 ^ 10
sqrt(16) # 제곱근
10 > 5
5 < 10
!(5==5)
```

### R의 데이터 구조-Matrix
- 데이터의 형태가 2차원으로 행과 열로 구성
- 하나의 데이터 유형만 가능 
- matrix(data=NA,nrow=1,ncol=1,byrow=FALSE(TRUE 행방향),dimnames=NULL(차원의이름))
- rbind(vector or matrix), cbind(vectors or matrix)

```c
m <- matrix(seq(1,12),nrow=4,ncol = 3)
m1 <- m[2,3]
m2 <- m[3,]
m3 <- m[,-2] # 2열 제외하고 출력
m4 <- m[c(2,3),2] # 2,3 행 2열
m5 <- m[c(TRUE,FALSE,FALSE,TRUE),] # 1,4행 모든열

a1 = array(1:12,dim=c(3,2,2))
a2 = array(1:6, dim=c(2,3))
```

### R의 데이터 구조-Data.frame
- 엑셀의 Worksheet 같은 구조
- 여러 가지 데이터 유형을 가질수 있음.

```c 
name <- c('tom','jerry','cindy','mark')
gender <- c('f','m','f','m')
kor <- c(90,100,80,95)
eng <- c(90,95,100,80)
df <- data.frame(name,gender,kor,eng)
df2 <- data.frame(matrix(seq(1,9),nrow=3))
df3 <- data.frame(gender=c('f','m','f','m'),
                  score=c(90,100,80,95),
                  stringsAsFactors = TRUE) #str Type 을 Factor로 변함

> str(df3)
'data.frame':	4 obs. of  2 variables:
 $ gender: Factor w/ 2 levels "f","m": 1 2 1 2
 $ score : num  90 100 80 95\


rm(list=ls())
name <- c('tom','jerry','cindy','mark')
gender <- c('f','m','f','m')
kor <- c(90,100,80,95)
eng <- c(90,95,100,80)
df <- data.frame(name,gender,kor,eng)

v1 <- df[1,3]
df1 <- df[2,]
v2 <- df[,'name']
v3 <- df$name # name열
df2 <- df[1] # 1번열
df3 <- df['name']
v4 <- df[[1]] # 1번열,Vector
df4 <- df[seq(1,3),c(1,2)] # 1~3번행,1,2열
df5 <- df[,2:4]
df6 <- df[1:3]
df7 <- df[-2] # 2열 삭제
df8 <- df[-c(1,2,3)]
```

### R의 데이터 구조-list
- 서로다른 데이터 타입 저장가능
- 리스트에 저장된 데이터를 index또는 key를 사용해 접근가능

### 저장,불러오기
- setwd('디렉토리 이름') / getwd() 현재 디렉터리 이름 반환
- read.csv('파일이름', fileEncoding='UTF-8-BOM')
- write.csv('파일이름', row.names=FALSE)

```c
df = read.csv('.\\data\\easySample.csv',fileEncoding = 'UTF-8-BOM')
write.csv(df,'data/mySample.csv', row.names=FALSE)
write.csv(df,'data/mySample.csv', row.names=TRUE) # 행번호추가

cname <- c('ID','name','birth','dept','english','japaneese','chineese')
df3 <- read.table('data/easySample.txt',sep=',',col.names = cname) # TEXT 불러오기

rm(list=ls())
df4 <- read.csv('data/mySample.csv')
save(df4,file='data/myObject1.Rdata') # 객체 저장
load('data/myObject1.Rdata')
```

### data.frame 핸들링
- 

```c
rm(list=ls())
df = read.csv('.\\data\\easySample3.csv',fileEncoding = 'UTF-8-BOM')
df1 <- df[,c(2,4,8,9)]
df1$score <- c(90,80,50,70,100,95,85,70,95,75)
df2 <- subset(df1,subset = (salary>=5000)) # Salary >= 5000이상
df3 <- subset(df2,select=-score) # Score 열 제외
df4 <- subset(df1,subset = (salary>=5000),select=-score)
df5 <- subset(df2,select = c(pname,dept,salary)
colnames(df5)[2] <- 'department'  #칼럼명 변경
```

### R의 데이터셋 사용
- head,tail,summary,str : vector,matrix 등의 다양한 객체 사용가능
- View : matrix,data.frame,list 등 Data 구조에 적용 가능

```c
View(iris)
head(iris,3) # 처음부터
tail(iris,3) # 끝에서 
summary(iris) # 최소,최대,평균,1사/3사분지수
str(iris) # 데이터 Type
print(iris$Sepal.Width)
attach(iris)
print(Sepal.Width)
detach(iris)
print(iris$Sepal.Width)
```

### 그래프 종류
- 그래프의 종류
![graph](./img/graph.png)
- 산점도 : 2개 변수의 상관관계 plot(x,y)
- 산점도 행렬 : 여러 개의 변수 관계 pairs()
- **상자그림** : 이상치 존재 확인 NA 제외,**IQR** 길이(3사분위-1사분위), 중위값, 최소, 최대 확인,상한 값(Q3+IQR*1.5),하한 값(Q1-IQR*1.5) . boxplot()
![boxplot](./img/boxplot.png)
- 히스토그램 : 연속적 수치에 적합 hist()
![histograph](./img/histograph.png)
- 막대 그래프 : 변수의 빈도 활용, 막대 사이가 끊겨 있는 모양 barplot()

### R의 데이터 형변화

```c
r1 <- as.integer(3.14)  # 소수점 아래 절삭
r2 <- as.numeric('abc')
r3 <- as.numeric(TRUE) # TRUE:1/FALSE:0
r4 <- as.logical(0.1)

rm(list=ls())
r1 <- as.Date('2021-5-01') # 날짜 타입
#r2 <- as.Date('03/14/2021')
r3 <- as.Date('03/14/2021', format='%m/%d/%Y')
r4 <- format(Sys.Date()) # 현재 날짜
r5 <- format(Sys.Date(), format='%m/%d/%Y')

rm(list=ls())
r1 <- format(Sys.Date(), '%a')
r2 <- format(Sys.Date(), '%b')
r3 <- format(Sys.Date(), '%y')
r4 <- format(Sys.Date(), '%Y')
```

### Apply 계열함수
- 데이터 조작에 편리한 기능 제공,apply 함수가 가장 많이 사용
- for문  없이 milti-core 사용으로 빠르게 연산
- split -> apply -> combine 기능 제공, (데이터 분할->함수 적용 -> 재결합)
    - apply : array 행 또는 열 별로 함수를 적용한 다음 결과를 vector 또는 array 적용
    - lapply : vector,list에 함수를 원소별 적용하여 결과를 list로 반환
    - sapply : lapply와 유사하며, 결과를 vector,matrix 또는 array로 반환
    - tapply : 입력데이터를 특정 기준으로 묶고, 각 그룹마다 주어진 함수 적용

```c
a <- list(c(1,2,3), seq(1,9,2), seq(2,8,2))
r1 <- lapply(a, sum) # list
r2 <- unlist(r1) # factor
r3 <- sapply(a, sum) # factor
r4 <- lapply(c(1,2,3), function(x) { x*2 - 1 }) # list

rm(list=ls())
a <- c(1, 11, 2, 22, 3, 33, 4, 44)
b <- rep(c(TRUE, FALSE), 4) # TRUE,FALSE 4번 반복
r1 <- tapply(a, a<=10, sum) # 
r2 <- tapply(a, c(1,1,2,2,3,3,4,4), sum) # a는 함수를 사용할 변수,c(1,1,2,2,3,3,4,4)는 a를 그룹 변수(ex)1,11은 1번그룹),sum은 함수
r3 <- tapply(iris$Sepal.Length, iris$Species, mean) # 변수 : IRIS 길이/ 그룹 변수:Species / 함수 : mean(평균)
r4 <- tapply(a, b, sum)
```
### 패키지 설치,대표적인 패키지
- install.package('패키지이름')
- library('패키지이름')
- 주요패키지
    - reshape : melt,cast

```c
install.packages('reshape')
library('reshape') # library(reshape) 동일

data(airquality)
names(airquality)
colnames(airquality) <- tolower(colnames(airquality)) # 컬럼 소문자변환
names(airquality)

a <- melt(airquality, id=c('month', 'day'), na.rm=TRUE) # melt는 id로 주어진 값들만 고정적이고, 나머지 값들은 Variable/Value로만 표시
r1 <- cast(a, day~month~variable) # 행:day,열:month (3차원 행렬 표시)
r2 <- cast(a, month~variable, mean) 
r3 <- cast(a, month~variable, mean, margins=c('grand_row', 'grand_col')) # 행/열 총합 정보 추가
r4 <- cast(a, day~month, mean, subset=variable=='ozone') # subset(변수) 선택
r5 <- cast(a, month~variable, range) # range는 min,max 값 출력
```
    - reshape2 :acst,dcast
    - sqldf : sql문자열 
    - plyr : ply,adply,ddply()

### 유용한 함수
- paste : 문자열 붙임
- substr : 특정 문자열 추출 (substr(대상,시작위치,끝위치))
- split : 구분자 (split(iris,iris$Species))
- subset : 특정 부분만 추출(subset(iris,Species=='setosa'& Sepal.Length > 5.0))
- select : subset 에 select 인자를 지정하면 특정 열 선택 또는 제외 (subset(iris))

### 객체의 속성 관련 함수
- is.numeric(x) : Numeric
- is.logical(x): 객체
- is.character : 문자
- is.integer() : 정수
- is.double() : 실수
- is.factor : factor
- is.null,is.na : NULL,NA
- length : 길이
- nrow : 행의 개수
- NROW : 벡터의 행의 개수

```c
number <- 1:5
alphabet <- c('a', 'b', 'c')
paste(number, alphabet)
fruit <- c('apple', 'banana', 'orange')
substr(fruit, 2, 4)
r1 <- split(iris, iris$Species)
subset(iris, subset=Species=='setosa' & Sepal.Length > 5.5)
subset(iris, subset=Species=='setosa' & Sepal.Length > 5.5, 
       select = -c(Sepal.Length, Species))
```
### R 제어문,사용자 정의 함수

```c 
rm(list=ls())
a <- 15
if (a > 10) {
  print('a가 10보다 크다')
} else {
  print('a가 10보다 작거나 같다')
}

score <- c(100, 58, 95, 55, 85)
result <- ifelse(score >= 60, 'P', 'F')

rm(list=ls())
a <- c()
for (i in 1:5) {
  a[i] <- 2*i + 3
}

rm(list=ls())
a <- 1
while(a < 5){
  print(a)
  a <- a + 1
}


add <- function(a, b) {
  tot <- 0
  for (i in a:b) {
    tot <- tot + i
  }
  return (tot)
}

add(1, 10)
```

### 결측치 대치법
- 단숙 대치법
  - 완전 응답한 개체 분석 : 불완전 자료 무시,
  - 평균 대치법 : 비조건부 평균 대치법(데이터 평균값),조건부 평균대치법(회귀분석)
  - 단순확률대치법 : 
- 다중 대치법 : m번 m개 가상적 완전 자료 만듦. 계산의 난해성 문제 존재

### 결측치 인식,선택,삭제
- is.na : NA  
- complete.case() : 행 별로 결측치가 없으면 TRUE, 있으면 FALSE
- x[!complete.case(),] : 결측치 행만 추출
- x[!complete.case(),] : 결측치 포함하지 않은 행 추출
- na.omit() : 결측치(NA) 행 전체 삭제

```c
rm(list=ls())
data("airquality")
colnames(airquality) <- tolower(colnames(airquality))

r1 <- is.na(airquality$ozone)
r2 <- sum(r1)
r3 <- table(is.na(airquality$ozone))
r4 <- apply(airquality, 2, function(x) { sum(is.na(x))}) #2: 열방향
air_na <- airquality[!complete.cases(airquality),]
air_com <- airquality[complete.cases(airquality),]
r5 <- na.omit(airquality)
```

### 이상값
- 의도치 않게 잘못 입력된 값
- 분석 목적x
- 의도하지 않은 현상이지만, 분석에 포함
- 이상값 판단
  - ESD : 평균으로부터 3 * 표준편차 밖의 값
  - boxplot 사용 : IQR * 1.5
  - summary : 평균,중앙값,IQR 보고 판단
- 이상값 처리 : 무조건 제외 X