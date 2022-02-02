## 자동차 함수 만들어보기
```python
ferrai = {
    "name": "Ferrai",
    "color": "Rosso Corsa",
    "fuel": 50,
    "mileage": 0
}

lamborghini = {
    "name": "Lamborghini",
    "color": "Giallo Spica",
    "fuel": 40,
    "mileage": 0
}

# 액셀 함수 정의
def ferrai_accel():
    ferrai['fuel'] -= 2
    ferrai['mileage'] += 10


def lamborghini_accel():
    lamborghini['fuel'] -= 3
    lamborghini['mileage'] += 12


# 액셀 밟기 전 상태 확인
print(ferrai)
print(lamborghini)

# 액셀 밟기
ferrai_accel()
lamborghini_accel()

# 액셀 밟은 후 상태 확인
print(ferrai)
print(lamborghini)

>>>
{'name': 'Ferrai', 'color': 'Rosso Corsa', 'fuel': 50, 'mileage': 0}
{'name': 'Lamborghini', 'color': 'Giallo Spica', 'fuel': 40, 'mileage': 0}
{'name': 'Ferrai', 'color': 'Rosso Corsa', 'fuel': 48, 'mileage': 10}
{'name': 'Lamborghini', 'color': 'Giallo Spica', 'fuel': 37, 'mileage': 12}
```

- 위와 같이 자동차를 딕셔너리로 정의하고 액셀에 대한 함수를 정의하는 코드가 있다고 생각해보자. 액셀을 밟으면 딕셔너리에 접근하여 연료가 줄어들고, 주행거리가 늘어나게 설정했다. 그리고 액셀을 밟기 전 상태를 확인하고 액셀 함수 호출 후, 다시 상태를 확인해보면, fuel과 mileage가 변한것을 알 수 있다.  
- 
