# [실습] RAG 개념 및 바이브 코딩으로 적용하기

- **주관:** AI 전문 강사 조해창
- **학습 목표:** Retrieval-Augmented Generation(RAG)의 개념을 이해하고, 외부 문서를 기반으로 답변하는 AI 에이전트를 구현합니다.

## 주요 내용

### 1. RAG의 작동 원리와 중요성

*   **RAG (Retrieval-Augmented Generation) 개념:**
    *   **정의:** LLM(Large Language Model)이 답변을 생성하기 전에 외부 지식 저장소에서 관련 정보를 검색(Retrieval)하여, 이를 기반으로 답변을 생성(Generation)하는 기술.
    *   **필요성:**
        *   **환각(Hallucination) 문제 해결:** LLM이 사실과 다른 정보를 생성하는 문제 완화.
        *   **최신 정보 반영:** LLM의 학습 데이터에 없는 최신 정보를 활용하여 답변의 정확성 향상.
        *   **출처 명시:** 답변의 근거가 되는 정보의 출처를 명확히 제시하여 신뢰성 확보.
        *   **도메인 특화:** 특정 도메인(기업 내부 문서, 전문 분야 지식)에 특화된 AI 서비스 구축 가능.
*   **작동 원리:**
    1.  **질문 입력:** 사용자가 질문을 입력.
    2.  **정보 검색 (Retrieval):** 질문과 관련된 정보를 외부 지식 저장소(문서, 데이터베이스 등)에서 검색. 이때 질문의 의미를 이해하고 가장 관련성 높은 문서를 찾아내는 것이 중요.
    3.  **정보 증강 (Augmentation):** 검색된 정보를 LLM의 입력 프롬프트에 추가하여 LLM이 답변을 생성할 때 참고하도록 함.
    4.  **답변 생성 (Generation):** 증강된 정보를 바탕으로 LLM이 질문에 대한 답변을 생성.
*   **RAG의 중요성:** 실제 비즈니스 환경에서 LLM을 활용한 정확하고 신뢰성 있는 AI 서비스 구축의 핵심 기술.

### 2. Vector DB의 기본 개념

*   **Vector DB (Vector Database)란?**
    *   **정의:** 텍스트, 이미지, 오디오 등 비정형 데이터를 벡터(Vector) 형태로 변환하여 저장하고, 이 벡터들 간의 유사도를 기반으로 검색하는 데 최적화된 데이터베이스.
    *   **왜 필요한가?:** RAG 시스템에서 질문과 가장 유사한 문서를 효율적으로 찾아내기 위해 사용. 일반적인 관계형 데이터베이스로는 의미 기반 검색이 어려움.
*   **임베딩(Embedding) 개념:**
    *   **정의:** 단어, 문장, 문서 등 자연어 데이터를 고차원 벡터 공간의 숫자 배열(벡터)로 변환하는 과정. 이때 의미적으로 유사한 데이터는 벡터 공간에서 가까운 위치에 배치됨.
    *   **역할:** LLM이 텍스트의 의미를 이해하고, 벡터 DB가 의미 기반 검색을 수행할 수 있도록 함.
*   **유사도 검색 (Similarity Search):**
    *   **원리:** 질문 벡터와 벡터 DB에 저장된 문서 벡터들 간의 거리를 계산하여 가장 가까운(유사한) 벡터를 찾아내는 과정. 코사인 유사도(Cosine Similarity) 등이 주로 사용됨.
*   **주요 Vector DB 종류 (간략 소개):** Pinecone, Weaviate, Chroma, FAISS 등.

### 3. 간단한 텍스트 파일을 기반으로 답변하는 RAG 에이전트 구현

*   **구현 목표:** 특정 텍스트 파일(예: 회사 소개서, 제품 설명서)의 내용을 기반으로 질문에 답변하는 AI 에이전트 만들기.
*   **필요 라이브러리:**
    *   `langchain`: LLM 애플리케이션 개발을 위한 프레임워크.
    *   `anthropic`: Claude API 연동.
    *   `chromadb`: 로컬에서 사용할 수 있는 경량 Vector DB.
    *   `tiktoken` 또는 `sentence-transformers`: 텍스트 임베딩을 위한 라이브러리.
*   **구현 단계:**
    1.  **문서 로드:** `TextLoader` 등을 사용하여 텍스트 파일 로드.
    2.  **문서 분할 (Text Splitting):** 긴 문서를 LLM이 처리할 수 있는 작은 청크(chunk)로 분할. `RecursiveCharacterTextSplitter` 사용.
    3.  **임베딩 생성:** 분할된 각 청크를 임베딩 모델(예: `OpenAIEmbeddings` 또는 `HuggingFaceEmbeddings`)을 사용하여 벡터로 변환.
    4.  **Vector DB 저장:** 생성된 벡터와 원본 텍스트 청크를 ChromaDB에 저장.
    5.  **리트리버(Retriever) 생성:** ChromaDB에서 질문과 관련된 문서를 검색하는 리트리버 객체 생성.
    6.  **LLM 연동:** Claude LLM 모델 초기화.
    7.  **RAG 체인 구축:** `RetrievalQA` 또는 `create_retrieval_chain` 등을 사용하여 RAG 워크플로우 구성.
*   **코드 예시:**
    ```python
    import os
    from langchain_community.document_loaders import TextLoader
    from langchain_text_splitters import RecursiveCharacterTextSplitter
    from langchain_community.embeddings import HuggingFaceEmbeddings # 또는 OpenAIEmbeddings
    from langchain_community.vectorstores import Chroma
    from langchain_anthropic import ChatAnthropic # Claude LLM
    from langchain.chains import RetrievalQA

    # 1. 문서 로드 (예시: data.txt 파일)
    # data.txt 파일에 RAG에 사용할 텍스트 내용을 미리 넣어두세요.
    # 예: "이화여자대학교는 1886년에 설립된 한국 최초의 여성 교육기관입니다. ..."
    loader = TextLoader("./data.txt", encoding="utf-8")
    documents = loader.load()

    # 2. 문서 분할
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
    splits = text_splitter.split_documents(documents)

    # 3. 임베딩 모델 선택 (로컬 모델 사용 예시)
    # pip install sentence-transformers
    embeddings = HuggingFaceEmbeddings(model_name="jhgan/ko-sroberta-multitask") # 한국어 임베딩 모델

    # 4. Vector DB 저장 (ChromaDB 사용)
    vectorstore = Chroma.from_documents(documents=splits, embedding=embeddings)

    # 5. 리트리버 생성
    retriever = vectorstore.as_retriever()

    # 6. LLM 연동 (Claude)
    # ANTHROPIC_API_KEY 환경 변수 설정 필요
    llm = ChatAnthropic(model="claude-3-opus-20240229", temperature=0.0) # 또는 claude-3-sonnet-20240229

    # 7. RAG 체인 구축
    qa_chain = RetrievalQA.from_chain_type(
        llm,
        chain_type="stuff", # 검색된 모든 문서를 LLM에 한 번에 전달
        retriever=retriever,
        return_source_documents=True # 답변과 함께 출처 문서 반환
    )

    # 질문 예시
    question = "이화여자대학교는 언제 설립되었나요?"
    result = qa_chain.invoke({"query": question})

    print(f"질문: {question}")
    print(f"답변: {result['result']}")
    if result['source_documents']:
        print("\n--- 출처 문서 ---")
        for doc in result['source_documents']:
            print(f"페이지: {doc.metadata.get('page', 'N/A')}, 내용: {doc.page_content[:100]}...")
    ```
*   **실습:**
    *   `data.txt` 파일 생성 및 내용 입력.
    *   필요 라이브러리 설치.
    *   제공된 코드 실행 및 테스트.
    *   다양한 질문으로 RAG 에이전트의 답변 확인.
*   **개선 방향 논의:**
    *   더 복잡한 문서 형식(PDF, 웹페이지) 처리.
    *   더 큰 규모의 데이터셋 관리.
    *   성능 최적화 및 캐싱.
    *   사용자 인터페이스(Streamlit 등) 연동.