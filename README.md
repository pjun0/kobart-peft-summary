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

## 📊 성능 결과

테스트 데이터 100개에 대한 결과

| 메트릭 | 값 |
|--------|----|
| ROUGE-1 | 약 0.17 |
| ROUGE-2 | 약 0.04 |
| ROUGE-L | 약 0.17 |
| BERTScore (F1) | 0.7853 |

> 모델은 일부 문장에서 원문의 문구를 그대로 반복하거나, 주제를 명확하게 요약하지 못하는 경향이 있음

---

## ✅ 향후 개선 방향

- 추론 시 `length_penalty` 및 `no_repeat_ngram_size` 조정
- 전이 학습을 위한 뉴스 요약 → 논문 요약 방식 시도

---

## 🖼️ 요약 결과

| ![image](https://github.com/user-attachments/assets/3f5001b6-b92c-44bc-8a69-16b724e9a2b2)

