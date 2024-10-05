---
title: "CV(1)-image filtering"
excerpt: "컴퓨터비전 이미지 필터링 기법 설명"

categories:
  - CV
tags:
  - [tag1, tag2]

permalink: /CV/CV(1)-image-filtering/

toc: true
toc_sticky: true

date: 2024-10-05
last_modified_at: 2024-10-05
---

## 🦥 본문

컴퓨터 비전에서, 우리는 2D 이미지에 대해서 여러 가지 처리를 한다. 그중 가장 기본적인, image filtering에 대해서 알아 보자. 

기본적으로 이미지 처리를 위해서는, `sampling, quantize` 과정을 거쳐야 한다. 

`sampling` 은, 원본 이미지를 픽셀 값을 가지는 n x n 크기의 2D matrix로 표현하는 것

`quantize` 는 

![스크린샷 2024-09-18 오후 5.52.02.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9453daf9-ef32-472e-b3a9-a0cb0fc4fd2c/20f3dc56-dc32-4ec5-a537-04b632e2c4e1/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-09-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.52.02.png)


