신용카드 고객이탈 분석
================
이은서
2021/08/31

<style type="text/css">
  body{
  font-size: 15pt;
  font-family: Calibri
}
</style>

# 신용카드 고객이탈분석 모델 개발

## 과제 목표

신용카드 이용 고객 데이터를 활용한 고객 이탈 예측 모델 개발

## 분석결과 활용 프로세스

고객 이탈에 있어 어떠한 요소가 크게 작용하는지 분석 후 고객 이탈 방지

## 현황

신규 고객 유입보다는 기존 고객의 이탈을 방지함으로써 고객 수 유지 필요

## 문제점

이탈 고객 분류가 제대로 이루어지지 않아 이탈 가능성이 있는 고객을 제대로
구분해내지 못하는 중

## 분석 주요 내용

1.  이탈 / 비이탈 고객 분류
2.  사용카드 선택에 있어 가장 많은 영향을 끼치는 고객 특성 선정
3.  결과 도출

## 기대효과

-   기존에 분류하지 못했던 숨은 이탈 고객을 분류하여 분류 원인 발견 가능
-   이탈 가능성이 있는 고객의 상황과 이탈 이유를 분석하여 향상된
    고객서비스 제공
-   다양한 카드 서비스 제공을 위한 고객 특성 분석 및 희망 서비스 예측
    가능

## 필요 데이터

신용카드 고객 데이터

## 1. 분석 데이터 구성

-   분석 데이터 : 신용카드 고객 데이터
    (<https://www.kaggle.com/sakshigoyal7/credit-card-df>)
-   수집 대상 : 신용카드 고객 10,127명
-   고객번호를 제외한 범주형 변수 6개(이탈 여부, 성별, 부양 가족수,
    학력, 결혼 여부, 수입)와 수치형 변수 16개로 구성
-   분석에 이용할 독립변수는 label(이탈 여부)로 선정
-   변수 목록

<!-- -->

    ##  [1] "Customer_Age"             "Gender"                  
    ##  [3] "Dependent_count"          "Education_Level"         
    ##  [5] "Marital_Status"           "Income_Category"         
    ##  [7] "Card_Category"            "Months_on_book"          
    ##  [9] "Total_Relationship_Count" "Months_Inactive_12_mon"  
    ## [11] "Contacts_Count_12_mon"    "Credit_Limit"            
    ## [13] "Total_Revolving_Bal"      "Avg_Open_To_Buy"         
    ## [15] "Total_Amt_Chng_Q4_Q1"     "Total_Trans_Amt"         
    ## [17] "Total_Trans_Ct"           "Total_Ct_Chng_Q4_Q1"     
    ## [19] "Avg_Utilization_Ratio"    "label"

-   데이터 명세

<!-- -->

    ##   Customer_Age Gender Dependent_count Education_Level Marital_Status
    ## 1           45      M               3     High School        Married
    ## 2           49      F               5        Graduate         Single
    ## 3           51      M               3        Graduate        Married
    ## 4           40      F               4     High School        Unknown
    ## 5           40      M               3      Uneducated        Married
    ## 6           44      M               2        Graduate        Married
    ##   Income_Category Card_Category Months_on_book Total_Relationship_Count
    ## 1     $60K - $80K          Blue             39                        5
    ## 2  Less than $40K          Blue             44                        6
    ## 3    $80K - $120K          Blue             36                        4
    ## 4  Less than $40K          Blue             34                        3
    ## 5     $60K - $80K          Blue             21                        5
    ## 6     $40K - $60K          Blue             36                        3
    ##   Months_Inactive_12_mon Contacts_Count_12_mon Credit_Limit Total_Revolving_Bal
    ## 1                      1                     3        12691                 777
    ## 2                      1                     2         8256                 864
    ## 3                      1                     0         3418                   0
    ## 4                      4                     1         3313                2517
    ## 5                      1                     0         4716                   0
    ## 6                      1                     2         4010                1247
    ##   Avg_Open_To_Buy Total_Amt_Chng_Q4_Q1 Total_Trans_Amt Total_Trans_Ct
    ## 1           11914                1.335            1144             42
    ## 2            7392                1.541            1291             33
    ## 3            3418                2.594            1887             20
    ## 4             796                1.405            1171             20
    ## 5            4716                2.175             816             28
    ## 6            2763                1.376            1088             24
    ##   Total_Ct_Chng_Q4_Q1 Avg_Utilization_Ratio label
    ## 1               1.625                 0.061     0
    ## 2               3.714                 0.105     0
    ## 3               2.333                 0.000     0
    ## 4               2.333                 0.760     0
    ## 5               2.500                 0.000     0
    ## 6               0.846                 0.311     0

## 2. EDA

## 데이터 기본통계

    ##   Customer_Age   Gender   Dependent_count      Education_Level  Marital_Status
    ##  Min.   :26.00   F:5358   Min.   :0.000   College      :1013   Married :4687  
    ##  1st Qu.:41.00   M:4769   1st Qu.:1.000   Graduate     :3128   Single  :3943  
    ##  Median :46.00            Median :2.000   High School  :2013   Divorced: 748  
    ##  Mean   :46.33            Mean   :2.346   Post-Graduate: 516   Unknown : 749  
    ##  3rd Qu.:52.00            3rd Qu.:3.000   Doctorate    : 451                  
    ##  Max.   :73.00            Max.   :5.000   Uneducated   :1487                  
    ##                                           Unknown      :1519                  
    ##        Income_Category  Card_Category  Months_on_book  Total_Relationship_Count
    ##  Less than $40K:3561   Blue    :9436   Min.   :13.00   Min.   :1.000           
    ##  $40K - $60K   :1790   Silver  : 555   1st Qu.:31.00   1st Qu.:3.000           
    ##  $60K - $80K   :1402   Gold    : 116   Median :36.00   Median :4.000           
    ##  $80K - $120K  :1535   Platinum:  20   Mean   :35.93   Mean   :3.813           
    ##  $120K +       : 727                   3rd Qu.:40.00   3rd Qu.:5.000           
    ##  Unknown       :1112                   Max.   :56.00   Max.   :6.000           
    ##                                                                                
    ##  Months_Inactive_12_mon Contacts_Count_12_mon  Credit_Limit  
    ##  Min.   :0.000          Min.   :0.000         Min.   : 1438  
    ##  1st Qu.:2.000          1st Qu.:2.000         1st Qu.: 2555  
    ##  Median :2.000          Median :2.000         Median : 4549  
    ##  Mean   :2.341          Mean   :2.455         Mean   : 8632  
    ##  3rd Qu.:3.000          3rd Qu.:3.000         3rd Qu.:11068  
    ##  Max.   :6.000          Max.   :6.000         Max.   :34516  
    ##                                                              
    ##  Total_Revolving_Bal Avg_Open_To_Buy Total_Amt_Chng_Q4_Q1 Total_Trans_Amt
    ##  Min.   :   0        Min.   :    3   Min.   :0.0000       Min.   :  510  
    ##  1st Qu.: 359        1st Qu.: 1324   1st Qu.:0.6310       1st Qu.: 2156  
    ##  Median :1276        Median : 3474   Median :0.7360       Median : 3899  
    ##  Mean   :1163        Mean   : 7469   Mean   :0.7599       Mean   : 4404  
    ##  3rd Qu.:1784        3rd Qu.: 9859   3rd Qu.:0.8590       3rd Qu.: 4741  
    ##  Max.   :2517        Max.   :34516   Max.   :3.3970       Max.   :18484  
    ##                                                                          
    ##  Total_Trans_Ct   Total_Ct_Chng_Q4_Q1 Avg_Utilization_Ratio label   
    ##  Min.   : 10.00   Min.   :0.0000      Min.   :0.0000        0:8500  
    ##  1st Qu.: 45.00   1st Qu.:0.5820      1st Qu.:0.0230        1:1627  
    ##  Median : 67.00   Median :0.7020      Median :0.1760                
    ##  Mean   : 64.86   Mean   :0.7122      Mean   :0.2749                
    ##  3rd Qu.: 81.00   3rd Qu.:0.8180      3rd Qu.:0.5030                
    ##  Max.   :139.00   Max.   :3.7140      Max.   :0.9990                
    ## 

## 이탈/비이탈 고객 분석

| label | Freq |
|:------|-----:|
| 0     | 8500 |
| 1     | 1627 |

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-5-1.png)

<!-- -->

-   이탈 고객 1627명, 비이탈 고객 8500명, 전체 고객수의 16%가
    이탈고객으로 데이터의 불균형이 보여짐 -&gt; 이후 SMOTE를 통한 데이터
    불균형 핸들링 진행

## 범주형 변수 분석

### 인구통계학적 특징 분석

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-6-1.png)

<!-- -->

1.  여성 고객이 남성 고객보다 많음
2.  연령대는 20대부터 70대까지 골고루 분포, 가장 많은 연령대는 40대
3.  부양가족수는 평균 2.3명

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-7-1.png)

<!-- -->

1.  고객의 학력은 대졸자가 가장 많음
2.  고객의 결혼 여부는 기혼이 가장 많음
3.  4만 달러 이하의 수입 수준의 고객 수가 가장 많음
4.  보유 카드 대비 플래티넘에서의 고객 이탈이 가장 많음음

### 수치형 변수 분석

-   이탈 고객이 비이탈 고객보다 카드 이용 구매 횟수, 카드 이용 중 카드사
    연락 횟수, 카드 이용 한도, 리볼빙 한도, 총 결제량과 횟수, 평균 카드
    활용 비율이 낮음 -&gt; 이탈 고객의 이탈 원인에 있어 뚜렷한 원인 한
    가지만 있는 것이 아닌 여러 복합적인 원인이 있는것으로 예상

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-8-1.png)

<!-- -->

-   이탈 고객과 비이탈 고객의 은행 가입기간에는 큰 차이가 없음

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-9-1.png)

<!-- -->

-   이탈 고객의 카드 이용 구매 횟수가 비이탈 고객보다 적음

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-10-1.png)

<!-- -->

-   이탈 고객의 비활동(카드 비이용) 기간은 비이탈 고객보다 긺

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-11-1.png)

<!-- -->

-   이탈 고객의 카드 이용 중 카드사 연락 횟수가 비이탈 고객보다 많음

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-12-1.png)

<!-- -->

-   이탈 고객의 카드 이용 한도가 비이탈 고객보다 낮음

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-13-1.png)

<!-- -->

-   이탈 고객의 리볼빙 한도가 비이탈 고객보다 낮음

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-14-1.png)

<!-- -->

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-14-2.png)

<!-- -->

-   이탈 고객의 총 결제량과 횟수가 비이탈 고객보다 적음

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-15-1.png)

<!-- -->

-   이탈 고객의 평균 카드 활용 비율이 비이탈 고객보다 적음

## 3. 변수간 상관관계

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-16-1.png)



<!-- -->

-   총 리볼빙 한도와 평균 활용 비율의 상관계수 : 0.61, 총 거래 양과 총
    거래 횟수의 상관계수: 0.81
-   그 외 변수간 상관관계는 크게 나타나지 않음

## 4. 분석 모델 개발

### 1. 데이터 불균형 처리 및 분할

``` r
# 라벨인코딩
df$Gender <- as.numeric(df$Gender)
df$Education_Level <- as.numeric(df$Education_Level)
df$Marital_Status <- as.numeric(df$Marital_Status)
df$Income_Category <- as.numeric(df$Income_Category)
df$Card_Category <- as.numeric(df$Card_Category)

# 데이터 분할 (7:3)
df <- mutate_all(df, function(x) as.numeric(as.character(x)))
trainIndex <- createDataPartition(df$label, p = 0.70, list = F)
train <- df[trainIndex,]
test <- df[-trainIndex,]
```

``` r
# SMOTE를 이용한 데이터 불균형 처리
train.smote <- SMOTE(train[1:19], train$label, K=5)
train.smote <- train.smote$data
train.smote$class<-factor(train.smote$class)
```

### 2. 분류 모델링 및 평가

#### 로지스틱 회귀모형

``` r
library(e1071)
model1= glm(class ~., data=train.smote, family = "binomial")
step(model1, direction = "backward")
```

    ## Start:  AIC=7718.97
    ## class ~ Customer_Age + Gender + Dependent_count + Education_Level + 
    ##     Marital_Status + Income_Category + Card_Category + Months_on_book + 
    ##     Total_Relationship_Count + Months_Inactive_12_mon + Contacts_Count_12_mon + 
    ##     Credit_Limit + Total_Revolving_Bal + Avg_Open_To_Buy + Total_Amt_Chng_Q4_Q1 + 
    ##     Total_Trans_Amt + Total_Trans_Ct + Total_Ct_Chng_Q4_Q1 + 
    ##     Avg_Utilization_Ratio
    ## 
    ## 
    ## Step:  AIC=7718.97
    ## class ~ Customer_Age + Gender + Dependent_count + Education_Level + 
    ##     Marital_Status + Income_Category + Card_Category + Months_on_book + 
    ##     Total_Relationship_Count + Months_Inactive_12_mon + Contacts_Count_12_mon + 
    ##     Credit_Limit + Total_Revolving_Bal + Total_Amt_Chng_Q4_Q1 + 
    ##     Total_Trans_Amt + Total_Trans_Ct + Total_Ct_Chng_Q4_Q1 + 
    ##     Avg_Utilization_Ratio
    ## 
    ##                            Df Deviance     AIC
    ## - Income_Category           1   7681.0  7717.0
    ## - Months_on_book            1   7681.1  7717.1
    ## - Avg_Utilization_Ratio     1   7681.4  7717.4
    ## - Credit_Limit              1   7682.0  7718.0
    ## <none>                          7681.0  7719.0
    ## - Customer_Age              1   7683.8  7719.8
    ## - Card_Category             1   7685.8  7721.8
    ## - Education_Level           1   7689.7  7725.7
    ## - Total_Amt_Chng_Q4_Q1      1   7699.9  7735.9
    ## - Dependent_count           1   7702.1  7738.1
    ## - Marital_Status            1   7734.0  7770.0
    ## - Gender                    1   7792.3  7828.3
    ## - Total_Revolving_Bal       1   8030.6  8066.6
    ## - Contacts_Count_12_mon     1   8034.5  8070.5
    ## - Months_Inactive_12_mon    1   8059.8  8095.8
    ## - Total_Relationship_Count  1   8117.7  8153.7
    ## - Total_Ct_Chng_Q4_Q1       1   8198.3  8234.3
    ## - Total_Trans_Amt           1   8858.3  8894.3
    ## - Total_Trans_Ct            1  10915.7 10951.7
    ## 
    ## Step:  AIC=7717
    ## class ~ Customer_Age + Gender + Dependent_count + Education_Level + 
    ##     Marital_Status + Card_Category + Months_on_book + Total_Relationship_Count + 
    ##     Months_Inactive_12_mon + Contacts_Count_12_mon + Credit_Limit + 
    ##     Total_Revolving_Bal + Total_Amt_Chng_Q4_Q1 + Total_Trans_Amt + 
    ##     Total_Trans_Ct + Total_Ct_Chng_Q4_Q1 + Avg_Utilization_Ratio
    ## 
    ##                            Df Deviance     AIC
    ## - Months_on_book            1   7681.2  7715.2
    ## - Avg_Utilization_Ratio     1   7681.4  7715.4
    ## - Credit_Limit              1   7682.3  7716.3
    ## <none>                          7681.0  7717.0
    ## - Customer_Age              1   7683.8  7717.8
    ## - Card_Category             1   7685.8  7719.8
    ## - Education_Level           1   7689.7  7723.7
    ## - Total_Amt_Chng_Q4_Q1      1   7699.9  7733.9
    ## - Dependent_count           1   7702.1  7736.1
    ## - Marital_Status            1   7734.0  7768.0
    ## - Gender                    1   7796.2  7830.2
    ## - Total_Revolving_Bal       1   8032.1  8066.1
    ## - Contacts_Count_12_mon     1   8035.1  8069.1
    ## - Months_Inactive_12_mon    1   8059.9  8093.9
    ## - Total_Relationship_Count  1   8118.7  8152.7
    ## - Total_Ct_Chng_Q4_Q1       1   8198.3  8232.3
    ## - Total_Trans_Amt           1   8862.8  8896.8
    ## - Total_Trans_Ct            1  10917.6 10951.6
    ## 
    ## Step:  AIC=7715.15
    ## class ~ Customer_Age + Gender + Dependent_count + Education_Level + 
    ##     Marital_Status + Card_Category + Total_Relationship_Count + 
    ##     Months_Inactive_12_mon + Contacts_Count_12_mon + Credit_Limit + 
    ##     Total_Revolving_Bal + Total_Amt_Chng_Q4_Q1 + Total_Trans_Amt + 
    ##     Total_Trans_Ct + Total_Ct_Chng_Q4_Q1 + Avg_Utilization_Ratio
    ## 
    ##                            Df Deviance     AIC
    ## - Avg_Utilization_Ratio     1   7681.6  7713.6
    ## - Credit_Limit              1   7682.4  7714.4
    ## <none>                          7681.2  7715.2
    ## - Card_Category             1   7686.0  7718.0
    ## - Education_Level           1   7690.0  7722.0
    ## - Customer_Age              1   7691.2  7723.2
    ## - Total_Amt_Chng_Q4_Q1      1   7700.1  7732.1
    ## - Dependent_count           1   7702.5  7734.5
    ## - Marital_Status            1   7734.4  7766.4
    ## - Gender                    1   7796.2  7828.2
    ## - Total_Revolving_Bal       1   8032.9  8064.9
    ## - Contacts_Count_12_mon     1   8035.3  8067.3
    ## - Months_Inactive_12_mon    1   8064.5  8096.5
    ## - Total_Relationship_Count  1   8119.5  8151.5
    ## - Total_Ct_Chng_Q4_Q1       1   8198.6  8230.6
    ## - Total_Trans_Amt           1   8864.1  8896.1
    ## - Total_Trans_Ct            1  10920.4 10952.4
    ## 
    ## Step:  AIC=7713.57
    ## class ~ Customer_Age + Gender + Dependent_count + Education_Level + 
    ##     Marital_Status + Card_Category + Total_Relationship_Count + 
    ##     Months_Inactive_12_mon + Contacts_Count_12_mon + Credit_Limit + 
    ##     Total_Revolving_Bal + Total_Amt_Chng_Q4_Q1 + Total_Trans_Amt + 
    ##     Total_Trans_Ct + Total_Ct_Chng_Q4_Q1
    ## 
    ##                            Df Deviance     AIC
    ## - Credit_Limit              1   7682.5  7712.5
    ## <none>                          7681.6  7713.6
    ## - Card_Category             1   7686.5  7716.5
    ## - Education_Level           1   7690.5  7720.5
    ## - Customer_Age              1   7691.8  7721.8
    ## - Total_Amt_Chng_Q4_Q1      1   7700.4  7730.4
    ## - Dependent_count           1   7702.7  7732.7
    ## - Marital_Status            1   7734.5  7764.5
    ## - Gender                    1   7799.3  7829.3
    ## - Contacts_Count_12_mon     1   8035.5  8065.5
    ## - Months_Inactive_12_mon    1   8064.8  8094.8
    ## - Total_Relationship_Count  1   8119.5  8149.5
    ## - Total_Ct_Chng_Q4_Q1       1   8198.6  8228.6
    ## - Total_Revolving_Bal       1   8510.8  8540.8
    ## - Total_Trans_Amt           1   8867.7  8897.7
    ## - Total_Trans_Ct            1  10925.1 10955.1
    ## 
    ## Step:  AIC=7712.47
    ## class ~ Customer_Age + Gender + Dependent_count + Education_Level + 
    ##     Marital_Status + Card_Category + Total_Relationship_Count + 
    ##     Months_Inactive_12_mon + Contacts_Count_12_mon + Total_Revolving_Bal + 
    ##     Total_Amt_Chng_Q4_Q1 + Total_Trans_Amt + Total_Trans_Ct + 
    ##     Total_Ct_Chng_Q4_Q1
    ## 
    ##                            Df Deviance     AIC
    ## <none>                          7682.5  7712.5
    ## - Education_Level           1   7691.5  7719.5
    ## - Customer_Age              1   7692.3  7720.3
    ## - Card_Category             1   7692.5  7720.5
    ## - Total_Amt_Chng_Q4_Q1      1   7701.3  7729.3
    ## - Dependent_count           1   7704.3  7732.3
    ## - Marital_Status            1   7735.8  7763.8
    ## - Gender                    1   7820.4  7848.4
    ## - Contacts_Count_12_mon     1   8036.7  8064.7
    ## - Months_Inactive_12_mon    1   8065.0  8093.0
    ## - Total_Relationship_Count  1   8120.4  8148.4
    ## - Total_Ct_Chng_Q4_Q1       1   8199.5  8227.5
    ## - Total_Revolving_Bal       1   8511.0  8539.0
    ## - Total_Trans_Amt           1   8874.3  8902.3
    ## - Total_Trans_Ct            1  10925.9 10953.9
    
    ## 
    ## Call:  glm(formula = class ~ Customer_Age + Gender + Dependent_count + 
    ##     Education_Level + Marital_Status + Card_Category + Total_Relationship_Count + 
    ##     Months_Inactive_12_mon + Contacts_Count_12_mon + Total_Revolving_Bal + 
    ##     Total_Amt_Chng_Q4_Q1 + Total_Trans_Amt + Total_Trans_Ct + 
    ##     Total_Ct_Chng_Q4_Q1, family = "binomial", data = train.smote)
    ## 
    ## Coefficients:
    ##              (Intercept)              Customer_Age                    Gender  
    ##                7.9715387                -0.0123833                -0.7326588  
    ##          Dependent_count           Education_Level            Marital_Status  
    ##                0.1143334                 0.0455191                 0.2587579  
    ##            Card_Category  Total_Relationship_Count    Months_Inactive_12_mon  
    ##                0.2716598                -0.4416666                 0.6121389  
    ##    Contacts_Count_12_mon       Total_Revolving_Bal      Total_Amt_Chng_Q4_Q1  
    ##                0.5395029                -0.0009108                -0.6529064  
    ##          Total_Trans_Amt            Total_Trans_Ct       Total_Ct_Chng_Q4_Q1  
    ##                0.0005992                -0.1385408                -3.2195212  
    ## 
    ## Degrees of Freedom: 11744 Total (i.e. Null);  11730 Residual
    ## Null Deviance:       16280 
    ## Residual Deviance: 7682  AIC: 7712

``` r
fitmodel=glm(formula = class ~ Gender + Dependent_count + Education_Level + 
    Marital_Status + Card_Category + Months_on_book + Total_Relationship_Count + 
    Months_Inactive_12_mon + Contacts_Count_12_mon + Total_Revolving_Bal + 
    Total_Amt_Chng_Q4_Q1 + Total_Trans_Amt + Total_Trans_Ct + 
    Total_Ct_Chng_Q4_Q1, family = "binomial", data = train.smote)
summary(fitmodel)
```

    ## 
    ## Call:
    ## glm(formula = class ~ Gender + Dependent_count + Education_Level + 
    ##     Marital_Status + Card_Category + Months_on_book + Total_Relationship_Count + 
    ##     Months_Inactive_12_mon + Contacts_Count_12_mon + Total_Revolving_Bal + 
    ##     Total_Amt_Chng_Q4_Q1 + Total_Trans_Amt + Total_Trans_Ct + 
    ##     Total_Ct_Chng_Q4_Q1, family = "binomial", data = train.smote)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -3.5566  -0.4234  -0.0385   0.4577   3.0910  
    ## 
    ## Coefficients:
    ##                            Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)               7.744e+00  3.122e-01  24.801  < 2e-16 ***
    ## Gender                   -7.325e-01  6.318e-02 -11.595  < 2e-16 ***
    ## Dependent_count           1.161e-01  2.454e-02   4.730 2.24e-06 ***
    ## Education_Level           4.438e-02  1.513e-02   2.933  0.00335 ** 
    ## Marital_Status            2.585e-01  3.561e-02   7.259 3.91e-13 ***
    ## Card_Category             2.731e-01  8.628e-02   3.165  0.00155 ** 
    ## Months_on_book           -1.047e-02  3.909e-03  -2.679  0.00738 ** 
    ## Total_Relationship_Count -4.397e-01  2.193e-02 -20.053  < 2e-16 ***
    ## Months_Inactive_12_mon    6.148e-01  3.241e-02  18.969  < 2e-16 ***
    ## Contacts_Count_12_mon     5.398e-01  2.993e-02  18.038  < 2e-16 ***
    ## Total_Revolving_Bal      -9.112e-04  3.321e-05 -27.433  < 2e-16 ***
    ## Total_Amt_Chng_Q4_Q1     -6.457e-01  1.519e-01  -4.251 2.12e-05 ***
    ## Total_Trans_Amt           5.989e-04  1.889e-05  31.696  < 2e-16 ***
    ## Total_Trans_Ct           -1.383e-01  3.134e-03 -44.142  < 2e-16 ***
    ## Total_Ct_Chng_Q4_Q1      -3.221e+00  1.542e-01 -20.887  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 16281.1  on 11744  degrees of freedom
    ## Residual deviance:  7685.1  on 11730  degrees of freedom
    ## AIC: 7715.1
    ## 
    ## Number of Fisher Scoring iterations: 6

``` r
anova(fitmodel, test = "Chisq")
```

    ## Analysis of Deviance Table
    ## 
    ## Model: binomial, link: logit
    ## 
    ## Response: class
    ## 
    ## Terms added sequentially (first to last)
    ## 
    ## 
    ##                          Df Deviance Resid. Df Resid. Dev  Pr(>Chi)    
    ## NULL                                     11744    16281.1              
    ## Gender                    1     35.6     11743    16245.5 2.458e-09 ***
    ## Dependent_count           1      7.5     11742    16238.0  0.006045 ** 
    ## Education_Level           1      9.2     11741    16228.8  0.002413 ** 
    ## Marital_Status            1      5.2     11740    16223.6  0.023124 *  
    ## Card_Category             1      0.3     11739    16223.4  0.609540    
    ## Months_on_book            1      2.8     11738    16220.6  0.097006 .  
    ## Total_Relationship_Count  1    518.3     11737    15702.3 < 2.2e-16 ***
    ## Months_Inactive_12_mon    1    586.1     11736    15116.3 < 2.2e-16 ***
    ## Contacts_Count_12_mon     1   1029.4     11735    14086.8 < 2.2e-16 ***
    ## Total_Revolving_Bal       1   1166.5     11734    12920.4 < 2.2e-16 ***
    ## Total_Amt_Chng_Q4_Q1      1    290.9     11733    12629.4 < 2.2e-16 ***
    ## Total_Trans_Amt           1    652.1     11732    11977.3 < 2.2e-16 ***
    ## Total_Trans_Ct            1   3774.4     11731     8202.9 < 2.2e-16 ***
    ## Total_Ct_Chng_Q4_Q1       1    517.8     11730     7685.1 < 2.2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
pred <- predict(fitmodel, test, type="response")
pred1 = rep(0,2531)
pred1[pred>0.2] = 1
```

``` r
cmlr = confusionMatrix(as.factor(pred1), as.factor(test$label), positive="1")
round(cmlr$byClass["F1"], 4)
```

    ##     F1 
    ## 0.5114

``` r
roc_lr2 = roc(test$label, pred1, plot=TRUE, print.auc=TRUE)
```

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

#### 의사결정나무

``` r
mt = rpart(class ~., data=train.smote, method = "class")
plotcp(mt)
```

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

``` r
mt_prune = prune(mt,cp=0.036)
library(rpart.plot)
prp(mt_prune, type = 4, extra = 2)
```

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

``` r
printcp(mt_prune)
```

    ## 
    ## Classification tree:
    ## rpart(formula = class ~ ., data = train.smote, method = "class")
    ## 
    ## Variables actually used in tree construction:
    ## [1] Total_Relationship_Count Total_Revolving_Bal      Total_Trans_Amt         
    ## [4] Total_Trans_Ct          
    ## 
    ## Root node error: 5820/11745 = 0.49553
    ## 
    ## n= 11745 
    ## 
    ##         CP nsplit rel error  xerror      xstd
    ## 1 0.552234      0   1.00000 1.00000 0.0093101
    ## 2 0.061168      1   0.44777 0.44794 0.0077383
    ## 3 0.042726      3   0.32543 0.32904 0.0068788
    ## 4 0.036000      6   0.19725 0.20206 0.0055895

``` r
mt_prune$variable.importance
```

    ##           Total_Trans_Ct          Total_Trans_Amt      Total_Revolving_Bal 
    ##               2716.39665               2662.31008               1006.23568 
    ##      Total_Ct_Chng_Q4_Q1    Avg_Utilization_Ratio     Total_Amt_Chng_Q4_Q1 
    ##                891.50094                637.25832                482.60921 
    ## Total_Relationship_Count    Contacts_Count_12_mon   Months_Inactive_12_mon 
    ##                443.38971                365.14922                217.22572 
    ##          Avg_Open_To_Buy             Credit_Limit           Marital_Status 
    ##                198.21279                154.55180                 89.37666 
    ##            Card_Category                   Gender 
    ##                 71.96393                 57.44834

``` r
tree.p = predict(mt_prune, test, type = "class")
cmt = confusionMatrix(tree.p, as.factor(test$label), positive ="1")
round(cmt$byClass["F1"], 4)
```

    ##     F1 
    ## 0.7106

``` r
test$tp1= tree.p
roc_t= roc(response= test$label, predictor = factor(test$tp1, ordered=TRUE), plot=TRUE, print.auc=TRUE)
```

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

#### 랜덤포레스트

``` r
trControl <- trainControl(method = "cv",
    number = 10,
    search = "grid")
```

``` r
set.seed(1234)
rf1 = train(class ~ .,data=train.smote, method="rf",metric ="Accuracy",trControl = trControl)
print(rf1)
```

    ## Random Forest 
    ## 
    ## 11745 samples
    ##    19 predictor
    ##     2 classes: '0', '1' 
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold) 
    ## Summary of sample sizes: 10570, 10571, 10570, 10570, 10570, 10571, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   mtry  Accuracy   Kappa    
    ##    2    0.9772675  0.9545322
    ##   10    0.9793107  0.9586223
    ##   19    0.9732651  0.9465292
    ## 
    ## Accuracy was used to select the optimal model using the largest value.
    ## The final value used for the model was mtry = 10.

``` r
plot(rf1)
```

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-31-1.png)<!-- -->

``` r
varImp(rf1)
```

    ## rf variable importance
    ## 
    ##                           Overall
    ## Total_Trans_Ct           100.0000
    ## Total_Trans_Amt           76.2130
    ## Total_Revolving_Bal       36.4642
    ## Total_Ct_Chng_Q4_Q1       24.5696
    ## Total_Relationship_Count  19.9446
    ## Total_Amt_Chng_Q4_Q1      11.8975
    ## Months_Inactive_12_mon    11.2710
    ## Avg_Utilization_Ratio      7.6198
    ## Marital_Status             5.9142
    ## Contacts_Count_12_mon      5.7218
    ## Customer_Age               4.9626
    ## Gender                     4.9537
    ## Credit_Limit               3.8810
    ## Avg_Open_To_Buy            3.6827
    ## Months_on_book             2.3318
    ## Dependent_count            1.7898
    ## Education_Level            1.0440
    ## Income_Category            0.5722
    ## Card_Category              0.0000

``` r
rfpred = predict(rf1, test)
cmrf = confusionMatrix(rfpred, as.factor(test$label), positive="1"); cmrf
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction    0    1
    ##          0 2504   42
    ##          1   71  421
    ##                                           
    ##                Accuracy : 0.9628          
    ##                  95% CI : (0.9555, 0.9692)
    ##     No Information Rate : 0.8476          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.8596          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.008438        
    ##                                           
    ##             Sensitivity : 0.9093          
    ##             Specificity : 0.9724          
    ##          Pos Pred Value : 0.8557          
    ##          Neg Pred Value : 0.9835          
    ##              Prevalence : 0.1524          
    ##          Detection Rate : 0.1386          
    ##    Detection Prevalence : 0.1619          
    ##       Balanced Accuracy : 0.9409          
    ##                                           
    ##        'Positive' Class : 1               
    ## 

``` r
round(cmrf$byClass["F1"], 4)
```

    ##     F1 
    ## 0.8817

``` r
test$rfp= rfpred
roc_rf= roc(response= test$label, predictor = factor(test$rfp, ordered=TRUE), plot=TRUE, print.auc=TRUE)
```

![](신용카드고객이탈_보고서_files/figure-gfm/unnamed-chunk-35-1.png)<!-- -->

#### XGBoost
