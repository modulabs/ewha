
# 3. 바이브 코딩과 AI 에이전트 구축

## 바이브 코딩(Vibe Coding)이란?
- "이런 느낌으로 만들어줘" 와 같이, 자연어를 통해 AI에게 코드 생성을 요청하고, 생성된 코드를 조립하여 빠르게 프로토타입을 만드는 개발 방식입니다.
- 기존 코딩: 모든 로직을 직접 작성
- 바이브 코딩: 아이디어와 흐름에 집중, 세부 구현은 AI에게 위임

## AI 에이전트 구축 실습
- 목표: "오늘 날씨를 알려주는 AI 에이전트" 만들기
- 단계:
  1. 필요한 기능 정의 (날씨 정보 가져오기, 사용자에게 정보 요약)
  2. 각 기능을 수행하는 파이썬 코드 생성 (AI에게 요청)
  3. 생성된 코드들을 하나의 스크립트로 결합

### 예시 코드 (Python)
```python
import requests

def get_weather(city):
    # 이 부분의 코드는 AI에게 요청하여 생성합니다.
    # "requests 라이브러리를 사용해서 openweathermap API로 서울 날씨를 가져오는 코드 만들어줘"
    api_key = "YOUR_API_KEY"
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}"
    response = requests.get(url)
    return response.json()

def summarize_weather(weather_data):
    # "날씨 json 데이터를 받아서 '서울의 현재 날씨는 [날씨], 온도는 [온도]도 입니다.' 형태로 요약해줘"
    summary = f"서울의 현재 날씨는 {weather_data['weather'][0]['description']}, 온도는 {round(weather_data['main']['temp'] - 273.15, 2)}도 입니다."
    return summary

# 에이전트 실행
city = "Seoul"
weather_data = get_weather(city)
summary = summarize_weather(weather_data)
print(summary)
```
