# KoBART 논문 요약 모델 학습 프로젝트

이 프로젝트는 한국어 논문 전문을 입력으로 받아 핵심 내용을 요약하는 KoBART 기반의 텍스트 요약 모델을 학습한 작업입니다. 

총 3개의 주요 노트북을 통해 데이터 전처리, 모델 학습, PEFT(LORA) 적용 실험을 단계적으로 수행했습니다.

---

## 📁 노트북 구성

| 노트북 이름 | 설명 |
|-------------|------|
| `dataset.ipynb` | 원본 JSON 데이터를 토크나이즈하고 학습용 데이터셋으로 저장 |
| `kobart-peft-summary.ipynb` | LoRA 기반의 PEFT 기법을 적용하여 파라미터 효율적 미세조정 수행 |

---

## 📚 데이터셋 전처리 방식

- 사용 데이터: AI Hub 한국어 논문 요약 데이터
- 원문: `original_text`, 요약: `summary_text`
- 토크나이저: `PreTrainedTokenizerFast` from `gogamza/kobart-base-v2`
- `max_length=512`로 truncation 및 padding 적용
- 최종 데이터셋은 HuggingFace `Dataset` 포맷으로 저장 후 학습에 사용

---

## 🧠 모델 학습 설정

| 항목 | 값 |
|------|----|
| Base model | `gogamza/kobart-base-v2` |
| 학습 에폭 | 3 (PEFT 실험) |
| 배치 사이즈 | 32 |
| Learning Rate | 5e-5 |
| 학습 장비 | Kaggle P100 |

---

## 🧪 PEFT (LoRA) 실험

- `peft`, `transformers`, `accelerate` 라이브러리 활용
- LoraConfig 설정:
  ```python
  LoraConfig(
      r=8,
      lora_alpha=32,
      lora_dropout=0.1,
      task_type=TaskType.SEQ_2_SEQ_LM
  )
  ```
- 학습 파라미터 수: 전체 대비 약 0.35%만 학습

---

### 📊 테스트 결과 (100개 샘플 기준)

| 메트릭            | 값     |
|-------------------|--------|
| **ROUGE-1**       | 0.1729 |
| **ROUGE-2**       | 0.0407 |
| **ROUGE-L**       | 0.1697 |
| **BERTScore (F1)** | 0.7853 |

---

### 🧠 평가 해석

- 모델은 일부 문장에서 **원문 문장을 반복하거나**, **핵심 정보를 충분히 요약하지 못하는 경우**가 관찰됨

  ![image](https://github.com/user-attachments/assets/d92abe3a-9e9f-46d5-941c-231a567b7555)

- 그럼에도 불구하고, **의미적으로는 정답 요약과 유사한 내용을 생성한 사례**가 다수 존재함

> ROUGE 점수는 상대적으로 낮게 나타났으나,  
> BERTScore는 의미적 유사성을 기반으로 한 평가 결과를 반영하며 높은 점수를 기록함

이는 모델이 **정확히 같은 단어를 사용하지 않더라도**,  
**핵심 내용을 의미적으로 잘 요약하고 있음을 시사함**

| 지표         | 평가 방식 |
|--------------|------------|
| **ROUGE**    | 정답 요약과 생성 요약 간의 **n-gram 단위 단어 중복률**을 기준으로 평가 (표현이 다르면 감점) |
| **BERTScore** | 문장 간 **의미적 유사성**을 임베딩 기반으로 평가 (다양한 표현도 인정) |

> 논문 요약처럼 표현의 다양성이 중요한 태스크에서는  
> **BERTScore가 모델 성능을 더 정확하게 반영할 수 있음**


---

## ✅ 향후 개선 방향

- 추론 시 `length_penalty` 및 `no_repeat_ngram_size` 조정
- 전이 학습을 위한 뉴스 요약 → 논문 요약 방식 시도

---

## 🖼️ 요약 결과

| ![image](https://github.com/user-attachments/assets/3f5001b6-b92c-44bc-8a69-16b724e9a2b2)

