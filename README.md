# credit_prediction

## 데이터 수집
- 아래 url을 통해 신용 등급 확인
  - https://www.korearatings.com/cms/frCmnCon/index.do?MENU_ID=360

- 아래 url을 통해 해당 기업의 재무제표(사업 보고서) 다운
  - https://opendart.fss.or.kr/disclosureinfo/fnltt/singl/main.do  

## 전처리 과정

### processing.ipynb
- 재무제표 PDF를 각 페이지 당 jpg로 변환

### shinhan.ipynb
- 기업의 각 jpg 파일을 easyOCR을 통해 전체 text로 변환
- 전체 text를 정규표현식 작성을 통해 예측에 의미 있는 표현만 json 파일로 저장

## 모델링 과정
- Hugging Face에서 KR-Fin Bert 모델을 가져옴
- KR-Fin BERT의 금융 특화된 encoder를 통해 인코딩
- Fin BERT 논문에서 얘기한 것 처럼 BERT 모델은 동결 시키고 [CLS] 토큰을 가져와 Dense layer를 통해 확률 반환
- 반환 확률을 soft max 함수를 통해 label 예측


## Fine-tuning
- 동결시킨 BERT 외에 마지막 2개의 layer만 fine-tuning 실행
- dense layer 역시 fine-tuning 실행
- 학습 데이터와 평가 데이터를 9:1 비율로 나누어 검증 실행
- batch size : 4 
- learning rate : 1e-5
- epoch : 5
- AdamW 사용, weight decay : 0.01

## 한계점 
2020년 사업 보고서와 신용 등급을 동시에 
