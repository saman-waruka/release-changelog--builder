# ทำ chage log สวยๆ เมื่อมีการ push tag ด้วย Release Changelog Builder 
(https://github.com/marketplace/actions/release-changelog-builder)

## 1. เตรียม label ที่เราจะใช้ generate change log (แล้วจะนำไปใช้ต่อในการ config Release Changelog Builder) 
ในที่นี้จะสร้างเป็น    
- feature
- fix 
- dependencies

ตอนที่เรา PR ก็ให้ใส่ label ไว้ด้วย ตอน push tag Release ก็จะมีสรุปให้เราด้วยว่า release นี้ มี feature อะไร fix อะไร  ที่ PR ไหน 
[ดูตัวอย่างที่นี่](https://github.com/mikepenz/release-changelog-builder-action/releases/tag/v0.9.0)
<br /><br />

## 2. สร้างไฟล์ config-release-changelog-builder.json ( ที่จริงชื่ออะไรก็ได้ ) ในโฟลเดอร์ .github

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
      }
  ]
}
```
<br /><br />

## 3. สร้างไฟล์ release-changelog-builder.yml ( ที่จริงชื่ออะไรก็ได้ ) ในโฟลเดอร์  .github/workflow
```
name: 'CI Release Changelog Builder'
on:
  push:
    tags:
      - '*'

jobs:
  release:
    if: startsWith(github.ref, 'refs/tags/')
    runs-on: ubuntu-latest
    steps:
      - name: Build Changelog
        id: github_release
        uses: mikepenz/release-changelog-builder-action@v1
        with:
          configuration: ".github/config-release-changelog-builder.json"
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
<br /><br />
# [ดูตัวอย่างที่นี่](https://github.com/mikepenz/release-changelog-builder-action/releases/tag/v0.9.0)
^_^ ^o^
<br /><br />
ปล. ถ้าเจอ error หน้าตาแบบนี้ไ่ต้องตกใจ จะเป็นเฉพาะ push tag แรก 
หลังจาก tag ที่ 2 ไป ก็จะไม่มี error นี้ 
<br /><br />
![alt text](https://res.cloudinary.com/dxqn5gftg/image/upload/v1617276036/Screen_Shot_2564-04-01_at_18.17.48_svelo8.png)