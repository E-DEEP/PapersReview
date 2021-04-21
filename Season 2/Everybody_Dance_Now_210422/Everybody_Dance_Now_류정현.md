# Everybody Dance Now

##### video-to-video translation (pose-to-appearance mapping)
##### 1) predict two consecutive frames for temporally coherent video results
##### 2) introduce a separate pipeline for realistic face synthesis

##### * the target person never performed the same exact sequence of motions as the source
##### - target : whose appearance we wish to synthesize
##### - source : whose motion we wish to impose onto target person

##### frame-by-frame manner (i.e. discover an image-to-image translation between the source&target)
##### use "pose stick figures"


## 3. METHOD
### 1) pose detection
##### use pre-trained pose detector P (estimates 2D x,y) and create pose stick figure
### 2) global pose normalization
##### need to transform keyporints of the source person so that they appear in accordance wuth the target person's bosy shape and location
##### find trasfromation by analyzing heights and ankle positions
### 3) mapping from normalized pose stick fuigures to the target subject
##### temporal smoothing : Lsmooth(G, D) = E(x,y)[log D(xt, xt+1, yt, yt+1)]+Ex[log(1 − D(xt, xt+1, G(xt), G(xt+1))] 
##### face GAN : Lface(Gf , Df ) = E(xF ,yF )[log Df (xF , yF )]+ExF[log1 − Df (xF , G(x)F + r)]
##### 
##### full : minG((maxDiPkiLsmooth(G, Dk)) + λFM PkiLFM(G, Dk)+λP (LP (G(xt−1), yt−1) + LP (G(xt), yt)))
