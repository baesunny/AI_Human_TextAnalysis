# AI vs Human Text Analysis

한국어 인간 작성 텍스트와 AI 생성 텍스트를 주제별로 수집·정제하고, 형태소 분석과 임베딩 기반 비교 분석을 수행한 텍스트 분석 프로젝트입니다. 경제학, 법률·법학, 교육학, 자연과학, 천문학 5개 주제에 대해 균형 잡힌 10만 문장 데이터셋을 구축하고, 두 텍스트 집단의 언어적 차이를 정량적으로 비교했습니다.

## 프로젝트 개요

이 프로젝트는 동일 주제·유사 문장 길이 조건에서 인간 작성 문장과 AI 생성 문장을 나란히 비교할 수 있는 분석용 코퍼스를 만드는 것을 목적으로 합니다.

데이터 수집, AI 텍스트 생성, 최종 데이터셋 통합, 형태소 분석, 문장·단어 임베딩 비교까지 전 과정이 완료된 상태입니다.

주요 분석 흐름은 다음과 같습니다.

1. 한국어 도서 말뭉치 JSON에서 인간 작성 문장 추출 및 주제별 샘플링
2. OpenAI API를 활용한 주제별 AI 설명형 문장 생성
3. 인간·AI 데이터 통합 및 10만 문장 최종 데이터셋 구성
4. KoNLPy 기반 형태소·품사·명사 빈도 분석
5. 문장 임베딩, UMAP 시각화, FastText 단어 임베딩, t-SNE 비교 분석

## 분석 대상

| 구분 | 내용 |
| --- | --- |
| 인간 텍스트 | 한국어 도서 말뭉치(BOOK_CORPUS) JSON 문장 |
| AI 텍스트 | OpenAI API 기반 주제별 설명형 문장 생성 결과 |
| 분석 주제 | 경제학(320), 법률·법학(360), 교육학(370), 자연과학(400), 천문학(440) |
| 주요 텍스트 필드 | text, word_count, length_bin, topic_code, topic_name, source |
| source 구분 | human, ai |

## 데이터 규모

| 데이터 | 행 수 | 설명 |
| ---: | ---: | --- |
| `data/final/final_human_ai_100k.csv` | 100,000 | 인간 5만 + AI 5만 통합 최종 데이터셋 (저장소 포함) |
| `data/results/morph/` | 4 CSV | 형태소 분석 통계 결과 (저장소 포함) |
| `data/results/embedding/` | 6 CSV | FastText 기반 단어 유사도·빈도 비교 결과 (저장소 포함) |
| `data/processed/` | - | 중간 처리 CSV (노트북 재실행 시 생성, Git 제외) |
| `data/raw/human/` | - | 원천 JSON 말뭉치 (별도 확보, Git 제외) |
| `data/results/embeddings/` | - | 문장 임베딩 벡터 (노트북 재실행 시 생성, Git 제외) |

최종 데이터셋은 주제별 1만 문장, source별 5만 문장으로 균형 있게 구성되어 있습니다.

## 주요 기능

### 인간 데이터 준비

- 도서 말뭉치 JSON 파일에서 문장 단위 텍스트 추출
- 주제 코드별 데이터프레임 생성 및 CSV 저장
- 문장 길이(어절 수) 구간 분포를 유지하는 층화 샘플링
- 주제별 1만 문장 샘플링 후 5만 문장 통합 데이터 생성

### AI 데이터 생성

- 주제별 프롬프트, 문체, 문장 길이, temperature를 다양화하여 설명형 문장 생성
- 배치 단위 생성 및 중간 저장 로직 구현
- 병렬 처리 버전으로 주제별 1만 문장 생성
- 중복 제거 및 길이 구간 정리 후 AI 5만 문장 통합

### 텍스트 비교 분석

- KoNLPy `Okt` 기반 형태소·품사·명사 추출
- source(human/ai)별 토큰 수, 품사 비율, 명사 빈도 비교
- 문장 임베딩 생성 및 UMAP 2차원 시각화
- human/ai 각각 FastText 모델 학습
- 주제별 핵심 단어의 빈도·유사어·t-SNE 비교

## 디렉토리 구조

```text
AI_Human_TextAnalysis/
├── notebooks/
│   ├── 01_human_data_preparation.ipynb   # 인간 데이터 추출·샘플링
│   ├── 02_ai_data_generation.ipynb       # AI 문장 생성 및 통합
│   └── 03_text_analysis.ipynb            # 형태소·임베딩 비교 분석
├── data/
│   ├── final/
│   │   └── final_human_ai_100k.csv       # 최종 통합 데이터셋 (Git 포함)
│   └── results/
│       ├── morph/                        # 형태소 분석 통계 CSV (Git 포함)
│       └── embedding/                    # FastText 단어 비교 CSV (Git 포함)
├── .gitignore
├── requirements.txt
└── README.md
```

용량이 큰 중간 산출물(`data/processed/`, `data/raw/`, 문장 임베딩 NPY, FastText 모델, morph_tokens.pkl 등)은 `.gitignore`로 제외되어 있습니다. 분석을 처음부터 재현하려면 노트북을 순서대로 실행하면 됩니다.

## 파일 설명

| 경로 | 설명 |
| --- | --- |
| `notebooks/01_human_data_preparation.ipynb` | JSON 말뭉치 로드, 문장 정제, 길이 구간 유지 샘플링, 인간 5만 문장 생성 |
| `notebooks/02_ai_data_generation.ipynb` | OpenAI API 기반 AI 문장 생성, 주제별 병렬 처리, human/ai 통합 |
| `notebooks/03_text_analysis.ipynb` | 형태소 분석, 문장 임베딩, UMAP, FastText, t-SNE 비교 분석 |
| `data/final/final_human_ai_100k.csv` | 분석에 사용한 최종 10만 문장 데이터셋 |
| `data/results/morph/` | 토큰·품사·명사 빈도 통계 CSV |
| `data/results/embedding/` | 단어 유사도·빈도 비교표 CSV |
| `data/processed/`, `data/raw/`, `data/results/embeddings/` | 노트북 재실행으로 생성되는 대용량 중간 파일 (Git 제외) |
| `requirements.txt` | 분석 환경 재현을 위한 Python 패키지 목록 |

## 사용 기술

- Python, Jupyter Notebook
- pandas, numpy, matplotlib
- KoNLPy (Okt)
- OpenAI API
- sentence-transformers
- umap-learn, scikit-learn
- gensim (FastText)

## 실행 방법

```bash
pip install -r requirements.txt
jupyter notebook
```

AI 데이터 생성 노트북(`02_ai_data_generation.ipynb`)을 실행하려면 환경 변수 `OPENAI_API_KEY`를 설정해야 합니다.

분석 흐름은 아래 순서로 확인할 수 있습니다.

1. `notebooks/01_human_data_preparation.ipynb`에서 인간 데이터 추출·샘플링 과정 확인
2. `notebooks/02_ai_data_generation.ipynb`에서 AI 문장 생성 및 최종 통합 과정 확인
3. `notebooks/03_text_analysis.ipynb`에서 형태소·임베딩 기반 human/ai 비교 분석 확인

## 프로젝트 결과

- 5개 학문 주제에 대해 인간 5만 문장, AI 5만 문장으로 구성된 균형 데이터셋을 구축했습니다.
- 문장 길이 구간을 유지한 채 주제별 1만 문장씩 샘플링·생성하여 비교 가능한 조건을 맞췄습니다.
- 형태소·품사·명사 빈도 분석을 통해 human/ai 텍스트의 어휘 사용 차이를 확인했습니다.
- 문장 임베딩과 UMAP, FastText와 t-SNE를 활용해 주제별 단어 사용 패턴과 의미 공간 차이를 비교했습니다.
- 원천 데이터, 중간 처리 결과, 최종 분석 산출물을 단계별 디렉터리로 정리해 분석 과정을 추적할 수 있도록 구성했습니다.
