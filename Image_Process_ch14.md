## Chapter 14. Image Mosaic/panorama

정의 : Stitch multi-images into a seamless composite

SIFT & SURF의 응용

접점 부분에 Blending 기술(=stitching) 적용 

![](http://deliveryimages.acm.org/10.1145/2210000/2206309/pulli8.png)

### 2D 수행 절차 
1. Take image sequence from the same scene
	* Global alignment of the images spatially
2. Warp the image pair
	* Compute 2D/3D projective transformation of images
	* Reprojection onto mosaic surface
3. Local Alignment
	* Move warped images to overlap exactly
4. Stitching(Optional) : 경계선 찾기
	* Find boundary between overlapped images
	* 길찾기 문제와 비슷 
5. Composition of the warped images
	* Blend the two together to create a mosaic

### 원통형 Panorama (=Cylindrical Mosaic)
![](http://pages.cs.wisc.edu/~vmathew/cs766-proj2/images/final_set4.jpg)

### 구형 Spherical Mosaic
![](http://www.cs.columbia.edu/CAVE/projects/sp_mos/pics/spherical_mosaic.jpg)


