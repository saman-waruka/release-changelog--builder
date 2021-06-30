# ทำ Release chage log สวย ๆ เมื่อมีการ push tag ด้วย [Release Changelog Builder](https://github.com/marketplace/actions/release-changelog-builder) 


## 1. เตรียม label ที่เราจะใช้สำหรับสร้าง change log (สร้างแล้วจะนำไปใช้ต่อในการ config ของ Release Changelog Builder หรือ จะใช้ label ที่มีอยู่แล้วก็ได้ แล้ว config ตาม) 
ในที่นี้จะสร้างเป็น    
- feature
   - สี  : ![#0052CC](https://via.placeholder.com/15/0052CC/000000?text=+) `#0052CC` 
   - คำอธิบาย : ไว้บอกว่ามีฟีเจอร์อะไรใหม่ (ใน changelog จะดึงตรงนี้ไปแสดง)
- fix 
   - สี  : ![#FBCA04](https://via.placeholder.com/15/FBCA04/000000?text=+) `#FBCA04` 
   - คำอธิบาย :  ไว้บอกว่าแก้ issue อะไร (ใน changelog จะดึงตรงนี้ไปแสดง)
- dependencies
   - สี  : ![#D4C5F9](https://via.placeholder.com/15/D4C5F9/000000?text=+) `#D4C5F9` 
   - คำอธิบาย :  ไว้บอกว่ามีการอัพเดต Dependecies (ใน changelog จะดึงตรงนี้ไปแสดง)
- refactor-code
   - สี  : ![#C2E0C6](https://via.placeholder.com/15/C2E0C6/000000?text=+) `#C2E0C6` 
   - คำอธิบาย :  ไว้บอกว่ามีการ Refactor code (ใน changelog จะดึงตรงนี้ไปแสดง)
- Hot fix
   - สี  : ![#B60205](https://via.placeholder.com/15/B60205/000000?text=+) `#B60205` 
   - คำอธิบาย :  ไว้บอกว่ามีการ Hot fix (ใน changelog จะดึงตรงนี้ไปแสดง)
- security
   - สี  : ![#ee0701](https://via.placeholder.com/15/ee0701/000000?text=+) `#ee0701` 
   - คำอธิบาย :  ไว้บอกว่ามีการเปลี่ยนแปลงในเรื่อง security  (ใน changelog จะดึงตรงนี้ไปแสดง)

#### ปล. Add Label โดยการไปที่ Repository  > เลือก Pull Requests  > คลิกที่ Label <br /><br />


### ตอนที่เรา Pull Request ก็ให้ใส่ label ไว้ด้วย ตอน push tag Release ก็จะมีสรุปให้เราด้วยว่า release นี้ มี feature อะไร fix อะไร  ที่ Pull Request ไหน 
<br />

## [ดูตัวอย่างที่นี่ PR](https://github.com/saman-waruka/release-changelog--builder/pull/18)
<br /><br />

## 2. สร้างไฟล์ config-release-builder.json ( ที่จริงชื่ออะไรก็ได้  แต่ต้องไปใส่ชื่อให้ตรงกัน ในไฟล์ .yml ) ในโฟลเดอร์ของโปรเจค 
ไฟล์นี้จะเป็น config ว่า PR นั้น ๆ จะจัดอยู่ในกลุ่มไหน ดูจาก label อะไร
```
{
  "categories": [
      {
          "title": "## 🚀 Features",
          "labels": ["feature"]
      },
      {
          "title": "## 🐛 Fixes",
          "labels": ["fix"]
      },
      {
          "title": "## 📦 Dependencies",
          "labels": ["dependencies"]
      },
      {
          "title": "## 🔄 Refactor Code",
          "labels": ["refactor-code"]
      },
      {
          "title": "## 🔧🔨 Hot fix",
          "labels": ["Hotfix"]
      },
      {
          "title": "## 🔒 Security",
          "labels": ["security"]
      }
  ]
}
```

<br>

## <span style="color:#ee0701">คำเตือน !!!</span>
เพื่อให้การ build change log ทำงานได้อย่างถูกต้อง โปรดตรวจสอบ Label ใน Repo กับ ใน config-release-builder.json ให้ตรงกันด้วยนะจ๊ะ

<br /><br />

## 3. สร้างไฟล์ release-changelog-builder.yml ( ที่จริงชื่ออะไรก็ได้ ) ในโฟลเดอร์  .github/workflow

นำ ชื่อไฟล์จากข้อ 2  ไปใส่ตรง  with configuration ของ  mikepenz/release-changelog-builder-action@v1
หน้าตาก็จะออกมาประมาณนี้
```
name: 'CI Release Changelog Builder'
on:
  push:
    tags:
      - '*'
jobs:
  release:
    if: startsWith(github.ref, 'refs/tags/')
    # runs-on: [self-hosted, linux]
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Build Changelog
        id: github_release
        uses: mikepenz/release-changelog-builder-action@v1
        with:
          configuration: "config-release-builder.json"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      - name: Create Release
        uses: actions/create-release@v1
        with:
          tag_name: ${{ github.ref }}
          release_name: ${{ github.ref }}
          body: ${{steps.github_release.outputs.changelog}}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
<br/>

## 4. โครงสร้างโปรเจคจะออกมาแบบนี้
```
root
  |__  .github
  |        |__ workflow
  |              |__ release-changelog-builder.yml
  |
  |__ config-release-builder.json
```

<br /><br />
## [ดูตัวอย่าง Official ที่นี่](https://github.com/mikepenz/release-changelog-builder-action/releases)
## [ดูตัวอย่าง ของ Repository นี้](https://github.com/saman-waruka/release-changelog--builder/releases)
<br />

เรียบร้อย ปรบมืสิรอไร  👏 👏 👏 👏 👏

 😃 😃 😃 😃

<br />

ปล. emoji อื่นๆ [ดูได้ที่นี่](https://gist.github.com/rxaviers/7360908)

<br />

ปล2. ถ้าเจอ error หน้าตาแบบนี้ไ่ต้องตกใจ จะเป็นเฉพาะ push tag แรก 
หลังจาก tag ที่ 2 ไป ก็จะไม่มี error นี้ 
<br /><br />
![alt text](https://res.cloudinary.com/dxqn5gftg/image/upload/v1617276036/Screen_Shot_2564-04-01_at_18.17.48_svelo8.png)
