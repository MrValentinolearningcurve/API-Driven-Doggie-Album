# API-Driven-Doggie-Album
Loading and Storing Canine Captures

Github: https://github.com/MrValentinolearningcurve/API-Driven-Doggie-Album

สร้างโปรแกรมในการดึงข้อมูลจากที่หนึ่ง และนำไปไว้อีกที่หนึ่ง ซึ่งในเวิร์คชอปนี้จะสมมุติว่าเราเป็นคนที่ชื่นชอบสุนัข และอยากที่จะเก็บลิ้งค์รูปของสุนัขต่าง ๆ ไว้ เพื่อเอาไปเปิดดูเล่นเพื่อคลายเครียด หรือเพื่อใช้ทำวิจัยเกี่ยวกับการวิเคราะห์รูปสุนัขในอนาคต 

- รูปสุนัข - เราจะดึงมาจาก Dog API จากเว็บ https://dog.ceo/dog-api/

- ที่เก็บลิ้งรูป - เราจะเก็บไว้ที่ Pantry - Free JSON Storage API ที่ https://getpantry.cloud/

ส่วนแรกเราจะดึงข้อมูลลิ้งค์รูปจาก Dog API ก่อน โดยจะเขียนโปรแกรมโดยใช้ภาษา Python เริ่มจากการสร้าง Python virtual environment และจัดการ dependencies ต่าง ๆ โดยใช้ poetry เป็นตัวช่วย

วิธีติดตั้ง poetry

https://python-poetry.org/

    curl -sSL https://install.python-poetry.org | python3 - ## เอาจาก website เป็นการเชื่อมเพื่อติดตั้ง poetry

เช็คว่ามี file pyproject.toml

    -- bash command
    poetry init

ตอนนี้เราก็ได้ Python virtual environment มาแล้ว เนื่องจากว่าการดึงข้อมูลรูปสุนัข เราจะดึงผ่าน API ทีนี้ในภาษา Python ก็จะมี package หรือ module ที่ชื่อว่า requests ที่เราต้องติดตั้งเพิ่มเติมก่อนถึงจะใช้งานได้ โดยเราจะติดตั้งโดยใช้คำสั่ง

    --  bash command
    poetry add requests

พอเราติดตั้ง package เสร็จ เราก็จะเริ่มเขียนโค้ดกัน

    -- file name extract.py

    import json

    import requests

    if __name__ == "__main__":
    url = "https://dog.ceo/api/breeds/image/random"
    response = requests.get(url)
    data = response.json()
    print(data)

    try:
        with open("dogs.json", "r") as f:
            existing_data = json.load(f)
    except FileNotFoundError:
        existing_data = {
            "data": []
        }

    existing_data["data"].append(data)

    with open("dogs.json", "w") as f:
        json.dump(existing_data, f)
		   

1.สิ่งที่โค้ดด้านบนนี้ทำคือ เราจะส่ง request เข้าไปที่ Dog API ที่ URL https://dog.ceo/api/breeds/image/random ซึ่งเราจะได้ response ที่อยู่ในรูปแบบ JSON กลับมา

2.หลังจากนั้นเราก็เอาข้อมูลใน response นั้นเอามาเก็บใส่ไว้ใน Python list โดยก่อนที่จะเก็บเข้า list ไปนั้นเราจะมีการอ่านไฟล์ dogs.json ขึ้นมาก่อนเพื่อโหลดข้อมูลที่เราเคยดึงไว้มาเก็บไว้ใน list แล้วค่อยเพิ่มข้อมูลใหม่เข้าไปใน list นั้น

3.แล้วจึงเอาข้อมูลทั้งหมดเขียนทับลงไฟล์ dogs.json อีกที ตรงนี้เราทำเพื่อให้เรายังมีข้อมูลที่เราเคยดึงไว้อยู่

4.หลังจากที่เราเก็บข้อมูลมาแล้ว เราจะอัพโหลดขึ้นไปไว้บน Pantry (ซึ่งก่อนที่จะใช้งาน Pantry จะต้องลงทะเบียนก่อน เพื่อให้ได้ Pantry ID มา) โดยการเขียน Bash script ไปเรียกโปรแกรม curl เพื่ออัพโหลดข้อมูล

ที่ไฟล์ load.sh เราจะเขียนประมาณนี้ ตรง <ID> ให้เราใส่ Pantry ID ของเราแทนลงไป
#!/bin/bash
```
curl -XPOST \
  -H "Content-type: application/json" \
  -d @dogs.json "https://getpantry.cloud/apiv1/pantry/<ID>/basket/randomDogs"
```
เท่านี้เราก็สามารถอัพโหลดข้อมูลรายการลิ้งค์รูปสุนัขของเราขึ้นไปบน Pantry ได้แล้ว
