대회명: 2022 날씨 빅데이터 콘테스트 과제 1-1 
주제: 기상위성 자료를 활용한 여름철 자외선 산출 기술 모델 개발
수상명: 최우수상 (환경부장관상)
주관처: 기상청
-
수상 일자(년/월): 2022/08
역할: 데이터 수집/분석, 모델 개발
1) R을 활용하여 '기상청 날씨 마루' 외부 데이터 수집
2) 계절별 자외선 지수 상관관계 분석
3) 모델 개발 및 성능 비교


# 데이터 피쳐 분석 및 전처리 
<img width="560" height="420" alt="uv" src="https://github.com/user-attachments/assets/e2403044-4d16-4e59-8ad7-6053ad2122f0" />
- 5, 24 feature 및 7~22 feature 간 상관성이 높음 → 파생 변수 2개 생성 (mean_5_24, mean_7_22)
- 종속 변수 6 feature (UV (=자외선 지수)) 결측치 제거 수행


## 사용된 정규화 기법
- L1 or L2 regularization
- Dropout
- Learning rate decay
- 5-fold Cross validation

## RMSE 성능 결과
CNN 0.75
xgboost 0.66
tabnet 0.65
lgbm 0.61

## 데이터셋 관리
contest-UV_new 폴더에 20,21년도 6월 uv범위 및 비율 csv파일을 데이터로 활용
4가지 버전의 데이터 (v1~v4)를 활용하여 모델 개발

1. 기존 44개 변수 테이블 (v1)
2. 상관성이 높은 변수들에 대해 평균을 취한 열을 추가한 테이블 - rolling 기법 추가 (v2)
3. 외부 데이터 (34~37번 열)로 값을 갱신한 후 평균을 취한 테이블 (v3)
4. NDVI, NDWI 열이 추가된 테이블 (v4)

- v1
**Feature** : 'Lat','SolarZA','SateZA','ESR','Band1','Band2','Band3','Band4','Band6','Band7', '30daysBand3','30daysBand13','GK2A-LST','insitu-TD','insitu-TG', 'insitu-HM','insitu-PS','insitu-TED0.05','insitu-TED0.1','insitu-TED0.2', 'insitu-TED0.3','insitu-TED0.5','insitu-TED1.0','insitu-TED1.5','insitu-TED5.0'
    - 기존 44개 변수 중 일부분만 선정

- v2
: v1에서 NDVI/NDWI 열 추가, 상관성이 높은 변수(40~42, 43~44)에 대해 평균을 취한 열 추가

- v3
: v2에서 rolling 기법 추가

- v4
: Grouping (+Rolling) 적용 및 일부 피쳐 제거 (날짜 데이터, 관측소 위치, 위도, 경도)


| 모델 | loss 값 (mse) | 버전  | 사용 변수 |
| --- | --- | --- | --- |
| FFNN-1 | 2.667 | v2 (v2_1) | ‘Band1’,'SolarZA', 'SateZA', 'ESR','Height', 'LandType', 'insitu-HM', 'insitu-TD', 'insitu-TG',’insitu-TED0.05’,’insitu-TED0.1’, 'mean_6_23', 'mean_34_37', 'mean_38_39', 'mean_40_42', 'mean_43_44’ |
| FFNN-2 |  |  |  |
| Xgboost | 1.791875 | v4 | 'Band1', 'SolarZA', 'SateZA', 'ESR', 'Height', 'LandType', 'insitu-HM',
'insitu-TD', 'insitu-TG', 'insitu-TED0.05', 'mean_6_23', 'mean_38_39',
'mean_40_42', 'NDVI', 'NDWI’ |
| CNN | 1.426 | v2 | 'GK2A-LST', 'SolarZA', 'SateZA', 'ESR','Height', 'LandType', 'insitu-HM', 'insitu-TD', 'insitu-TG', 'mean_6_23', 'mean_34_37', 'mean_38_39', 'mean_40_42', 'mean_43_44’ |
| Xgboost  | 0.215817(대회 검증)
 | v4 | 'Band1', 'Band14', 'Band15', 'SolarZA', 'SateZA', 'ESR', 'Height', 'LandType', 'insitu-HM', 'insitu-TD', 'insitu-TG', 'insitu-TED0.05', 'NDVI', 'NDWI’ |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |

이후, v4 모델에서 아래 피쳐 제거 및 외부 데이터 (34~37번 열)로 값을 갱신한 후 평균을 취한 열을 추가하여 최종 제출하였
1. 6번~23번 변수
2. 34번~37번 변수
3. 38~39번 변수 
4. 40~42번 변수 
5. 43~44번 변수 
6. 위도
7. 경도
8. 위치
9. 시간
10. 날짜 데이터

