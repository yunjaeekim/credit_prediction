# 📚 재무제표를 통한 기업의 신용 등급 예측 프레임워크

## 🗂️ 데이터 수집
- 아래 url을 통해 신용 등급 확인
  - https://www.korearatings.com/cms/frCmnCon/index.do?MENU_ID=360

- 아래 url을 통해 해당 기업의 재무제표(사업 보고서) 다운
  - https://opendart.fss.or.kr/disclosureinfo/fnltt/singl/main.do  

## 🛠️ 전처리 과정

### processing.ipynb
- 재무제표 PDF를 각 페이지 당 jpg로 변환

### easyOCR.ipynb
- 기업의 각 jpg 파일을 easyOCR을 통해 전체 text로 변환

### KR_Fin_BERT
- 전체 text를 정규표현식 작성을 통해 예측에 의미 있는 표현만 json 파일로 저장

## 🤖 모델링 과정
- Hugging Face에서 KR-Fin Bert 모델을 가져옴
- KR-Fin BERT의 금융 특화된 encoder를 통해 인코딩
- Fin BERT 논문에서 얘기한 것 처럼 BERT 모델은 동결 시키고 [CLS] 토큰을 가져와 Dense layer를 통해 확률 반환
- 반환 확률을 soft max 함수를 통해 label 예측


## 🎯 Fine-tuning
- 동결시킨 BERT 외에 마지막 2개의 layer만 fine-tuning 실행
- dense layer 역시 fine-tuning 실행
- 학습 데이터와 평가 데이터를 9:1 비율로 나누어 검증 실행
- batch size : 4 
- learning rate : 1e-5
- epoch : 5
- AdamW 사용, weight decay : 0.01

## 📝 한계점 
이번 연구를 진행하면서 가장 아쉬웠던 점은 데이터가 너무 부족했다는 것입니다. Google Colab의 GPU 리소스 한계로 인해 2020년도의 데이터만 활용할 수밖에 없었고, 그마저도 사업보고서와 신용등급을 함께 구할 수 있는 기업들의 데이터만 수집하다 보니 겨우 34개(훈련용 26개, 테스트용 8개)의 데이터만 확보할 수 있었습니다. 이렇게 적은 데이터로는 KR-FinBERT 모델을 제대로 fine-tuning하기 어려웠고, 결국 모델의 성능도 기대에 미치지 못했습니다.

만약 더 높은 성능의 GPU를 활용할 수 있다면 2020년부터 2023년까지의 데이터를 모두 활용할 수 있을 것이고, 그렇다면 약 120~140개 정도의 데이터를 확보할 수 있을 것으로 예상됩니다. 물론 이 정도로도 딥러닝 모델을 학습시키기에는 여전히 부족하지만, 지금보다는 더 나은 결과를 얻을 수 있을 것 같습니다.

앞으로는 분기별 보고서나 다른 공시자료들도 활용하는 방안을 고려해볼 수 있을 것 같습니다. 또한 텍스트 데이터뿐만 아니라 재무지표 같은 수치 데이터도 함께 활용한다면, 모델의 성능을 한층 더 끌어올릴 수 있지 않을까 기대해봅니다.
