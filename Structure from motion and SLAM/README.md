# Geometric Calibration

![image](https://user-images.githubusercontent.com/81455416/146943624-05c075d6-f965-41c8-9cb4-5282a531e417.png)

- **x** : image coordinates
- **K** : intrinsic matrix : 3D position을 2D position으로 변환
- **[R t]** : extrinsic matrix : 각각 rotation, translation matrix. 카메라 포즈를 의미
- **X** : world coordinates



## Intrinsic matrix **K**

1. Pinhole camera model : 초점거리 f에 따른 3D↔2D의 관계
    
    ![image](https://user-images.githubusercontent.com/81455416/146943669-c76dc96a-2460-424f-a223-499ca95be3b6.png)
    
2. Principal point offset : image plane의 좌측 하단을 원점 취급하기 위해 좌표차를 고려
    
    ![image](https://user-images.githubusercontent.com/81455416/146943708-1302222e-d7b4-408d-9975-45670eecb867.png)
    
3. CCD coordinates : mm단위를 pixel단위로 변환 및 pixel skewness를 고려
    
    ![image](https://user-images.githubusercontent.com/81455416/146943729-dff8e1f7-4db4-48db-a1c3-9e2568070dd0.png)
    



## DLT (Direct Linear Transform)

![image](https://user-images.githubusercontent.com/81455416/146943444-38122d38-5c0b-43a0-968c-b80815279894.png)

- **Given**
    
    ~150개의 흰 점들의 3차원 좌표와 그에 대응되는 2차원 좌표점을 알고있다.
    
    ![image](https://user-images.githubusercontent.com/81455416/146943512-f6dd0057-0694-43b8-a295-6e63115e0324.png)
    

- **Goal**
    
    3D world coordinates를 통해 2D coordinates를 추정하는 matrix **P**를 추정
    
    ![https://latex.codecogs.com/svg.image?\bold{x}&space;=&space;\bold{P}\bold{X}](https://latex.codecogs.com/svg.image?\bold{x}&space;=&space;\bold{P}\bold{X})
    

- **Process**
    
    1. C matrix 계산
    
       ![image](https://user-images.githubusercontent.com/81455416/146943787-d36605e1-77f8-47d3-9a62-661c71a8adb8.png)
       
       n개의 대응점에 대해 다음과 같이 방정식 작성 가능하다.
       
       ![image](https://user-images.githubusercontent.com/81455416/146943901-0c9fec44-d711-4a1b-82cb-abd25a5afe77.png)
       
       다음과 같이 각각을 matrix A, C, 0으로 볼 수 있다.
       
       ![image](https://user-images.githubusercontent.com/81455416/146944114-d84747ed-e620-43e5-a0b3-a1633e34b07e.png)
       
       이렇게 구한 homogeneous equation은 linear algebra method (e.g., SVD)를 이용하여 계산할 수 있다.
    
    2. C=K［R t］로 분해
       
       ![image](https://user-images.githubusercontent.com/81455416/146944344-92430daf-72ee-47ba-83ed-b015f149b5c7.png)
       
       이때 K와 R matrix는 다음의 조건을 만족해야 한다.
      
       > K : upper triangular matrix <br>
       > R : orthogonal matrix

       따라서 P에 대해 ***RQ factorization***(decomposition)을 이용하여 K, R로 분해할 수 있다.
       
       혹은 환경적으로 K가 고정되어있는 경우(i.e., calibrated already)에는 pose는  ***PnP algorithms*** (Projective-n-point) (n≥3)을 이용하여 계산할 수 있다.
       
       혹은 EXIF(e.g., JPEG의 meta informatino)나 sensor spec을 이용하여 초점거리 f와 sensor pixel size 등을 사용하여 미지수의 개수를 줄일 수도 있다.
