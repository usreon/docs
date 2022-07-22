Annotation된 사진 파일들을 다운로드 받아 json값을 읽어 파싱한 후, 각 Key들로 컬럼을 생성하여 Value를 입력해야하는 요구사항을 받았다.

## TO BE (Json 분석)

### JSON

```ts
{
   "functionalities": [
        {
            "description": "결함조직 형성과 기능유지에 필요,",
            "functionality": {
                "id": [
                    "other"
                ]
            }
        },
        {
            "description": "철의 흡수에 필요,",
            "functionality": {
                "id": [
                    "antioxidant"
                ]
            }
        },
        ...
   ]
}
```

### Excel 예시

```ts
column : functionality
value : other/결함조직 형성과 기능유지에 필요, antioxidant/철의 흡수에 필요, ...
```

### JSON

```ts
"labels": [
    {
      "label": "recommendations",
      "description": "임신을 준비하고 있는 가임기 여성",
    },
    {
      "label": "recommendations",
      "description": "임신 중·후반기의 산모",
    },
]
```

### Excel 예시

```ts
column : recommendations
value : 임신을 준비하고 있는 가임기 여성, 관절 사용이 많은 스포츠를 즐기시는 분, ...
```

### How to

label이 recommendations일 때 description 값을 가져와 recom라는 컬럼에 ','로 구분하여 입력한다.

### JSON

```ts
"labels": [
  {
      "label": "precautions",
      "description": "유통기한을 확인하시기 바라며, 섭취량 및 섭취방법을 준수하시기 바랍니다.",
      "precaution.id": ["expired", "intake", "taking-method"],
    },
    {
      "label": "precautions",
      "description": "특이체질이거나 알러지 체질의 경우, 개인에 따라 과민반응이 나타날 수 있으므로 원료명을 확인하시고 섭취하십시오.",
      "precaution.id": ["idiosyncrasy", "allegy"],
    },
]
```

### Excel 예시

```ts
column: precautions;
value: allegy; //게 또는 새우에 알레르기가 있는 사람은 섭취시 주의하십시오.,
taking - medicine; //질병치료나 약물투여를 하시는 분들은 의사와 상의하시고 어린이 손에 닿지 않는 곳에 보관하십시오.,
kids; //질병치료나 약물투여를 하시는 분들은 의사와 상의하시고 어린이 손에 닿지 않는 곳에 보관하십시오., ...
```

### How to

label이 precautions 때 precaution.id 배열 안에 있는 값들을 앞에 쓰고 '/'로 구분하여 description 내용을 입력한다. 마찬가지로 다음 객체값을 읽을 때는 ','로 구분지어준다.

### JSON

```ts

    {
      "label": "certifications",
      "certification.id": "other",
    },
"certifications": [
   {
      "description": "",
       "certification": {
           "id": "hff"
       }
    }
],
```

### Excel 예시

```ts
column: certifications;
value: hff / other;
```

### How to

'/'로 구분

### JSON

```ts
{
      "unit": "g",
      "label": "nutrients",
      "weight": "1",
      "imgSize": { "width": 2091, "height": 27556 },
      "percent": "0",
      "description": null,
      "nutrient.name": "탄수화물",
    },
    {
      "unit": "g",
      "label": "nutrients",
      "weight": "1미만",
      "imgSize": { "width": 2091, "height": 27556 },
      "percent": "1",
      "description": null,
      "nutrient.name": "단백질",
    },
```

### Excel 예시

```ts
column : nutrient
value : 탄수화물/비율:1/단위:g/함량:2,396/단백질/비율:0/단위:g/함량:0,397/지방/비율:0/단위:g/함량:0,398/나트륨/비율:0/단위:mg/함량:0,399/글루코사민황산염/비율:0/단위:mg/함량:0,400/엠에스엠(MSM)/비율:0/단위:mg/함량:0
```

### How to

label이 nutrients일 때 `${key}: ${value}` 형식으로 입력한다. 구분은 역시 '/'.

### JSON

```ts
    {
      "unit": null,
      "label": "ingredients",
      "weight": null,
      "imgSize": { "width": 2091, "height": 27556 },
      "percent": null,
      "description": "인디안구스베리추출물분말(비타민C)",
      "nutrient.name": null,
      "precaution.id": [],
      "ingredient.name": "인디안구스베리추출물분말",
      "certification.id": null,
      "functionality.id": []
    },
    {
      "unit": null,
      "label": "ingredients",
      "weight": null,
      "imgSize": { "width": 2091, "height": 27556 },
      "percent": null,
      "description": "건조효모(나이아신)",
      "nutrient.name": null,
      "precaution.id": [],
      "ingredient.name": "건조효모",
      "certification.id": null,
      "functionality.id": []
    },
```

### Excel 예시

```ts
column : ingredients
value : 글루코사민황산염/비율:0/단위:/함량:0,3223/디에틸설폰/비율:0/단위:/함량:0,3224/결정셀룰로오스/비율:0/단위:/함량:0,3225/상어연골분말/비율:0/단위:/함량:0,3226/스테아린산/비율:0/단위:/함량:0,3227/녹색잎홍합추출분말/비율:0/단위:/함량:0,3228/생선콜라겐/비율:0/단위:/함량:0,3229/스테아린산마그네슘/비율:0/단위:/함량:0,3230/이산화규소/비율:0/단위:/함량:0,3231/게/비율:0/단위:/함량:0,3232/새우/비율:0/단위:/함량:0,3233/조개류/비율:0/단위:/함량:0
```

### How to

nutrients과 같음

### 로직

객체 값을 읽어 키가 labels이면 배열을 돌아 위의 Json값 처리를 해주고, 나머지는 ...rest로 받아 key 이름으로 컬럼 생성, value로 값을 입력해준다. depth가 없는 경우, Value가 객체로 이루어졌다면 그 객체를 돌면서 key를 생성하지 않고 '/'로 구분해준다.

## 정리

1. 서브태스크를 참조해 파일들을 하나씩 돌아서 Json 값을 가져온다.
2. Json을 파싱한 후 다음 iteration(다음 파일)로 넘어가기 전 column에 row를 추가한다.
3. 파일을 다 돌고 나면(iteration이 끝나면) 엑셀 파일을 쓴다.

## 결과물
