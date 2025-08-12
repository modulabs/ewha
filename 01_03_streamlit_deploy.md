# [배포] Streamlit을 활용한 AI 서비스 배포

- **주관:** AI 전문 강사 조해창
- **학습 목표:** Python 지식만으로 간단하게 웹 UI를 만들어 내가 만든 AI 모델을 다른 사람에게 공유하는 방법을 배웁니다.

## 주요 내용

### 1. Streamlit 소개 및 기본 사용법

*   **Streamlit이란?**
    *   데이터 과학자와 머신러닝 엔지니어를 위한 오픈소스 Python 라이브러리.
    *   간단한 Python 스크립트만으로 인터랙티브한 웹 애플리케이션을 빠르게 개발할 수 있도록 지원.
    *   프론트엔드 개발 지식(HTML, CSS, JavaScript) 없이도 멋진 UI 구현 가능.
*   **주요 특징:**
    *   **간편성:** 몇 줄의 코드로 웹 앱 생성.
    *   **빠른 개발:** 코드 변경 시 실시간으로 UI 업데이트.
    *   **데이터 중심:** 데이터 시각화, 모델 결과 공유에 최적화.
    *   **다양한 위젯:** 슬라이더, 버튼, 텍스트 입력, 파일 업로드 등 풍부한 UI 컴포넌트 제공.
*   **기본 사용법:**
    *   **설치:** `pip install streamlit`
    *   **간단한 앱 만들기:**
        ```python
        # app.py
        import streamlit as st

        st.title("나의 첫 Streamlit 앱")
        st.write("안녕하세요, Streamlit 세계에 오신 것을 환영합니다!")

        name = st.text_input("이름을 입력하세요:")
        if name:
            st.write(f"환영합니다, {name}님!")

        number = st.slider("숫자를 선택하세요", 0, 100, 50)
        st.write(f"선택한 숫자: {number}")
        ```
    *   **앱 실행:** `streamlit run app.py`
    *   **주요 API:**
        *   `st.write()`: 텍스트, 데이터프레임, 차트 등 다양한 콘텐츠 출력.
        *   `st.text_input()`, `st.button()`, `st.slider()`: 사용자 입력 위젯.
        *   `st.sidebar()`: 사이드바 생성.
        *   `st.columns()`: 레이아웃 분할.
        *   `st.file_uploader()`: 파일 업로드 기능.
*   **데모:** 간단한 Streamlit 앱을 직접 코딩하고 실행하여 기본 동작 방식 시연.

### 2. 오늘 만든 RAG 에이전트를 Streamlit 웹 앱으로 만들기

*   **목표:** 이전 세션에서 만든 간단한 Q&A 봇 또는 RAG 에이전트(다음 세션에서 만들 예정)를 Streamlit 웹 인터페이스를 통해 사용자들이 쉽게 접근하고 상호작용할 수 있도록 배포.
*   **구현 단계:**
    *   **Streamlit 앱 구조 설계:**
        *   `app.py` 파일 생성.
        *   필요한 라이브러리 임포트 (streamlit, anthropic 등).
        *   Claude API 키 환경 변수 설정.
    *   **UI 구성:**
        *   앱 제목, 설명 추가.
        *   사용자 질문을 입력받을 `st.text_area` 또는 `st.text_input` 위젯 추가.
        *   질문 전송 버튼 (`st.button`) 추가.
        *   AI 응답을 표시할 `st.write` 또는 `st.info` 위젯 추가.
    *   **백엔드 로직 연동:**
        *   이전 세션에서 구현한 Claude API 호출 함수를 `app.py` 내에 통합.
        *   버튼 클릭 시 사용자 입력을 받아 Claude API로 전송하고, 응답을 UI에 표시.
        *   로딩 스피너 (`st.spinner`)를 사용하여 AI 응답 대기 중임을 사용자에게 알림.
*   **코드 예시 (Q&A 봇 연동):**
    ```python
    import streamlit as st
    import anthropic
    import os

    # Claude API 키 설정 (환경 변수에서 불러오기)
    ANTHROPIC_API_KEY = os.environ.get("ANTHROPIC_API_KEY")
    if not ANTHROPIC_API_KEY:
        st.error("ANTHROPIC_API_KEY 환경 변수가 설정되지 않았습니다.")
        st.stop()

    client = anthropic.Anthropic(api_key=ANTHROPIC_API_KEY)

    st.title("AI Q&A 봇 (Streamlit)")
    st.write("궁금한 점을 질문해주세요. Claude가 답변해 드립니다.")

    user_question = st.text_area("질문 입력:", height=100)

    if st.button("질문하기"):
        if user_question:
            with st.spinner("Claude가 답변을 생성 중입니다..."):
                try:
                    message = client.messages.create(
                        model="claude-3-opus-20240229", # 또는 사용 가능한 모델
                        max_tokens=1024,
                        messages=[
                            {"role": "user", "content": user_question}
                        ]
                    )
                    st.info(message.content[0].text)
                except Exception as e:
                    st.error(f"API 호출 중 오류 발생: {e}")
        else:
            st.warning("질문을 입력해주세요.")

    st.markdown("---")
    st.caption("이 앱은 Streamlit과 Claude API를 사용하여 구축되었습니다.")
    ```
*   **실행 및 테스트:** 로컬에서 Streamlit 앱을 실행하여 정상 작동 여부 확인.
*   **배포 옵션 논의 (선택 사항):** Streamlit Cloud, Hugging Face Spaces, 또는 자체 서버에 배포하는 방법 간략 소개.