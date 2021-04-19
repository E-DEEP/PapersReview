# Everybody Dance Now
작성자: 한지수  
#MotionTransfer #videotovideotranslation #GAN 

Dataset: a variety of videos, enabling trained amateurs to spin and twirl like ballerinas, perform martial arts kicks, or dance as vibrantly as pop stars
Transfer motion between two video subjects in a frame-by-frame manner > mapping between images of two inidividuals > discover an image-to-image translation between source and target set
“Do as I do” motion transfer: transfer performance to a novel target < Video-to-video translation using pose as an intermediate representation
1. Extract pose from [the source subject]
2. Apply the learned pose-to-appearance mapping to generate [the target subject]
Predict two consecutive frames for temporally coherent video results / a separate pipeline for realistic face synthesis

## Related Works
#### Extract motion
Problem: unlikely to have an exact frame(due to body shape, motion style unique to each other)
Solution: key point-based pose > use Pose stick figures by OpenPose, DensePose
Input: pose stick figures from the source from the trained model 

#### Image-to-image translation
Disentangle motion from appearance and Synthesize video 
- MoCoGAN: unsupervised adversarial training to learn this separation and generates videos of subjects performing novel motions or facial expression
    - Dynamics Transfer GAN 부문 (facial expressions from a source image > a video onto a target person )
- 그 외 models
    - Pix2pix, CoGAN, UNIT, CycleGAN, DiscoGAN, Cascaded Refinement Networks, pix2pixHD

## Method
### Pose Detection
Pretrained state-of-the-art detector to create pose stick figures (Related Works - Extract motion)

### Pose Encoding and Normalization
Accounts for differences between the source and target body shapes and locations within the frame
1. Encoding body poses
Use pre-trained pose detection

2. Global pose normalization
Transform the pose key points of the source person so that they appear in accordance with the target person’s body shape and location as in the Transfer section
: by analyzing the heights and ankle positions for the poses of each subject and use a linear mapping btw the closest and farthest ankle positions

### Pose to Video Translation
1. Simple GAN Model (Create video sequence)
: predict two consecutive frames, by enforcing temporal coherence  
    - training  
        간단히 말해서 fake G(x_t)와 real y_t를 구별할 수 있도록 coherence 학습된 Pose detector P 얻기
        <img src="https://chart.apis.google.com/chart?cht=tx&chl=L_%7Bsmooth%7D%20(G%2CD)%3D%5Cmathbb%7BE%7D_%7B(x%2Cy)%7D%20%5Blog%20D(x_t%2C%20x_%7Bt%2B1%7D%2C%20y_t%2C%20y_%7Bt%2B1%7D)%5D%2BE_x%5Blog(1-D(x_t%2C%20x_%7Bt%2B1%7D%2C%20G(x_t)%2C%20G(x_%7Bt%2B1%7D))%5D">
    - transfer  
        일반 image가 들어오면 이를 동일하게 적용
2. FaceGAN  
: to add more detail and realism to the face region  
얼굴 면적(x_F)만을 취하여 GAN 모델에 적용하는 방식, pix2pix 방식을 적용하였다.
<img src="https://chart.apis.google.com/chart?cht=tx&chl=L_%7Bface%7D%20(G_f%2CD_f)%3D%5Cmathbb%7BE%7D_%7B(x_F%20%2Cy_F)%7D%20%5Blog%20D_f(x_F%2C%20y_F)%5D%2BE_x_%7BF%7D%5Blog(1-D(x_F%2C%20G(x)_F%2Br)%5D">