
# 4. Streamlit을 활용한 AI 서비스 배포

## Streamlit이란?
- 파이썬만으로 데이터 과학 및 머신러닝 웹 애플리케이션을 빠르고 쉽게 만들 수 있는 오픈소스 프레임워크입니다.
- 복잡한 프론트엔드 지식 없이도 인터랙티브한 UI를 구현할 수 있습니다.

## 왜 사용하는가?
- **신속한 프로토타이핑:** 아이디어를 몇 줄의 코드만으로 웹 서비스로 만들 수 있습니다.
- **쉬운 사용법:** 파이썬 함수처럼 위젯(버튼, 슬라이더 등)을 추가할 수 있습니다.
- **AI 모델 연동:** 만든 AI 모델이나 에이전트를 쉽게 웹으로 공유하고 테스트할 수 있습니다.

## 실습: 날씨 알려주는 웹 앱 만들기
1. Streamlit 설치: `pip install streamlit`
2. 아래 코드를 `app.py`로 저장
3. 터미널에서 `streamlit run app.py` 실행

### 예시 코드 (app.py)
```python
import streamlit as st
import requests

# 이전 실습에서 만든 함수 재사용
def get_weather(city):
    api_key = "YOUR_API_KEY"
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        return None

def summarize_weather(weather_data):
    summary = f"현재 날씨는 {weather_data['weather'][0]['description']}, 온도는 {round(weather_data['main']['temp'] - 273.15, 2)}도 입니다."
    return summary

# --- Streamlit UI ---
st.title("날씨 알려주는 AI 에이전트")

city = st.text_input("도시 이름을 영어로 입력하세요:", "Seoul")

if st.button("날씨 알려줘!"):
    weather_data = get_weather(city)
    if weather_data:
        summary = summarize_weather(weather_data)
        st.success(summary)
    else:
        st.error("도시를 찾을 수 없거나 API 오류가 발생했습니다.")
```
