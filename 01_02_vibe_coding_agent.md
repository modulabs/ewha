# [실습] 바이브 코딩과 AI 에이전트 구축

- **주관:** AI 전문 강사 조해창
- **학습 목표:** VSCode 환경에서 Claude API를 활용하여 직접 코드를 작성하고, 간단한 AI 에이전트의 작동 원리를 체험합니다.

## 주요 내용

### 1. 바이브 코딩(Vibe Coding)이란? (개발 환경 세팅)

*   **개념 설명:**
    *   **바이브 코딩의 정의:** 개발자가 코딩에 몰입할 수 있도록 최적화된 환경을 구축하고, 효율적인 개발 워크플로우를 만드는 방법론. AI 도구를 활용하여 생산성을 극대화하는 것을 포함.
    *   **왜 바이브 코딩인가?:** 개발 생산성 향상, 오류 감소, 코드 품질 개선, 개발자의 피로도 감소.
*   **개발 환경 세팅:**
    *   **VSCode 설치 및 기본 설정:**
        *   VSCode 다운로드 및 설치 (Windows/macOS/Linux).
        *   기본 테마, 폰트, 아이콘 설정.
        *   필수 확장 프로그램 설치: Python, Pylance, Code Runner, Prettier, GitLens 등.
    *   **Python 환경 설정:**
        *   Python 설치 및 가상 환경(venv) 생성 및 활성화.
        *   `pip`를 이용한 패키지 관리.
    *   **터미널 환경 설정:** VSCode 내장 터미널 사용법, 기본 셸(Bash, Zsh) 설정.
    *   **Git 설치 및 기본 설정:** Git 설치 확인, 사용자 정보 설정 (`git config`).

### 2. VSCode에서 Claude API 연동 및 인증

*   **Claude API 소개:**
    *   Anthropic Claude API의 특징 및 장점 (긴 컨텍스트, 안전성, 추론 능력).
    *   API 사용을 위한 기본 개념 (API 키, 엔드포인트, 요청/응답 형식).
*   **API 키 발급 및 관리:**
    *   Anthropic 개발자 콘솔에서 API 키 발급 과정 안내.
    *   API 키 보안 관리의 중요성 (환경 변수 사용 권장).
*   **Python 라이브러리 설치:**
    *   `anthropic` 라이브러리 설치 (`pip install anthropic`).
*   **VSCode에서 API 연동 실습:**
    *   Python 스크립트 작성: `import anthropic`, API 클라이언트 초기화.
    *   환경 변수에서 API 키 불러오기 (`os.environ.get("ANTHROPIC_API_KEY")`).
    *   간단한 API 호출 예제: `client.messages.create()` 메서드 사용.

### 3. 기본적인 프롬프트 엔지니어링 실습

*   **프롬프트 엔지니어링의 중요성:** AI 모델의 성능을 최대한 끌어내기 위한 효과적인 질문 및 지시 작성 기술.
*   **기본 원칙:**
    *   **명확성:** 모호하지 않고 구체적인 지시.
    *   **간결성:** 불필요한 정보 제거.
    *   **역할 부여:** AI에게 특정 역할(예: 전문 번역가, 마케터) 부여.
    *   **제약 조건:** 출력 형식, 길이, 톤 등 제약 조건 명시.
    *   **예시 제공 (Few-shot learning):** 원하는 결과물의 예시를 제공하여 AI의 이해도 높이기.
*   **실습:**
    *   **다양한 프롬프트 작성:**
        *   정보 추출 프롬프트 (예: 특정 텍스트에서 날짜, 장소 추출).
        *   요약 프롬프트 (예: 뉴스 기사 3줄 요약).
        *   번역 프롬프트 (예: 한국어를 영어로 번역).
    *   **Claude API를 통한 결과 확인:** 작성한 프롬프트를 Claude API에 전송하고 결과 분석.
    *   **프롬프트 개선:** 결과에 따라 프롬프트를 수정하고 재실험하는 과정 반복.

### 4. 간단한 Q&A 봇 만들어보기

*   **Q&A 봇의 기본 구조:**
    *   사용자 입력 받기.
    *   입력을 Claude API로 전송.
    *   Claude의 응답을 사용자에게 출력.
*   **Python 코드 작성 실습:**
    *   사용자 입력을 받는 함수 (`input()`).
    *   Claude API 호출을 캡슐화하는 함수.
    *   반복문을 사용하여 지속적인 대화 가능하게 하기.
*   **코드 예시:**
    ```python
    import anthropic
    import os

    client = anthropic.Anthropic(api_key=os.environ.get("ANTHROPIC_API_KEY"))

    def ask_claude(question):
        try:
            message = client.messages.create(
                model="claude-3-opus-20240229", # 또는 claude-3-sonnet-20240229 등 사용 가능한 모델
                max_tokens=1024,
                messages=[
                    {"role": "user", "content": question}
                ]
            )
            return message.content[0].text
        except Exception as e:
            return f"API 호출 중 오류 발생: {e}"

    if __name__ == "__main__":
        print("간단한 Claude Q&A 봇입니다. 종료하려면 '종료'를 입력하세요.")
        while True:
            user_input = input("질문: ")
            if user_input.lower() == "종료":
                print("봇을 종료합니다.")
                break
            response = ask_claude(user_input)
            print(f"Claude: {response}")
    ```
*   **실행 및 테스트:** 작성한 Q&A 봇을 실행하고 다양한 질문으로 테스트.
*   **개선 방향 논의:** 대화 기록 유지, 특정 지식 기반 연동(RAG), 오류 처리 강화 등.