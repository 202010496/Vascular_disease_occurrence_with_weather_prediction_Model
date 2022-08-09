Vascular_disease_occurrence_with_weather_prediction_Model
- 2022-2 project (2022_weather_contest)
- 2022년 기상청 빅데이터 콘테스트 공모작


# 2차 과제 - 기상에 따른 혈관질환 발생 예측 모델 개발

- 제공 데이터: 예보 데이터 (6/12시간강수량, 습도, 풍속, 풍향, 6/12시간적설, 3시간기온, 하늘상태 등), 
              병원 데이터(서울백병원 내 일 단위 혈관질환 발생 환자 수, 환자의 성별, 발생 지역)
- 추가 수집 데이터: 기상관측 데이터 (평균 기온, 평균 최고기온, 평균 최저기온, 평균 습도 등), 미세먼지 데이터
---> 총 15개의 변수 사용

- 파생 변수: 일교차(평균 최고기온 - 평균 최저기온), 강수량((6시간 강수량 + 12시간 강수량)/2), 적설((6시간 적설 + 12시간 적설)/2)
- 데이터 제공 형태: .csv
- 데이터 형태: 예보 데이터의 경우, 일별(yyyymmdd)/시간별(02~23시까지 3시간 단위로 총 8개의 시간) 기상 수치 & 기상관측 및 미세먼지 데이터의 경우, 일별 기상 수치(모든 시간대별 데이터를 평균한 값) 제공
- 예보 데이터 활용: 각 일별 예보 데이터 중 수치(시간대별 대표 기상 수치 군집 시각화 결과)가 두드러지며, 사람들이 주로 활동하는 시간대인 오전 8시 ~ 오후 5시로 선정하여 이에 대한 일별 평균값으로 변환

- 종속변수(예측값): 혈관질환 발생 환자 수
- 독립변수: 그 외 14개 변수

- 활용 모델: 3가지 앙상블 (GradientBoosting, LightGBM, XGBoost Regression)
---> total_weight = (1. / gb_score) + (1. / xgb_score) + (1. / lgb_score)
     real_pred = (pred_gb * (1. / gb_score) + pred_xgb * (1. / xgb_score) + pred_lgb * (1. / lgb_score)) / total_weight
     
     ![image](https://user-images.githubusercontent.com/100951015/183676515-dcbb491f-b3ef-44a1-985f-87f06388b7a1.png)

     
     
     
- 검증 방법: RMSE (모델 정확도, 실제 검증값 대비 정확도)

- 분석 프로세스

    ![image](https://user-images.githubusercontent.com/100951015/183670389-ba629654-ab8f-4826-92ac-b679679e54ec.png)


# 결과
[사용 파라미터]
1) GradientBoosting {'subsample':0.7, 'n_estimators':1000, 'max_depth':3, 'min_samples_leaf':5, 'min_samples_split':2, 'learning_rate':0.1}
2) XGboost {'learning_rate': 0.1, 'max_depth': 3, 'n_estimators':20000,'subsample': 0.7}  
3) LGMB (bagging_fraction=0.72, colsample_bytree=0.72, lambda_l1=0.1, lambda_l2=0.35, learning_rate=0.01, max_depth=7, min_data_in_leaf=8, num_iterations=20000, random_state=42, scale_pos_weight=1.5)

- 마감 결과 (기준 오후 18시): 최종 13위 (총 31팀 참여)

![image](https://user-images.githubusercontent.com/100951015/183677185-fef46506-775c-4b6b-abfa-f1ad3d39a1d6.png)



# 문제점 및 아쉬운 점
- 다량의 제공 데이터 내 결측값 및 누락값 존재
- 기상관측데이터의 경우, 세종과 대전 지역 내 데이터가 부재하므로 학습 및 테스트 데이터인 2012년~2015년 발생 환자 수의 평균값으로 대체
- 혈관질환의 경우, 기상 조건의 변화에 즉각 반응하는 것이 아닌 하루 ~ 이틀 간의 격차를 두고 발생 
  (인과를 제대로 분석하기 위해서는 독립변수와 종속변수 간 이틀 정도의 격차를 두고 결합 및 학습을 시켜야 함) 
- 그러나 결측값 및 누락값이 다량으로 존재해, 이를 제한 시간 내 보간하는데 한계가 있다고 판단하여 시간차를 고려하지 않고 학습을 시킴
- 더 많은 환자 정보가 존재한다면, 기존에 환자의 건강상태가 혈관질환 발생률에 미치는 영향을 함께 고려할 수 있을 것으로 예상
- 미루어진 1차 과제 마감일에 따라 실제 2차 과제 수행 기간이 약 5일로 축소됨, 그 중 데이터 수집 및 결합과 전처리에 3~4일이 소요됨 (약 300개 이상의 .csv 형태의 데이터 존재)




# 활용 방안
- 경고 시스템 구축: 혈관질환에 영향을 미치는 기상 조건 변화를 빠르게 감지할 수 있는 시스템 구축
- 유연한 지역 쉼터 운영: 기상조건 중 하나인 ‘일교차’와 같이 혈관질환에 가장 큰 영향을 미치는 ‘기온’ 변화에 따른 여름철 무더위쉼터 및 겨울철 한파쉼터 운영
- 병원 내 혈관질환 환자 발생 대비 시스템 구축:  실시간 기상조건 변화 정보를 기반으로 발생 가능한 환자 수를 예측한 결과, 환자의 수가 많은 경우 이를 대비하여 응급 혈관질환 환자 수용 및 응급처치를 위한 대책 및 환경 마련
